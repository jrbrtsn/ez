# libez

(C) 2019 John D. Robertson <john@rrci.com>


In an effort to make C programming both easier and more secure, I present the
EZ method.

Reasoning for this is simple; Many function calls can fail for a variety of
reasons, some or even most of which are obscure. However, it is both insecure
and counterproductive not to check for said failures. Unfortunately coding
proper checks and/or recovery from failures will involve (often complex) logic
constructs, and detracts from the readability of your source code.

Sometimes your app needs to recover and so you must supply this code, but more
often than not a boilerplate approach is sufficient. The EZ method is a
consistent convention to provide centralized and reusable boilerplate error
handling on an _optional_ basis, both for existing library functions and your
own.

Modern programming languages address this problem by throwing *exceptions*,
which by default result in your program getting terminated. To avoid
termination you must write code to *catch* such exceptions, but this code
exists separated in source code from the function call, and tends to be both
confusing to read and hideous to look at. If you think *goto* statements are in
poor taste, then these will make you vomit.

Enter the EZ method; from a practical coding standpoint you merely prepend
"ez\_" to any function call for which you desire default error handling. When
an error occurs a very helpful error message is printed (in DEBUG mode this
includes the source code location from which the function was called), and your
program is terminated. If your app needs to recover from such an error, simply
erase the "ez\_" prefix from the function call and the original unwrapped
function gets called. You may then implement the recovery code where God
intended it to be - right where you make the function call!

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
errors will be handled safely and securely:

```
FILE *fh= ez_fopen("/home/rip/van.winkle", "r");

while(ez_fgets(buf, sizeof(buf), fh)) {
   /* Process contents of buf */
}
```

## EZ Wrapper Implementation

Implementing an EZ wrapper for an existing library function involves:

+ Supply an entry in the *ez\_libXXX.h* header file for both DEBUG and
production versions of the wrapper function, *\_ez\_fnXXX()*

+ Supply two versions of the *ez_fnXXX()* macro in the *ez\_libXXX.h* bracketed
with *#ifdef DEBUG*, *#else*, *#endif*.  These macros may be variadic to avoid
unnecessary complexity.

+ Supply the *\_ez\_fnXXX()* implementation in the *ez\_libXXX.c* file.

### Implementation Notes

Two macros located in *ez.h* help minimize the amount of source code required
in the header and implementation files.  Using these is optional. The DEBUG
version of the wrapper passes the C preprocessor macros *__FILE__*, *__LINE__*,
and *__func__* as the first three arguments to  *\_ez\_fnXXX()* so that the
source location of the function call may be included in error messages. For the
production version these macros are not passed, and the source code location of
the function call cannot be included in the error message. This is usually fine
since it should be rare that errors get caught with EZ error handling by the
time your app reaches production.

## Crowd Sourcing

Of course the EZ wrapper implementations must come from somewhere. The included
source source code supplies these for many of the functions I commonly use.
Header files are named after the library whose functions are getting wrapped,
with *ez_libc.h* being the largest.  It is my goal to crowd source the bulk of
EZ wrapper implementations for various C libraries.

Many hands make light work, and I hope that you find this strategy and code as
useful as I have for improving the readability, reliability, and security of
your code.
