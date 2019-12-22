#libez

(C) 2019 John D. Robertson <john@rrci.com>


In an effort to make C programming both easier and more secure, I present the
EZ method.

Reasoning for this is simple; Many function calls can fail for a variety of
reasons, some or even most of which are obscure. However, it is both insecure
and counterproductive not to check for said failures. Unfortunately coding
proper checks and/or recovery from failures will involve (often complex) logic
constructs, and detracts from understanding the primary logic of your source
code.

Sometimes your app needs to recover, and so you must supply this code, but more
often than not a boilplate approach is sufficient. The EZ method is a
consistent convention to provide centralized and reusable boilerplate error
handling on an _optional_ basis, both for existing library functions and your
own.

Modern programming languages address this problem by throwing *exceptions*,
which by default result in your program getting terminated. To avoid
termination you must write code to *catch* such exceptions, but this code
exists separated in source code from the function call, and tends to be both
confusing to read and hideous to look at. If you think *goto* statements are in
poor taste, then this will make you vomit.

Enter the EZ method; from a practical coding standpoint you merely prepend
"ez\_" to any function call for which you desire default error handling. When
an error occurs a very helpful error message is printed (in DEBUG mode this
includes the source code location from which the function was called), and your
program is terminated. If your app needs to recover from such an error, simply
erase the "ez\_" prefix from the function call and supply the recovery code,
where God intended it to be - right where you made the function call!

In effect the EZ method provides the utility of throwing exceptions, but
without the catch - Woohoo!

## Example

In the following segment of code, there is no error checking at all:

```
FILE *fh= fopen("/home/rip/van.winkle", "r");

while(fgets(buf, sizeof(buf), fh)) {
   /* Process contents of buf */
}
```

Of course both *fopen()* and *fgets()* could encounter errors. Now let's employ
the EZ method - just place *ez\_* in front of the two functions, and runtime
errors will be handled safely and securely.

```
FILE *fh= ez_fopen("/home/rip/van.winkle", "r");

while(ez_fgets(buf, sizeof(buf), fh)) {
   /* Process contents of buf */
}
```

## Crowd Sourcing

It is my goal to crowd source EZ implementations of functions found in various
C libraries. This project contains the beginning of that effort, with many
commonly used functions already in place.

Many hands make light work, and I hope that you find this strategy and code as
useful as I have for improving the readability, reliability, and security of
your code.
