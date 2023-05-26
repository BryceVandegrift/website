---
title: "Stop Saying C/C++"
date: 2023-05-18T13:13:36-04:00
draft: false
---

For as long as I can remember, I have heard people say C/C++ when referring to
a project written in C and or C++. A lot of programming/developer jobs also
refer to C/C++ when they need a programmer who knows either C or C++. To most
people who have never touched C or C++ this might not seem like a big deal.
However, the problem is that when people say this term (C/C++) they make it
seem like C and C++ are similar or closely related programming languages.
**That is not true.** Although C++ was based off of C when it was first
created, these two languages have slowly drifted apart over the years to the
point where they share less and less in common[^1].

## C and C++ are VERY Different

There is probably someone who is going to say, "Well you can write C code in
a C++ program, so technically C is a subset of C++." The only problem is that
you can write C code in [Zig](https://ziglang.org/documentation/master/#C),
[Go](https://pkg.go.dev/cmd/cgo), [Nim](https://github.com/nim-lang/Nim/wiki/Nim-for-C-programmers),
and basically almost every other language out there has a C FFI! So should I refer to Zig,
Go, and Nim as C/Zig, C/Go, and C/Nim? Obviously **no**.

### C with Classes

> "But C++ is just C with classes!"

**No**, it isn't. Anyone who says this obviously has never worked with C++. C++ has
completely different standard libraries, implementations, and standards than C.
It is true that when C++ was first made it was just C with classes[^2], that has
long been false ever since C++ has implemented features separate from C.

### Incompatibility

#### Void Pointers

One such case where C++ is incompatible with C is with void pointers.
For example, this program will compile with a C compiler (like GCC), but it
will not compile with a C++ compiler (like G++):

``` c
#include <stdlib.h>

int main() {
	int *a = malloc(5);
	return 0;
}
```

All this code does is allocate 5 bytes to an integer pointer `a`. This program
works perfectly fine when compiled with GCC, but if I compile this program with
G++ this error is returned:

```
main.c: In function 'int main()':
main.c:4:24: error: invalid conversion from 'void*' to 'int*' [-fpermissive]
    4 |         int *a = malloc(5);
      |                  ~~~~~~^~~
      |                        |
      |                        void*
```

The reason this happens is that `malloc` returns a void pointer and C++ cannot
convert a void pointer into an integer pointer unless it is specifically cast
to an integer pointer.

#### K&R Syntax

Another big incompatibility with C and C++ is that C++ is actually incompatible
with [K&R](https://en.wikipedia.org/wiki/C_(programming_language)#K&R_C) syntax. Given this example function formatted in K&R syntax:

``` c
int gcd(a, b)
	int a;
	int b;
{
	if (b == 0)
		return a;
	return gcd(b, (a % b));
}
```

It will compile perfectly fine for GCC (as expected), however G++ gives us
*another* set of errors...

```
gcd.c:3:9: error: 'a' was not declared in this scope
    3 | int gcd(a, b)
      |         ^
gcd.c:3:12: error: 'b' was not declared in this scope
    3 | int gcd(a, b)
      |            ^
gcd.c:3:13: error: expression list treated as compound expression in initializer [-fpermissive]
    3 | int gcd(a, b)
      |             ^
gcd.c:6:1: error: expected unqualified-id before '{' token
    6 | {
      | ^
```

This makes it almost impossible to use K&R syntax with C++ unless you format
your function arguments according to [ANSI C](https://gist.github.com/nicholatian/2d9514feaf9a95e7561a433ac404b141).
(I know not many people care about K&R syntax, but I think that it is still an
important difference).

There are also many other things in C that will not transfer over to C++ like
complex numbers, default return types, and more, but
I think you already get the picture by now. These incompatibilities are not
anything that would break the entire C language if used in conjunction with
C++, but these small differences slowly add up.

### Hard for Beginners

Not differentiating between C and C++ also has the side effect of ostracizing new
users. Many beginner programmers are lead by the term "C/C++" to think that
they're basically the same language. In fact there are [many](https://medium.com/@yekayama/stop-making-c-c-tutorials-2fa9bc114488) tutorials out there
that are advertised as "C/C++ tutorials", continuing the confusion.
This can also scare away C beginners by making them think that understanding
the complexities of C++ are required to understand C (*SPOILER*: They're not).
I have fallen for this trap in the past, as well as many others.
C is honestly a very simple programming language, C++ is not.

## C and C++ Programmers are VERY Different

With the new C++ standards given throughout the years like C++11, C++20, and
etc. C++ programmers have been given more tools and functions that don't exist
in standard C. This usually results in modern C programs having more lines of
code than modern C++, however this means that modern C is usually more readable
than modern C++. Here is an example question from [LeetCode](https://leetcode.com/problems/maximum-count-of-positive-integer-and-negative-integer/).
Solutions differ, but most C solutions look something like this:

``` c
int maximumCount(int *nums, int numsSize) {
	int pos = 0, neg = 0;
	for (int i = 0; i < numsSize; i++) {
		if (nums[i] > 0) pos++;
		else if (nums[i] < 0) neg ++;
	}
	return pos > neg ? pos : neg;
}
```

Even though this code is pretty compact for C standards it is still *very*
readable. Now for the C++ solutions, there are a lot of variations to this
solution so I will use one that's different *enough* from C.

``` cpp
int maximumCount(std::vector<int> nums) {
	auto [a, b] = std::equal_range(nums.begin(), nums.end(), 0);
	return std::max(std::distance(nums.begin(), a), std::distance(b, nums.end()));
}
```

This uses `vector` and `algorithm` from the C++ standard library[^3].
As you can see this code is *much* more compact but is definitely not as
readable as the C code. Although the C solution can be compiled by a C++
compiler I wanted to highlight just how different they can be from each other.
This is but one example of how C and C++ programmers have slowly separated
when it comes to programming.

### Many C Programmers Won't Touch C++

I'm pretty sure everyone knows the C programmer stereotype by now, the only
thing is that it is **TRUE**.
Lots of [Suckless](https://suckless.org/) users and developers only use
C and POSIX shell in their programs. [Cat-v](https://harmful.cat-v.org/software/c++/) endorses
C and C-like languages, but despises C++. Even
[Linus Torvalds](https://lore.kernel.org/all/alpine.LFD.0.999.0709061839510.5626@evo.linux-foundation.org/),
the creator Linux and Git, won't touch C++.
Heck, even I love C but I can't stand programming in C++.

This is probably the biggest reason why employers **SHOULD NOT** put C/C++
on job descriptions, especially if they're only looking for C developers.
All they are doing is scaring away competent C developers.

{{< img src="/p/cpp.webp" alt="Stop doing C++!" link="/p/cpp.webp" id="smallimg" >}}

## The Solution

If you're referring to a C program or programmer just say "C".
If you're referring to a C++ program or programmer just say "C++".
If you're referring to both used separately say something like:

- C and C++
- C, C++
- C++ with C
- Etc.

**NOT C/C++**

Only if you're using C *together* with C++ would it be acceptable to
say C/C++.

[^1]: Please note that although these are real concerns with C and C++,
this is more of a rant than anything else (and somewhat satire).

[^2]: Fun fact: C++ *was* actually called ["C with Classes"](https://www.stroustrup.com/bs_faq.html#invention) before it was
initially released.

[^3]: Credit to [code_report](https://youtu.be/U6I-Kwj-AvY) for these two solutions.
