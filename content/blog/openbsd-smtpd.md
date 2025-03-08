---
title: "OpenBSD Server: smtpd"
date: 2025-03-07
draft: false
---

At last, the final part to this series. You can catch up on parts 1 and 2
[here](/blog/openbsd-server-install) and [here](/blog/openbsd-httpd) respectively.
Setting up an email server is no easy task, it takes a lot of configuration and
sometimes trial and error. This guide should be good enough for small email
servers that don't get too many emails.
So let's go ahead and finally setup an email server for our system.

## Some Needed Packages

First, we need to install some extra packages for our email server to work the
way we want it to. We will need `rspamd` to filter out spam, `dovecot` in order
to use IMAP and or POP3 to view our emails, and we will need
`dovecot-pigeonhole` to help sort our emails:

``` sh
doas pkg_add rspamd-- dovecot-- dovecot-pigeonhole-- opensmtpd-filter-rspamd
```

## DNS Records

In order for our email server to properly work and also to not get filtered or
marked as spam, we will need to add some DNS records.

### MX Record

A MX record is required to tell other SMTP servers where to deliver emails
sent to our address. For our MX record we can have our emails redirected to
`mail.example.com`[^1]. When creating an MX record, this is what it should look
like:

```
Record Type     Host        Value        MX Priority    TTL
     V            V           V                V         V
    MX            @    mail.example.com        0        3600
```

Our record type should (obviously) be MX, our host should be empty (which is
sometimes represented as an `@` symbol), our value is where our mail should go
which is `mail.example.com`, our MX priority should just be 0 for now, and our
TTL is set to 3600 seconds (or 1 hour).

For reference, this is how my MX record is setup for `brycevandegrift.xyz`:

{{< img src="/p/mx-record.webp" alt="My personal MX record" link="/p/mx-record.webp" >}}

### SPF Record

Our SPF record is vital in order to detect spam. It defines what servers can
send emails from a domain. In our case, only `mail.example.com` will be sending
emails on our behalf, so we can set our SPF record accordingly:

```
Record Type     Host        Value        TTL
     V            V           V           V
    TXT           @     v=spf1 mx all    3600
```

Our SPF record is just a normal TXT record that has the string "v=spf1 mx all"
in it which allows emails to be sent in reference to our MX record.

### DKIM Record

We need a DKIM record in order to cryptographically sign and verify the
emails we send. Without DKIM your emails will **NOT** be accepted by most email
servers and will be marked as spam. In order to use DKIM we will need to
generate our private and public keys on our system. We can generate our keys and
give them the correct permissions like so:

``` sh
# First enter root session
su

umask 077
install -d -o root -g wheel -m 755 /etc/mail/dkim
install -d -o root -g _rspamd -m 775 /etc/mail/dkim/private
openssl genrsa -out /etc/mail/dkim/private/example.com.key 2048
openssl rsa -in /etc/mail/dkim/private/example.com.key -pubout -out /etc/mail/dkim/example.com.pub
chgrp _rspamd /etc/mail/dkim/private/example.com.key /etc/mail/dkim/private/
chmod 440 /etc/mail/dkim/private/example.com.key
chmod 775 /etc/mail/dkim/private/

# Exit root session
exit
```

Now that we successfully generated our private and public DKIM keys, we need to
put our public DKIM key into our DKIM record. We can get our DKIM public key as
a single line by running this `awk` command:

``` sh
doas awk '/PUBLIC/ { $0="" } { printf ("%s",$0) } END { print }' /etc/mail/dkim/example.com.pub
```

We can now copy that string and put it into our DKIM record.

```
Record Type        Host                    Value                  TTL
     V               V                       V                     V
    TXT      dkim._domainkey   v=DKIM1;k=rsa;p=<YOUR DKIM KEY>    3600
```

Just replace `<YOUR DKIM KEY>` with the string you just copied and your DKIM
record is good to go.

### DMARC Record

We now need to add a DMARC record to our DNS records. DMARC is just another way
of preventing email spoofing and spam by telling receiving email servers what to
do with potentially invalid emails. Here is a good default to use for you DMARC
record:

```
Record Type        Host                                Value                                  TTL
     V               V                                   V                                     V
    TXT              @     v=DMARC1;p=reject;rua=mailto:dmarc@example.com;sp=reject;aspf=r;   3600
```

Wow, the value for this record is quite long, let's break down what it means.
`p=reject` and `sp=reject` just means to immediately discard any invalid emails,
`aspf=r` means that *any* email sent from *any* subdomain of example.com is
valid, and `rua=dmarc@example.com` tells where the mail server receives DMARC
reports (don't worry, you shouldn't care about DMARC reports, they are mostly
useless).

### PTR Record

