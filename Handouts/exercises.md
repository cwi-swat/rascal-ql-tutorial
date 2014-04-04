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

## Hacking your DSL with Rascal: Exercises
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
</ul>
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
<li>fix the formatter to pretty print <code>unless</code>.</li>
</ol>
</td></tr>
</table>


#### 2. Evolution: Date valued questions

Add support for date valued questions:

* add syntax to **QType** to allow date fields (*Lexical.rsc*)
* add new **QType** constructor for date types (*AST.rsc*)
* add new case to **type2widget** in *Compile.rsc* to generate DateValueWidgets (see *resources/js/framework/value-widgets.js*)

#### 3. Evolution: Conditional expression

Add conditional expression <code>x ? y : z</code> 

 *  add production to Expr (QL.rsc)
 	* 	Make sure it's low in the priority hierarchy i.e. x && y ? a : b  is equal to (x && y) ? a : b.
 *  add new `Expr` constructor in *AST.rsc*
 *  add new case to `typeOf` in *TypeOf.rsc*
 *  add new case to `tc` in *CheckExpr.rsc*
 *  add new case to `expr2js` in *Expr2JS.rsc*

#### 4. Transformation: Explicit desugaring of *unless* to *ifThen*

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>(Warm up) </strong> Use visit to:
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

<table border="1" width="100%" bordercolor="lightgrey" >
<tr><td>
<strong>Tips</strong><br/>
See examples of <code>visit</code> at:
<ul>
<li><i>Resolve.rsc</i></li>
<li><i>Outline.rsc</i></li>
</ul>
</td></tr>
</table>

<span></span>

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Optional Exercises</strong>
<ol type="a">
<li>add <code>unlessElse</code>, and desugar it to <code>ifThenElse</code>.</li>
</ol>
</td></tr>
</table>

---
### Part II: Advanced examples

<!--
For this part of the session, we need to understand three important Rascal features: **visitors**, **locations** and **relations**.A visitor in Rascal is ...
-->

#### 5. Analysis: Extract control dependencies

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Warm up</strong><br>
<ol type="I">
<li>use deep matching (using <code>/</code>) to find a variables in a form.</li>
<li>use deep match to find all question with label value (within the quotes) equal to name; make sure there are such labels in your test code.</li>
</ol>
</td></tr>
</table>

 * use the Node and Deps data types and nodeFor function shown below
 * visit the form, and when encountering `ifThen`/`ifThenElse`
 * record an edge (tuple) between each `Id` used in the condition and each `Id` defined in the body/bodies.
 *  use deep match (`/p`) to find `Id`s in expressions

For inspiration, a function to extract data dependencies (`dataDeps(Form f)`) is shown.

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Tips</strong>
<ul>
<li>Examples of deep match:
<ul>
<li><code>freeVars</code> in <i>Compile.rsc</i></li>
<li><code>typeEnv</code> in <i>Check.rsc</i></li>
</ul>
<li>first define a helper function <code>set[Id] definedIn(Question q)</code> that returns the defined names at arbitrary depth in a question (q).</li>
</td></tr>
</table>

#### 6. Analysis: Cycle detection
 
 * detect cycles in a form using `dataDeps` and `controlDeps`
 * first lift a `Deps` relation to `rel[str, str]`
 * use transitive reflexive closure `R*` 

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Optional Exercises</strong>
<ol type="a">
<li>compute the <code>Nodes</code> involved in a cycle (if any).</li>
<li>produce a <code>set[Message]</code> for such nodes and hook it up to the type checker (<i>Check.rsc</i>)</li>
</ol>
</td></tr>
</table>

#### 7. Implement a rename refactoring

##### 7.a. Analysis: Compute all locations referencing/referenced by a name location

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Warm up</strong><br><br>
Use visit to:
<ol type="I">
<li>call <code>resolve(loadExample("tax.tql"))</code>.</li>
<li>click on the locations to see where they point.</li>
<li>count the number of uses (references) and defs (declarations) (use <code>size</code> from the standard module <code>Set</code>)</ol>
</td></tr>
</table>

This exercise amounts to computing the equivalence relation of the use-def
relation `Ref.use` which is declared as `rel[loc use, loc def]`.
(See <http://en.wikipedia.org/wiki/Equivalence_relation>)

 * obtain the names relation by calling resolve on an AST (`Resolve.rsc`)
 * use a comprehension to compute the symmetric closure of a relation (symmetric closure means `<x, y>` in the relation if also `<y, x>`)
 * use `R*` to compute the reflexive, transitive closure of a relation (See <http://en.wikipedia.org/wiki/Reflexive_closure> and <http://en.wikipedia.org/wiki/Transitive_closure>)
 * use the "image" `R[x]` to compute all locs equivalent to *name*.

##### 7.b. Transformation: Substitute all names in the input source

* construct a renaming map `map[loc, str]` based on the result of 7a
* use the function `substitute(src, map[loc, str])` from `String`
* observe the effect of rename by selecting an identifier in a TQL editor, and richt-click, select Rename... in the context menu.

<table border="1" width="100%" bordercolor="lightgrey">
<tr><td>
<strong>Optional Exercises</strong>
<ol type="a">
<li>implement the rename refactoring, but now on ASTs. Use <code>format</code>) (Format to see the result of your refactoring.).</li>
<li>check as precondition that <i>newName</i> isn't already used. Retrieve the name at a location by  subscripting on src: <code>src[l.offset..l.offset+l.length]</code>.</li>
</ol>
</td></tr>
</table>

