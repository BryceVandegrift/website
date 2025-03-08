---
title: "The Importance of Lisp"
date: 2022-09-13
draft: false
---

Lisp (also known as LISP) is a family of programming languages that have had a
significant impact on the world of computing. Lisp has had and still
has a great influence on the evolution of programming languages and computing
theory as a whole. It remains a very easy language to learn and not that hard
to master. 

For those of you who don't know what Lisp is, Lisp stands for "LISt Processor".
A Lisp program is made up of at least of at least one "list" which is
indicated by an expression surrounded by parentheses. So for example,
if you wanted to multiply 2 and 4, you would give an expression like this
to a Lisp interpreter or compiler:

``` scheme
(* 2 4) ; Results in 8
```

The Lisp interpreter or compiler evaluates the contents of the list to
produce a result. The first element of the list is the operator being used,
in this case its multiplication (`*`). Any other items in the list are
arguments given to the operator, in this case `2` and `4`. If you are familiar
with Polish Notation or Prefix Notation then this may look very similar, the
only difference being the addition of parentheses to enclose the expression.

We can easily create compound expressions by just putting lists inside of
lists like so:

``` scheme
(- 10 (/ 4 2)) ; Equivalent to 10 - (4 / 2)
(+ (* 2 2) (* 2 5)) ; Equivalent to (2 * 2) + (2 * 5)
```

It takes a bit of getting used to, but Lisp's notation makes it very easy to
string together compound expressions.

## Practical Lisp

Nowadays there are two different major dialects of Lisp, **Common Lisp** and
**Scheme**. There are not that many differences between Common Lisp and
Scheme so I will use the Scheme dialect in the rest of these examples since
I am familiar with it.

If we want to, we can define custom operators, functions, or variables just
like in any other programming language. To do so in Scheme we use the
`define` keyword:

``` scheme
(define x 24) ; Define the variable x as 24
(define mult *) ; Define the operator mult as multiplication
(define (circum radius) (* 2 3.14 radius)) ; Define circumference as 2 * π * radius
```

You may have noticed that we defined all three of these with the same `define`
keyword, so what makes a variable different from a function? Nothing really.
The great thing about Lisp is that almost everything defined under a `define`
statement is treated the same. This means that almost anything we can do with
variables we can do with functions and vice versa. So, for example, we can
create a function like this:

``` scheme
(define (applyFunc func arg1 arg2) (func arg1 arg2))
```

This function, `applyFunc`, takes three arguments: a function and two arguments
for the given function. It then applies the two arguments to the given function
and spits out a result. We can test our `applyFunc` function like so:

``` scheme
(define (sum-of-squares x y) (+ (* x x) (* y y))) ; Same as x^2 + y^2
(applyFunc sum-of-squares 2 4)
```

Now this may look fairly useless at the moment because we can just call
`sum-of-squares` directly and pass it arguments manually. However, being able
to pass functions as arguments opens a whole new world of possibilities when
it comes to programming.

## Higher-Order Functions

This brings us to an important topic, anonymous functions using the `lambda`
keyword. The `lambda` keyword creates a one-time use function, this can be
very useful for passing to another function as an argument. As an example we
can use a lambda function with our `applyFunc` function.

``` scheme
(applyFunc (lambda (x y) (* x y)) 2 4) ; Same a 2 * 4
```

The example above creates a one-time use function `(lambda (x y) (* x y))`
and passes it to `applyFunc` with the arguments `2` and `4`. Although the
result isn't that spectacular, the implications of lambda functions opens up
a whole new world of possibilities for programming that only exist in
functional languages like Lisp. Functions that take functions or return
functions are called `higher-order functions`.

Lisp was one of, if not the first language to implement higher-order functions
in a programming language. Other high-level programming languages like Lua,
Python, Haskell, and more followed suit much later.

## Recursion

Another important concept pioneered by Lisp in the world of programming is
recursion. A function is a recursive function when it calls itself, pretty
simple. Almost all recursive functions also have a base-case or exit clause
in order to stop recurring, otherwise they run forever.
If you still don't understand recursion then read this sentence again. ;)

As an example, let's translate the equation for Fibonacci numbers into a
Lisp expression. The mathematical definition is a follows:

{{< img src="/p/fib.webp" mouse="Recursive Fibonacci Equation" alt="Fibonacci Equation">}}

A translation from mathematical notation into Lisp would look like this:

``` scheme
(define (fib n)			  ; Begin function
(if (<= n 1) n			  ; If n <= 1 then return n
(+ (fib (- n 1)) (fib (- n 2))))) ; Else return fib(n-1) + fib(n-2)
```

As you can see, the `fib` function defined above calls itself until it meets
the base requirements to end the recursive loop. Recursion makes it stupidly
easy to translate recursive mathematical functions to Lisp expressions.
Just like higher-order functions, Lisp was one of the first programming languages
to implement this and other languages followed afterwards.

## Hold Up

Now, before you go out and write your next project in Lisp, you should keep
something in mind. Lisp is **not** the fastest or smallest language out there, it
was not designed to be so. There are some pretty good implementations of Lisp
out in the wild, but don't expect them to outperform C, Go, Lua, or even
Python most of the time.

If you're going to program something in Lisp you should keep these things in
mind:

- How important is execution speed?
- How important is program/runtime size?
- How important is portability?

If you value any three of these too much, then you might not want to write
your program in Lisp. You probably don't want to write a program that only
takes up a few kilobytes in Lisp, nor would you want to write a program that
needs millisecond speed either. However for all other needs Lisp works
perfectly fine if not exceptionally great!

But this brings us back to the importance of Lisp altogether. Most people
look at Lisp and see a language that innovated countless things in the field
of programming languages, but eventually got replaced by newer languages. But
Lisp as a language is still very much alive and making improvements and
innovations to this day. It is still a very good programming language for
tackling many tasks and remains one of my favorite programming languages.

{{< img src="/p/lisp.webp" link="/p/lisp.webp" alt="LISP machine" >}}
