---
title: "OpenBSD Server: httpd"
date: 2025-02-27T15:06:37-05:00
draft: false
---

I finally got around to writing part 2 of this series. If you have not read part
1 yet, then you can find it [here](/blog/openbsd-server-install). So now that we
have our base server installed, we can go ahead and setup `httpd` to serve our
website.

## Setup httpd

Let's go ahead and create a very simple http server using `httpd` just to make
sure that everything is working properly. We can make a config file for `httpd`
at `/etc/httpd.conf` like so:

``` lighty 
server "example.com" {
	listen on * port 80
	alias "www.example.com"
	root "/htdocs/example.com"
}

types {
	include "/usr/share/misc/mime.types"
}
```

This will create a web server for "example.com" (make sure to replace
"example.com" with your domain name) on port 80 and will redirect traffic from
"www.example.com" to "example.com". Next we need to create
`/var/www/htdocs/example.com` to host our files:

``` sh
mkdir -p /var/www/htdocs/example.com
```

Let's just put a very basic webpage at `/var/www/htdocs/example.com/index.html`
to serve as our homepage.

``` html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Example webpage</title>
    <meta charset="utf-8">
  </head>
  <body>
    <h1>This is an example webpage</h1>
    <p>Looks like it works!</p>
  </body>
</html>
```

Now, in order to make sure that our httpd config file is correct and does not
have any errors, we can run this command to check it:

``` sh
doas httpd -n
```

If your configuration is correct, then it will spit out `configuration ok` and
you can move on. If not, then it will spit out what error you have to fix in the
`/etc/httpd.conf` file. Finally we can enable httpd on boot and start it using
the `rcctl` command:

``` sh
doas rcctl enable httpd
doas rcctl start httpd
```

**Congrats!** You now have a web page on the internet at "example.com"!

## Getting a certificate

Now that we verified that a normal web server works, we can go ahead and setup
ACME in order to obtain a certificate in order to allow HTTPS connects to our
website. First we need to create a file named `/etc/acme-client.conf` and add
this to our file:

``` lighty
authority letsencrypt {
	api url "https://acme-v02.api.letsencrypt.org/directory"
	account key "/etc/acme/letsencrypt-privkey.pem"
}

domain example.com {
	# Allows an alias
	alternative names { www.example.com }
	domain key "/etc/ssl/private/example.com.key"
	domain full chain certificate "/etc/ssl/example.com.fullchain.pem"
	sign with letsencrypt
}
```

The first section defines Let's Encrypt as our certificate authority. The
second section defines a domain to fetch a certificate for. It will place our
domain key at `/etc/ssl/private/example.com.key` and our certificate at
`/etc/ssl/example.com.fullchain.pem` (remember to replace "example.com" with
your domain).

Now we have to edit `/etc/httpd.conf` in order to ACME to generate a certificate
for our domain:

``` lighty
server "example.com" {
	listen on * port 80
	alias "www.example.com"
	root "/htdocs/example.com"
	
	location "/.well-known/acme-challenge/*" {
		request strip 2
		root "/acme"
	}
}

types {
	include "/usr/share/misc/mime.types"
}
```

Now we can run this command in order to generate a certificate for our domain:

``` sh
acme-client -v example.com
```

## Setup HTTPS

Now that we have our certificate, let's go ahead and edit our `/etc/httpd.conf`
file in order to use HTTPS for our web server:

``` lighty
server "example.com" {
	listen on * tls port 443
	alias "www.example.com"
	root "/htdocs/example.com"
	
	tls {
		certificate "/etc/ssl/example.com.fullchain.pem"
		key "/etc/ssl/private/example.com.key"
	}
	
	location "/.well-known/acme-challenge/*" {
		request strip 2
		root "/acme"
	}
}

server "example.com" {
	listen on * port 80
	alias "www.example.com"
	block return 301 "https://example.com$REQUEST_URI"
}

types {
	include "/usr/share/misc/mime.types"
}
```

Finally we can restart `httpd` and go to "example.com" and we will have a secure
connection to the website.

## Automatically renew certificates

The final part of this post is going to take care of lose ends by automatically
renewing our certificate before it expires. Most certificates expire after only
a few months, so we want to automatically renew them. In order to do so we can
make a new file at `/etc/weekly.local`. Any commands that we put in this file
will run once a week, so lets put this in `/etc/weekly.local`:

``` sh
# Check for new certificates every week
acme-client -v example.com
rcctl restart httpd
```

## Almost Done

That's the end of part 2.
The final part of this 3 part series will be about setting up an email server
using `smtpd` and setting up spam filtering (as well as other things). So stay
tuned for part 3.

---

You can now read part 3 of this series [here](/blog/openbsd-smtpd).

---