Finally, our last record! All you need to do is set a PTR record (also called a
Reverse DNS pointer) that points to
`mail.example.com`. That's it. This is yet **another** mechanism to prevent
spam. Please note that there are different ways to set a PTR record, most ways
involve finding a "Reverse DNS" option for your VPS/hosting provider and using
that.

## Email Configuration

At last, we can now configure the setting for our email server. Our first order
of business is to get a valid certificate for our `mail.example.com` subdomain.
This process should be somewhat familiar if you read my last article. Let's
first append this to the end of our `/etc/acme-client.conf`:

``` lighty
domain mail.example.com {
	domain key "/etc/ssl/private/mail.example.com.key"
	domain full chain certificate "/etc/ssl/mail.example.com.fullchain.pem"
	sign with letsencrypt
}
```

Now we just need to add this to `/etc/httpd.conf` in order to get a certificate
for *any* subdomain of example.com:

``` lighty
server "*" {
	listen on * port 80
	
	location "/.well-known/acme-challenge/*" {
		root "/acme"
		request strip 2
	}
	
	location * {
		block return 302 "https://$HTTP_HOST$REQUEST_URI"
	}
}
```

Now we just run this command to generate a certificate for `mail.example.com`:

``` sh
doas acme-client -v mail.example.com
```

Finally, we shouldn't forget to automatically renew our certificates, so change
`/etc/weekly.local` as follows:

``` sh
# Check for new certificates every week
acme-client example.com
acme-client mail.example.com

rcctl restart httpd smtpd dovecot
```

### Spam

The first program that we should configure is `rspamd`. Only a few things need
to be set in order for it to work properly. Go ahead and create a file at
`/etc/rspamd/local.d/dkim_signing.conf` and add this to it:

``` lighty
# This should always be true
allow_username_mismatch = true;

# Allow rspamd to sign with our DKIM key
domain {
	example.com {
		path = "/etc/mail/dkim/private/example.com.key";
		selector = "dkim";
	}
}
```

This is entirely optional, but if you want more performance you can enable
and start `redis` as a cache backend:

``` sh
doas rcctl enable redis
doas rcctl start redis
```

Now enable and start `rspamd`:

``` sh
doas rcctl enable rspamd
doas rcctl start rspamd
```

### SMTP

Now let's configure OpenSMTPD in order to send and receive emails. We will need
to create a config file at `/etc/mail/smtpd.conf` and configure it like so:

``` lighty
# Set locations to certificates
pki example.com cert "/etc/ssl/mail.example.com.fullchain.pem"
pki example.com key "/etc/ssl/private/mail.example.com.key"
pki example.com dhe auto

# Delimiter for email addresses
smtp sub-addr-delim '_'

# Sets rspamd as our filter
filter rspamd proc-exec "filter-rspamd"

# Sets ports to use for mail transfer
listen on all port 25 tls pki "example.com" filter "rspamd"
listen on all port 465 smtps pki "example.com" auth mask-src filter "rspamd"
listen on all port 587 tls-require pki "example.com" auth mask-src filter "rspamd"

# Load mail aliases
table aliases file:/etc/mail/aliases

# Actions for mail delievery
action "local" lmtp "/var/dovecot/lmtp" alias <aliases>
action "outbound" relay

# Actions for recieving mail
match from any for domain "example.com" action "local"
match from local for local action "local"

# Actions for sending mail
match from any auth for any action "outbound"
match from local for any action "outbound"
```

Now we need to place our mail delievery address in `/etc/mail/mailname`:

``` lighty
mail.example.com
```

And now we can test our `smtpd` config by running:

``` sh
doas smtpd -n -f /etc/mail/smtpd.conf
```

If everything is all good then you can restart `smtpd`:

``` sh
doas rcctl restart smtpd
```

### IMAP/POP3

The last part of our email server is `dovecot` which allows us to view our
emails using IMAP and or POP3. In order to use our certificates we will need to
configure `/etc/dovecot/conf.d/10-ssl.conf` like so:

``` lighty
ssl_cert = </etc/ssl/mail.example.com.fullchain.pem
ssl_key = </etc/ssl/private/mail.example.com.key
ssl_dh = </etc/dovecot/dh.pem
```

We need to generate a Diffie-Hellman key in order to make each TLS connection
unique (these commands might take a *while* to generate a key):

``` sh
doas openssl dhparam -out /etc/dovecot/dh.pem 4096
doas chown _dovecot:_dovecot /etc/dovecot/dh.pem
doas chmod 400 /etc/dovecot/dh.pem
```

Now we need to configure our mailbox and some basic settings for `dovecot`. Edit
`/etc/dovecot/conf.d/10-mail.conf` so it resembles the following:

