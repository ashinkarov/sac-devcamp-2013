Github doesn't want to let me edit, so here are a few comments
and ideas for the DevCamp that I will not be able to attend:

1. Build: my versioning scheme for `sac2c` is crude, but it has
   one benefit: monotonically increasing version numbers
   allow one to tell immediately how far you are behind
   the repostory, etc.

   I do not think that my scheme works well for other things, such as
   the SAC unit tests, stdlib, benchmarks, etc. It would be nice
   to have a consistent way to handle that numbering AUTOMATICALLY.

   Separating stdlib so that it does NOT have to rebuilt whenever
   someone adds a new primitive or changes an AST field would
   be GREAT. Right now, `git bisect` is effectively useless for
   any serious work, because you have to guess the appropriate
   stdlib build number, then rebuild it, too. This is a mug's game.

2. Testing: I do not think that putting a "test" target in the Makefile
   is a good idea: it is never going to be able to reflect YOUR
   specific changes without a lot of effort. There is also not
   much that can be done in "5min max" to cover the entire
   compiler.

   My existing unit test suite (`~/sac/testsuite/optimizations/*`)
   offers these capabilities:
   1. easy expansion as optimizations are added - `mkdir newopt`
   2. automated execution: `make -j42`
   3. includes both checking of result values AND checking
      of optimization-specific IL code values. E.g.,
      for CF, does X-X remove the subtract when X is AKS, but not
      when it is AKD? E.g., does WRCI produce RC(AAA) when
      it should?
   4. Is fairly fast, but could be faster if we cleaned up `sac2c`
      performance and fix a number of serious run-time problems,
      such as the RC(AAA) above.
   5. Ability to run a specific subset of tests when you change
      something local. E.g., AWLF, CF, LS...

   I recommend using this instead of attempting to make an all-singing,
   all-dancing single test script that will inevitably be outdated
   and inadequate.

   The Makefile could be MUCH improved. I don't really know they
   work for anything non-trivial. E.g., I would like the unit test 
   logfile(s) created by running Make to be copied, so that they can be
   archived. [They're relatively small, but do provide some insight.]

3. General problems:
   Recent compiler warnings in my stuff are intentional and
   are cage-rattlers for me; I'll remove
   them when I get back to town near month-end.

4. Memory leaks: One of the UVA folks wrote some code
   that does some of this, and I think that an hour or so of
   work on it would make it very easy to fault-isolate
   ast-related memory leaks. I sent email to Clemens on this
   topic some time ago, suggesting a trivial improvement
   to it, but do not know if he has taken
   any action on it, or if he intends to. At any rate, that looks
   to be more sac-compiler-writer-friendly than valgrind. And
   a lot faster.

5. More involved issues: I agree that tracing error messages
   (compiler and run-time) back to user-written source code
   is absolutely critical to making the compiler at all useful
   by humans. And by us.

   Similarly, reporting of optimization success/failure, at the
   source code level, is crucial. For example, had a recent datareuse
   failure been reportable, code faults in WRCI, DR, and other
   places could have been found automatically.

   DItto run-time performance measurements: E.g., "How many array
   allocations were performed in EACH user-defined function during
   the execution of this app?" NB. I want one answer per function,
   not a scalar. I also do not want a trace of ALL allocations,
   since we have no way to analyze those in a way that allows
   trace-back to the source code.

6. I want to see DOCUMENTATION of compiler internals as
   an integral part of ALL compiler development. E.g.,
   WRCI, REUSE, RWO, etc., had zero documentation at
   the file head ends on:

   - the intent of the optimization
   - examples of use of the optimization
   - examples that it can not handle, but perhaps could with
      some specified changes.

   The idea here is that we have a lot of people coming and going
   on the project, and when things break, we have no clue
   as to what the broken thing was supposed to do.

   AND, unit tests that should be adequate to perform automated
   code coverage on the optimization's code. [ I would be satisfied
   with S0 coverage...]

7. Performance: spend some time to find out where the sac2c
   bottlenecks are. I suspect that fixing a few places or functions
   would produce at least an order-of-magnitude faster compile
   times. Is valgrind a good way to do that?


--
Bob


