<table width="100%">
<tr>
<td>
<img src="http://homepages.cwi.nl/~hills/images/logo.png" height="60px"/>
</td>
<td align="right">
<img src="http://homepages.cwi.nl/~pietrzak/img/1b_CWI_LogoPMS193.png" height="60px"/>
</td>
</tr>
</table>

# Hacking your DSL with Rascal
## Exercises
---
### Part I: First steps

#### 0. FizzBuzz

(See <http://c2.com/cgi/wiki?FizzBuzzTest>)

Write a program that prints the numbers from 1 to 100. But for multiples of three print “Fizz” instead of the number and for the multiples of five print “Buzz”. For numbers which are multiples of both three and five print "FizzBuzz".


<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Tips</strong>
<ul>
<li>[1..101] gives the list [1,2,3,...,100]</li>
<li>use <code>println</code> to print.</li>
</td></tr>
</table>

#### 1. Evolution: Adding *unless*

Add an unless statement which is to be used similar to `ifThen` statements: 

	unless (x > 1) { "What is your age?" age: int }
	
 *  add a production to **Question** in *QL.rsc*
 *  add a constructor to **Question** in *AST.rsc*
 *  add a tc rule to the type checker in *Check.rsc*
 *  add a normalize rule to the normalize in *Normalize.rsc* (note: desugar `unless` to `if(not(e), s)`)
 
Check in the IDE that the type checkers indeed signals errors in unless conditions and bodies, and that its conditions appear in the outliner.  

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Tips</strong>
<ul>
<li>implement <code>unless</code> analogous to <code>ifThen</code> in all cases</li>
</td></tr>
</table>

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Optional Exercises</strong>
<ol type="a">
<li>change the typechecker so that a warning is issued in the case of <code>ifThen(not(_), ...)</code>.</li>
<li>fix the outliner (<i>Outline.rsc</i>) so that unless conditions appears in the outline.</li>
<li>add conditional expression <code>x ? y : z</code> (affects: <i>QL.rsc, AST.rsc, CheckExpr.rsc, TypeOf.rsc, Expr2JS.rsc</i>)</li>
</ol>
</td></tr>
</table>

 
#### 2. Evolution: Adding date valued questions

Add support for date valued questions:

* add syntax to **QType** to allow date fields (*Lexical.rsc*)
* add new **QType** constructor for date types (*AST.rsc*)
* add new case to **type2widget** in *Compile.rsc* to generate DateValueWidgets (see *resources/js/framework/value-widgets.js*)

### Part II: Advanced examples

For this part of the session, we need to understand three important Rascal features: **visitors**, **locations** and **relations**.A visitor in Rascal is ...#### 3. Transformation: Desugaring *unless*

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Warm up</strong><br><br>
Use visit to:
<ol type="I">
<li>print out all labels in a form</li>
<li>count all questions (question/computed)</li>
</ol>
</td></tr>
</table>

Desugar:

* use `visit` to traverse and rewrite the Form
* use pattern matching to match on unless nodes.
* rewrite unless nodes to `ifThen` using `=>`

The desugar function is called before compilation so the compiler (*Compile.rsc*) does not have to be changed to support `unless`, even if no `normalize()` was used.


<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Optional Exercises</strong>
<ol type="a">
<li>add <code>unlessElse</code>, and desugar it to <code>ifThenElse</code>.</li>
</ol>
</td></tr>
</table>
---

#### 1. Build a name graph
