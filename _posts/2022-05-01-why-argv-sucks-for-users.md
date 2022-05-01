---
layout: post
title:  "Why argv sucks for users"
published: true
author: nemanjan00
image: /assets/blur-bright-business-codes-207580.jpg
hero_image: /assets/blur-bright-business-codes-207580.jpg
hero_darken: true
show_sidebar: true
---

There are some conventions and patterns that are part of so much software we are never getting rid of them.
One of those conventions defines how we pass arguments to applications. 

For people who use command line applications and who are in contact with the arguments
the most, it is very easy to develope the tolerance to **how fucked up they are**.
For other people, they probably never even realized it.

Just for simplicity, I will stay away from argument passing in bash, variables, pipes...

## The argument prefix monster

Just like the most people, my first contact with command-line applications was as user of
Windows and inside of `cmd.exe`.

Crazy thing about passing args in Windows, looking back at it is, Windows does not
use `-` for passing arguments, it uses `/`.

For example, on Windows, you will encounter something like this:

```batch
shutdown /s /f /t 0
```

Now, some of you will say *no, that is not the truth, Windows also uses `-`*.
And you would also be correct. This is just as valid:

```batch
shutdown -s -f -t 0
```

It is just as valid, until it is not. Crazy thing is, it is up to developer to
parse those arguments and if developer decided to support only one of those, you
might make a misstake without realizing.

But Windows users do not use command-line as often as Linux users do, sure it is
done better on Linux, right?

How about no?

Truth be told, on Linux at least `/` is not used, but, now, we come to the issue of
`-` vs `--`.

We all know `-` is used for short version of argument name and `--` is used for long version, right?

Sure we do, until we do not. There are plenty of programs out there that just use `-`
for everything.

## The argument value monster

Ok, but we can just try all try versions and be over with it, right, no big deal?

How about passing values for arguments?

There also are different ways:

``` bash
# quick note that not all of these make sense as ls arguments and some of
# them are here just for ilustration
ls --color=always # ok, this looks simple enough
ls --color always # wait, what, is = required or not?
ls -c always
ls -c=always
ls -calways # WTF!? Why is this even option? I have seen this one used a lot.
ls -c # Wait, what, what happens when you want argument without value? How is next thing parsed?
ls --color
```

According to last 2 examples now we know that other than type of prefix used,
there is another issue preventing generic parsers for arguments.

## The argument value monster

At least values are simple, right?

Not at all. Values are probably the craziest one so far...

What if you want your value to begin with `-`?

```bash
ls -c "\-always" # this is the way to do it,
# but it is so inconsistently implemented and unintuitive...
```

What if you want your value to be empty string?

```bash
ls -c "" # this one is consistent, but also might not be intuitive for everyone
```

What if you need to pass multiple values?

```bash
ls -c a -c l -c w -c a
ls -c "a l w a y s"
ls -c "a,l,w,a,y,s"
ls -c "a, l, w, a, y, s"
ls -ca a # yes, there are multichar short names
```

What if value is multiline?
...

The point is, there are a lot of edge cases implemented on per application basis.

## Unnamed arguments monster

Ok, those are simple, just pass them as documented, right?

What if there are multiple optional arguments?

Is order of named and unnamed arguments important? The problem I encounter most ofthen
is that parsers expect named arguments to go first, and unnamed later, but, you would be wrong
to assume that is always the case.

```bash
ffmpeg -ss 50 -i video1.mp4  -c copy -t 10 video2.mp4
```

## Completly crazy examples

One example of complete lack of any respect for convention is `dig`. Example would be:

```bash
dig -4 +all google.com @8.8.8.8
```

Believe it or not, this is valid and expected way to use `dig`.
