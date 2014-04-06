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

## Hack your DSL with Rascal: Exercises

Tijs van der Storm, Pablo Inostroza Valdera, CWI

---
### Part I

Before starting coding, make sure you have opened a Rascal console associated with the project `RascalQLTutorial` (right-click on any Rascal file in the project and select 'Start console'). Then, in the console, do:

* `import exercises::ImportThis;`
* `import exercises::Snippets;`
* past statements from *exercises/Snippets.rsc* and see what happens.

The exercises can be complete by directly editing *exercises/Part1.rsc* and *exercises/Part2.rsc*. 

#### 0. FizzBuzz

(See <http://c2.com/cgi/wiki?FizzBuzzTest>)

Write a program that prints the numbers from 1 to 100. But for multiples of three print “Fizz” instead of the number and for the multiples of five print “Buzz”. For numbers which are multiples of both three and five print "FizzBuzz".


<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Tips</strong>
<ul>
<li>[1..101] gives the list [1,2,3,...,100]</li>
<li>use <code>println</code> to print.</li>
</ul>
</td></tr>
</table>

#### 1. Adding *unless*

Add an unless statement which is to be used similar to `ifThen` statements: 

	unless (x > 1) { "What is your age?" age: int }
	
 *  add a production to **Question** in *QL.rsc*
 *  add a constructor to **Question** in *AST.rsc*
 *  add a tc rule to the type checker in *Check.rsc*
 *  add a normalize rule to the normalize in *Normalize.rsc* (NB: the semantics of  `unless(e, s)` is equivalent to `if(not(e), s)`)
 
Check in the IDE that the type checker indeed signals errors in unless conditions and bodies, and that its conditions appear in the outliner.  

<table border="1" width="100%" bordercolor="lightgrey" >
<tr><td>
<strong>Tips</strong>
<ul>
<li>implement <code>unless</code> analogous to <code>ifThen</code> in all cases</li>
</ul>
</td></tr>
</table>
<span></span>
<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Optional Exercises</strong>
<ol type="a">
<li>change the typechecker so that a warning is issued in the case of <code>ifThen(not(_), ...)</code>.</li>
<li>fix the outliner (<i>Outline.rsc</i>) so that <code>unless</code> conditions appears in the outline.</li>
<li>fix the formatter (<i>Format.rsc</i>)to pretty print <code>unless</code>.</li>
</ol>
</td></tr>
</table>


#### 2. Date valued questions

Add support for date valued questions:

* add syntax to **QType** to allow date fields (*Lexical.rsc*)
* add new **QType** constructor for date types (*AST.rsc*)
* add new case to **type2widget** in *Compile.rsc* to generate DateValueWidgets (see *resources/js/framework/value-widgets.js*)

#### 3. Conditional expressions

Add conditional expression <code>x ? y : z</code> 

 *  add production to Expr (*QL.rsc*)
 	* 	Make sure it's low in the priority hierarchy i.e. `x && y ? a : b`  should be parsed as `(x && y) ? a : b`.
 *  add new `Expr` constructor in *AST.rsc*
 *  add new case to `typeOf` in *TypeOf.rsc*
 *  add new case to `tc` in *CheckExpr.rsc*
 *  add new case to `expr2js` in *Expr2JS.rsc*

### Part II

#### 4. Explicit desugaring of *unless* to *ifThen* using `visit`

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Warm up</strong>
<ol type="I">
<li>use <code>visit</code> print out all labels in a form</li>
<li>use <code>visit</code>count all questions (question/computed)</li>
</ol>
</td></tr>
</table>

Explicit desugaring of `unless`:

* use `visit` to traverse and rewrite the Form
* use pattern matching to match on unless nodes.
* rewrite unless nodes to `ifThen` using `=>`

The desugar function is called before compilation so the compiler (*Compile.rsc*) does not have to be changed to support `unless`, even if no `normalize()` was used.

<table border="1" width="100%" bordercolor="lightgrey" >
<tr><td><strong>Tip</strong><br/>
<ul>
<li>See examples of <code>visit</code> in <i>Resolve.rsc</i> and <i>Outline.rsc</i>
</li>
</ul>
</td></tr>
</table>

<span></span>

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Optional</strong>
<ol type="a">
<li>add <code>unlessElse</code>, and desugar it to <code>ifThenElse</code>.</li>
<li>write a transformation using <code>visit</code> to simplify algebraic  expressions (e.g.,  <code>1 * x</code>, <code>0 + x</code>, <code>true && x</code>, <code>false && x</code>, etc.).
</ol>
</td></tr>
</table>


#### 5. Extract data dependencies

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Warm up</strong><br>
<ol type="I">
<li>use deep matching (using <code>/</code>) to find all variables (<code>Id</code>) in a form.</li>
<li>use deep match to find all question with label value (within the quotes) equal to name; make sure there are such labels in your test code.</li>
</ol>
</td></tr>
</table>

A computed question is dependent on the questions it refers to in its expression. Such dependencies can be represented as a binary relation (a set of tuples). The goal of this exercise is to extract such a relation.


 * use the `Node` and `Deps` types and `nodeFor` function shown in (*Dependencies.rsc*)
 * visit the form, and when encountering a computed question
   record edges to the `Deps` graph to record a data dependency. 
 * use deep match (`/`) to find `Id`s in expressions

 
<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Tips</strong>
<ul>
<li>check out examples of deep match in <i>Compile.rsc</i>
and <i>Check.rsc</i></li>
</li>
<li>
 have a look at <code>controlDeps</code>, defined in (<em>Dependencies.rsc</em>) for inspiration
</li>
<li>use the function <code>visualize(Deps)</code> (<em>Visualize.rsc</em>) to visualize the result of your
  data dependency graph. Click on nodes to see the location they
  correspond to. 
</li>
</td></tr>
</table>

