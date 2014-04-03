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

---
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

#### 4. Analysis: Extract control dependencies

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Warm up</strong><br><br>
Use visit to:
<ol type="I">
<li>use deep matching (using /) to find a variables in a form.</li>
<li>use deep match to find all question with label value (within the quotes) equal to name; make sure there such labels in your test code.</li>
</ol>
</td></tr>
</table>

 * use the Node and Deps data types and nodeFor function shown below
 * visit the form, and when encountering ifThen/ifThenElse
 * record an edge (tuple) between each Id used in the condition and each Id defined in the body/bodies.
 *  use deep match (/p) to find Ids in expressions

For inspiration, a function to extract data dependencies (`dataDeps(Form f)`) is shown.

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Tips</strong>
<ul>
<li>first define a helper function set[Id] definedIn(Question q) that returns the defined names at arbitrary depth in a question (q).</li>
</td></tr>
</table>

#### 5. Analysis: Cycle detection
 
 * detect cycles in a form using dataDeps and controlDeps
 * first lift a Deps relation to rel[str, str]
 * use transitive reflexive closure R* 

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Optional Exercises</strong>
<ol type="a">
<li>compute the Nodes involved in a cycle (if any).</li>
<li>produce a set[Message] for such nodes and hook it up to the type checker (Check.rsc)</li>
</ol>
</td></tr>
</table>

#### 6. Implement a rename refactoring

##### 6.a. Analysis: Compute all locations referencing/referenced by a name location

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Warm up</strong><br><br>
Use visit to:
<ol type="I">
<li>call <code>resolve(loadExample("tax.tql"))</code>.</li>
<li>click on the locations to see where they point.</li>
<li>count all questions (question/computed).</li>
</ol>
</td></tr>
</table>

This exercise amounts to computing the equivalence relation of the use-def
relation `Ref.use` which is declared as `rel[loc use, loc def]`.
(See http://en.wikipedia.org/wiki/Equivalence_relation)

 * get the use relation from Refs, returned from resolve (Resolve.rsc)
 * use a comprehension to compute the symmetric closure of a relation (symmetric closure means <x, y> in the relation if also <y, x>)
 * use R* to compute the reflexive, transitive closure of a relation
 * use the "image" R[x] to compute all locs equivalent to `name`.

##### 6.b. Transformation: Substitute all names in the input source

* construct a renaming map map[loc, str] based on the result of 6a
* use the function substitute(src, map[loc, str]) from String
* observe the effect of rename by selecting an identifier in a TQL editor, and richt-click, select Rename... in the context menu.

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Optional Exercises</strong>
<ol type="a">
<li>implement the rename refactoring, but now on ASTs. Use format (Format to see the result of your refactoring.).</li>
<li>check as precondition that `newName` isn't already used. Retrieve the name at a location by  subscripting on src: src[l.offset..l.offset+l.length].</li>
</ol>
</td></tr>
</table>

