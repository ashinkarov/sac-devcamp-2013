Here is what my short investigation regarding Merijn's example revealed.
So, after minimisation Merijn's code that produces error with false
locations is the following:

```
use Structures: {tod, +};
use StdIO: {print};

double work(double x)
{
    return x;
}

void task(int load)
{
    load = tod (load);
    sequential = 0.0;
    sequential += work (load);


    print (sequential, 0);
}

int main () { return 0; }
```

The error message produced by the compiler looks like this:
```
./b.sac 9 error: All instances of "task" contain type errors

error: line 0 in file b.sac:
error: inferred type double should match declared type int
compilation failed while Running type inference system, 1 error(s).
```

So in essence, it doesn't like the fact that the parameter is
redefined with a new type, but it fails to print the location of the
line `load = tod (load);`.
So I decided to look what kind of locations the compiler stores, and
as it seems that flattering and whoever generates `_type_conv_` calls
fails to set it up correctly in assignments.  See below:
the wrapper functions from the modules have correct locations see
`<line:col>` at the end of `wrapper`; the assignments that are
generated have locations `<0:0>` -- see the comments `/*
loc=<line:col>  */` (these are for assignments).

Then I would assume that `NTCCTprf_type_conv` fails to get them either.

```
/****************************************************************************
 * _MAIN::task(...) [ body ]
 ****************************************************************************/
#65: in [ --, Terminal::Terminal] le <> ge <>, #66: in [ --,
TermFile::TermFile] le <> ge <> _MAIN::task( Terminal::Terminal
_rso_6_TheTerminal { ,NN } , TermFile::TermFile _rso_5_stdout { ,NN }
, int load { ,NN } )
/*
 *  task ::  ---
 */
{
  unknown[*] _rso_5_stdout__SSA0_1 { , NN } ;  /* declared:
TermFile::TermFile */
  unknown[*] _rso_6_TheTerminal__SSA0_1 { , NN } ;  /* declared:
Terminal::Terminal */
  unknown[*] sequential__SSA0_1 { , NN } ;
  unknown[*] load__SSA0_2 { , NN } ;  /* declared: int */
  unknown[*] load__SSA0_1 { , NN } ;  /* declared: int */
  unknown[*] _flat_1 { , NN } ;
  unknown[*] _flat_0 { , NN } ;
  unknown[*] sequential { , NN } ;

  load__SSA0_1 = wrapper<242:0>:Structures::tod( load) ; /* loc=<11:5>  */
  load__SSA0_2 = _type_conv_( int, load__SSA0_1); /* loc=<0:0>  */
  sequential = 0.0; /* loc=<12:5>  */
  _flat_0 = wrapper<4:0>:_MAIN::work( load__SSA0_2) ; /* loc=<0:0>  */
  sequential__SSA0_1 = wrapper<49:0>:Structures::+( sequential,
_flat_0) ; /* loc=<13:5>  */
  _flat_1 = 0; /* loc=<0:0>  */
  _rso_6_TheTerminal__SSA0_1, _rso_5_stdout__SSA0_1 =
wrapper<356:0>:StdIO::print( _rso_6_TheTerminal, _rso_5_stdout,
sequential__SSA0_1, _flat_1) ; /* loc=<16:5>  */
  return( _rso_6_TheTerminal__SSA0_1, _rso_5_stdout__SSA0_1); /* loc=<0:0>  */
}
```
Having said that, there is a similar issue with CTI error reporting,
which uses `CTIerrorLine`, where locations have file (which is not
necessarily global.filename) and column, and in principle it would be
nice to switch to the new functionality that I've introduced which is
`CTIerrorLoc`, but the problem is that some of the existing code relies
on `global.linenum` and `global.filename` which work fine only in
traversals.  So, I've eliminated most of the simple cases, but there
are still about 10-15 files requiring unification.  

P.S. Yeah, `<0:0>` locations are getting zeroes only in the debug
version of the compiler, in the product version it would be undefined
values, which would be really misleading for any user :)
