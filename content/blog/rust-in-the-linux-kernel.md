---
title: "Rust in the Linux Kernel"
date: 2022-10-25T17:32:01-05:00
draft: false
---

I know, I know, everyone is talking about support for the [Rust](https://www.rust-lang.org/) programming
language being [added to the Linux kernel](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8aebac82933ff1a7c8eede18cab11e1115e2062b), and I'm no exception. This event
is **HUGE** when it comes to Linux kernel development. Since the Linux kernel
is predominantly written in C and has been that way for about the last 30
years, this comes as a very big surprise. I honestly would have expected
C++ to be integrated in the Linux kernel if it weren't for Linus Torvald's
[hatred for C++](https://lore.kernel.org/all/alpine.LFD.0.999.0709061839510.5626@evo.linux-foundation.org/) (granted, I dislike C++ as well).

Now I'm not here to praise Rust as some sort of gift to the Linux kernel, nor
am I here to talk horribly about it and say that it has no use in the Linux
kernel whatsoever, because it does have a use. I am here to take a look at
what Rust does and doesn't do good and see if that lines up with the needs of
the Linux kernel.

### Speed/Performance

The average speed of programs written in Rust is about on par with programs
written in C from what I've seen. You can look at some of the benchmarks
[here](https://programming-language-benchmarks.vercel.app/c-vs-rust), but I also have a quick and simple one that I whipped up.
This example uses a very lazy and inefficient version of the Fibonacci
algorithm that I used a while ago in my [lisp article](https://brycevandegrift.xyz/blog/the-importance-of-lisp/#recursion).

Now I'm no expert programmer so don't complain if I didn't write either of
these programs "correctly", it's just a simple benchmark

[Machine specs](https://brycevandegrift.xyz/hardware/)

Implemented in C:

``` c
#include <stdio.h>
#include <stdlib.h>

int32_t fib(int32_t num) {
	if (num <= 1) {
		return num;
	}
	return fib(num - 1) + fib(num - 2);
}

int main() {
	int32_t result = fib(45);
	printf("%d\n", result);
	return 0;
}
```

Implemented in Rust:

``` rust
fn fib(num: i32) -> i32 {
    if num <= 1 {
        return num;
    }
    return fib(num - 1) + fib(num - 2);
}

fn main() {
    let result:i32 = fib(45);
    println!("{}", result);
}
```

The average time for C compiled with GCC and `-O2` optimization was 3.30
seconds and Rust compiled with RustC and `-O2` as well was 4.32 seconds.
C without optimizations was 9.31 seconds and Rust without optimizations was
12.69 seconds on average. So unoptimized Rust is a decent bit slower than C
and optimized Rust is about a second behind C which is what I would expect.
A bit of a performance hit is what I would expect for a language that focuses
more on memory safety rather than pure speed.

### Size

When it comes to size, Rust and C are both around the same as well. Although
I sometimes do hear complaints about Rust's standard library being large, the
Rust standard library probably isn't going to be included in the Linux kernel.
Compiling a "Hello, World!" program in GCC with `-O2` is around 21KB normally
and 15KB stripped. Compiling the same program in RustC with `-O2` results in a
21KB binary normally and a 14KB binary stripped (no standard library included).
So Rust can actually end up with smaller binaries than C on some occasions,
but in reality I would honestly expect the same size (if not slightly bigger)
binary sizes for Rust for most cases.

### Compiler/Frontend

The Rust compiler is probably one of Rust's biggest strengths compared to C.
The Rust compiler is a very helpful tool for developers. There are other just as
helpful tools that the creators of Rust provide separately like [rustfmt](https://github.com/rust-lang/rustfmt)
(like gofmt if you have used Go before), [Clippy](https://github.com/rust-lang/rust-clippy),
and more. But the Rust compiler is probably one of the most straightforward
and user friendly (in a mostly good way) compilers that I have ever seen.
Just take a look at the difference between an error message in GCC v.s. an
error message in RustC. The same mistake is being made in both languages.

An error message from GCC:
```
test.c: In function 'main':
test.c:8:32: warning: passing argument 1 of 'sumOfSquares' makes integer from pointer without a cast
[-Wint-conversion]
    8 |  int32_t result = sumOfSquares("Number");
      |                                ^~~~~~~~
      |                                |
      |                                char *
test.c:3:30: note: expected 'int32_t' {aka 'int'} but argument is of type 'char *'
    3 | int32_t sumOfSquares(int32_t x, int32_t y) {
      |                      ~~~~~~~~^
test.c:8:19: error: too few arguments to function 'sumOfSquares'
    8 |  int32_t result = sumOfSquares("Number");
      |                   ^~~~~~~~~~~~
test.c:3:9: note: declared here
    3 | int32_t sumOfSquares(int32_t x, int32_t y) {
      |         ^~~~~~~~~~~~
```

An error message from RustC:
```
error[E0061]: this function takes 2 arguments but 1 argument was supplied
 --> test.rs:6:19
  |
6 |     let _result = sum_of_squares("Number");
  |                   ^^^^^^^^^^^^^^----------
  |                                 ||
  |                                 |expected `i32`, found `&str`
  |                                 an argument of type `i32` is missing
  |
note: function defined here
 --> test.rs:1:4
  |
1 | fn sum_of_squares(x: i32, y: i32) -> i32 {
  |    ^^^^^^^^^^^^^^ ------  ------
help: provide the argument
  |
6 |     let _result = sum_of_squares(/* i32 */, /* i32 */);
  |                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

error: aborting due to previous error

For more information about this error, try `rustc --explain E0061`.
```

These error messages may look similar, but in my opinion the error/warning
messages from RustC are just a bit more clear than the messages from GCC and
definitely a lot clearer than other compilers.

When it comes to the Rust frontend, LLVM, I don't really mind it as a frontend.
For the Linux kernel, I think that GCC would be a better frontend for Rust to
use and there is work being done on just [that](https://github.com/Rust-GCC/gccrs).
But many languages use like [D](https://dlang.org/) ([LDC](https://github.com/ldc-developers/ldc)), newer versions of [Haskell](https://www.haskell.org/), [Zig](https://ziglang.org/), and
many other languages use LLVM and they generate pretty good results most of
the time. Is it the most ideal frontend when working with the Linux kernel,
probably not, but is it the worst? Definitely not.

### Memory Safety

What everyone knows Rust for, it's memory safety. The biggest tool that Rust
has at it's disposal to try to guarantee memory safety is the borrow checker.
For those of you who don't know what the borrow checker is, the borrow checker
assigns memory to a variable known as an owner. Once the owner of a piece of
data is out of scope, it deallocates the memory. You can also "borrow" the
memory (hence the name "borrow checker), but I won't go too into detail, 
since there are plenty of better explaniations of Rust's borrow checker. Just
keep in mind that the borrow checker is very important for memory safety.

I think that memory safety is Rust's biggest and most important contribution
to the Linux kernel. Memory safety can eliminate a lot if not the majority of
bugs out there in the wild and I think that the Linux kernel is no exception.
If used correctly, I think that Rust's memory safety could make the Linux
kernel even more robust than it already is with little to no sacrifices.


### Conclusion

Overall, I think that the inclusion of Rust in the Linux kernel isn't too bad
of a decision. Don't get me wrong, there are some bad or not fully fleshed
out parts to the language, but otherwise it's pretty solid. Now I'm no expert
programmer in either C or Rust, but I think that there could be a very good
balance within the Linux kernel if Rust is used correctly.

That being said, I'm personally still not sold on Rust's inclusion into the Linux
kernel. Despite the advantages of memory safety and a better compiler there
are still lots of parts of Rust that are not fully fleshed out and could
cause various problems for the Linux kernel. Although I think that Rust is
the best choice of any language out there to add to the Linux kernel,
I personally don't think that another language needs to be added to the Linux
kernel. I guess all that I can do is wait and see if this addition to the Linux
kernel is a good one or not.