``` lighty
# Where to put user maildir
mail_location = maildir:~/Maildir

namespace inbox {
	inbox = yes
}

mmap_disable = yes
first_valid_uid = 1000
mail_plugin_dir = /usr/local/lib/dovecot

protocol !indexer-worker {
}

mbox_write_locks = fcntl
```

Let's edit `/etc/dovecot/conf.d/20-lmtp.conf` in order to allow `sieve` and mail
plugins:

``` lighty
protocol lmtp {
	mail_plugins = $mail_plugins sieve
}
```

We now meed to modify `/etc/dovecot/conf.d/15-mailboxes.conf` and add this
inside the `namespace inbox {` section of the file in order to create a Spam
folder:

``` lighty
mailbox Spam {
	auto = create
	special_use = \Junk
}
```

If you don't plan on using IMAP and only wish to use POP3 for viewing/sending
emails then this last step is entirely optional, however I encourage users to
use IMAP instead of POP3. To enable IMAP we just need to edit
`/etc/dovecot/conf.d/20-imap.conf` like so:

``` lighty
protocol imap {
	mail_plugins = $mail_plugins imap_sieve
	mail_max_userip_connections = 25
}
```

#### Sieve and Filtering

Finally, the last part of setting up `dovecot` is configuring `sieve` in order
to filter and group emails properly. Go ahead and edit
`/etc/dovecot/conf.d/90-plugin.conf` so it looks like the following:

``` lighty
plugin {
	sieve_plugins = sieve_imapsieve sieve_extprograms
	
	# Moving to Spam
	imapsieve_mailbox1_name = Spam
	imapsieve_mailbox1_causes = COPY
	imapsieve_mailbox1_before = file:/usr/local/lib/dovecot/sieve/report-spam.sieve
	
	# Moving from Spam
	imapsieve_mailbox2_name = *
	imapsieve_mailbox2_from = Spam
	imapsieve_mailbox2_causes = COPY
	imapsieve_mailbox2_before = file:/usr/local/lib/dovecot/sieve/report-ham.sieve
	
	sieve_pipe_bin_dir = /usr/local/lib/dovecot/sieve
	
	sieve_global_extensions = +vnd.dovecot.pipe +vnd.dovecot.environment
}
```

This configuration allows sieve to "learn" what types of emails are spam and
what types of messages are not spam (sometimes called "ham" 🍖) depending on
where you move them. In order to make these filters functional we need to create
two sieve scripts. The first one is
`/usr/local/lib/dovecot/sieve/report-spam.sieve`. Let's create it and add the
following to it:

``` sieve
require ["vnd.dovecot.pipe", "copy", "imapsieve", "environment", "variables"];

if environment :matches "imap.user" "*" {
	set "username" "${1}";
}

pipe :copy "sa-learn-spam.sh" [ "${username}" ];
```

The second filter is `/usr/local/lib/dovecot/sieve/report-ham.sieve` and should
look like this:

``` sieve
require ["vnd.dovecot.pipe", "copy", "imapsieve", "environment", "variables"];

if environment :matches "imap.mailbox" "*" {
	set "mailbox" "${1}";
}

if string "${mailbox}" "Trash" {
	stop;
}

if environment :matches "imap.user" "*" {
	set "username" "${1}";
}

pipe :copy "sa-learn-ham.sh" [ "${username}" ];
```

Now we will create two shell scripts for these filters. The first one is
`/usr/local/lib/dovecot/sieve/sa-learn-ham.sh`:

``` sh
#!/bin/sh
exec /usr/local/bin/rspamc -d "${1}" learn_ham
```

The second one is `/usr/local/lib/dovecot/sieve/sa-learn-spam.sh`:

``` sh
#!/bin/sh
exec /usr/local/bin/rspamc -d "${1}" learn_spam
```

And we need to make them executable:

``` sh
doas chmod +x /usr/local/lib/dovecot/sieve/sa-learn-spam.sh /usr/local/lib/dovecot/sieve/sa-learn-ham.sh
```

Now we need to compile the sieve filters to actually use them:

``` sh
doas sievec /usr/local/lib/dovecot/sieve/report-spam.sieve
doas sievec /usr/local/lib/dovecot/sieve/report-ham.sieve
```

Lastly, let's enable and start `dovecot`:

``` sh
doas rcctl enable dovecot
doas rcctl start dovecot
```

## Done!

**Congrats!** You now have your own email server running on OpenBSD! You can now
login to your email server using your preferred email client. If you don't have
a preferred email client then you can use [Thunderbird](https://thunderbird.net)
if you want a GUI or [aerc](https://aerc-mail.org/) if you want to view emails
from the terminal.

You can now enjoy using email knowing that your inbox won't be monitored. 😎

[^1]: Just a friendly reminder to change "example.com" to your specific domain
name.
