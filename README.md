# Preliminary plan for the DevCamp 2013, Linz

Let's try to group our ideas in several big topics and then expand each big
topic in a smaller list(s) of bulletpoints.  As for formatting, please use the
[markdown format] [md] while editing this file.


## Improvements in the build system

 * Sac distribution.  Binary and source one.  We have a directory called `dist`
   in the root of the SaC compiler.  Agree on the way to do it, and write clean
   and *documented* code.
   1. There is a Makefile which makes a naive attempt to create a sources for 
      a specific architecture.  Well, assuming that it works, which I highly
      doubt, there have to be extra steps regarding configuration paramtetes.
      One has to propesly substiute compiler, flags, and os-name, etc in header
      files, in makefiles and in config.h.  Also, the makefile assumes that
      there is a stdlib and demos directory.  This is wrong assumption.
   2. There is an `install_sac` script which depends on the output of the 
      Makefile (??) and which compiles stdlib and demos on demand.  I don't
      understand the assumption regarding sac2c.  Should it be compied already?

 * Create install/uninstall target in the Makefile, avoiding shell variables.
   Move shell variables into sac2crc and define the default location of this
   file.

 * Agree on a versioning and implement it.  Here is a patch that does a proper
   manual versioning.  What about this Bob's automatic numbers?

## Testing
 * Fix the masterrun (ha-ha :)

 * Create a target in the Makefile called `test` that would allow to run a
   short (time-wise ~5min max) suite of tests to verify the sanity of your
   changes.

 * Introduce _some_ memroy-leak testing.  Valgrind returns a three mile long 
   list of memory leaks.  There should be none...

## General problems && bug fixing

 * Get rid of the remainders of old lexer/parser in the code, makefiles and 
   configure scripts.  WTF id `new_parser.c` from Fangyong?
 * Clean-up configure.ac:
   1. `sbrk` check is insane.
   2. revise architecture and compilers section.  Do those things even make
      sense?  Have you ever used DECC at all?  Can we unify darwin flags,
      without specifying _exact_ version?
   3. `MAX_PATH_LEN` definition is insane, as say, in POSIX you have it as a
      macro in include files, and you can specify it when you build a kernel.
      So specifying some constant is not really a good idea.
   4. How about CUDA checks, is it sane enough?

 * Error messaging.  One of the problems one could investigate is the lack of
   location numbers in the intermediate statements.  See X for mode details.

[md]: http://daringfireball.net/projects/markdown/syntax "Markdown syntax"
