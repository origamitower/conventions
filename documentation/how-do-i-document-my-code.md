How do I document my code?
==========================

Programming is inherently a social activity. It's made by humans, and produces
things for humans to use. It's essential that all of these people understand the
project. This is a communication problem, where several kinds of approaches have
to be employed. This article discusses communication between programmers, mainly
through documentation.

> **NOTE**
>
> This document is a work in progress. Information may be missing, and no
> section has been reviewed. Issues about this document should be reported on
> the [issue tracker](https://github.com/origamitower/conventions/issues).

## Table of Contents

  - [Why do we need documentation?](#why-do-we-need-documentation)
  - [Documentation with Meta:Magical](#documentation-with-metamagical)
    - [Social meta-data](#social-meta-data)
    - [Execution meta-data](#execution-meta-data)
    - [Organisation meta-data](#organisation-meta-data)
    - [Usage meta-data](#usage-meta-data)


## Why do we need documentation?

> “Thus, programs must be written for people to read, and only incidentally for
> machines to execute.”
>
> — Abelson and Sussman, in [Structure and Interpretations of Computer Programs](https://mitpress.mit.edu/sicp/full-text/book/book-Z-H-7.html)

Given that programming is a social activity, we'll have different people working
in the same code base, and different people using the artifacts produced by this
code. With more things being open source, this is truer than ever. These people
need to, somehow, communicate expectations and intent to each other such that
they may collaborate.

In a program, we're usually interested in communicating the following ideas:

  - **What is it?**  
    — A program is made out of several abstract concepts, and these concepts
    compose to express an idea. In order for one to understand the idea, they
    must first understand the concepts. This question can be asked while trying
    to understand both, and is ideally answered by the code itself.

    Code that clearly signals the ideas they're expressing is easier to follow,
    and thus easier to understand. That is, it succeeds at communicating the
    ideas that it aims to. This is achieved by
    [using the right abstraction for the solution being described](http://lambdatoast.com/programming/2014/09/20/the-point-of-abstraction.html).

    In some cases, however, people may not be familiar with the concept being
    expressed, even if the concept is clearly expressed in the program. In this
    case, they can either try to build a conceptual understanding by
    understanding how it works — so they try to come up with their own mental
    model for this concept based on how it works, — or they can be provided with
    a **conceptual documentation** that explains the concept to them, such that
    its implementation (how it works) becomes irrelevant to effectively
    communicate the idea.


  - **What does it do?**  
    — Sometimes we're not interested only in the conceptual understanding of the
    ideas expressed in the program, but also in how the computer will
    effectively execute the program. We ask these questions when we need to
    optimise a particular piece of code, for example, and it is also ideally
    answered through the code itself.

    In pretty much all cases we're interested in an answer to this question,
    we'll be dealing with **how the machine understands our code**. And
    sometimes we might want to provide higher-level descriptions of that to help
    programmers to get in the right mindset. We'll call this **execution
    documentation**.


  - **Why does it exist?**  
    — Besides understanding what a concept is, it's often important to
    understand the motivation behind that concept. These motivations allow us to
    decide when a concept should or shouldn't be used, and also to evaluate
    alternatives to that concept.

    This kind of intent is impractical to express in code itself (although
    things like dependent types bring us closer to that), so they're described
    in natural language itself. We'll call this **intent documentation**.


  - **How do I use it?**  
    — We need to understand how to use a particular concept in our
    programs. This question is partly addressed by language features, such as
    function signatures, interfaces, and types, and as much as possible we
    should try to rely on those features to answer this question. Sometimes,
    certain aspects of a concept can't be captured by the features the language
    offers, and for these we're again left with high-level, natural language
    descriptions. We'll call this **usage documentation**.


  - **What are the effects of using it?**  
    — Last, but equally important, we need to set people's expectations about
    some piece of functionality. Any observable effect or limitation needs to be
    properly communicated to the programmer, such that they can understand, in
    context, whether they should use it or not. Most languages have a pretty bad
    support for specifying expectations, with types being the usual way of doing
    it. When we can't specify these expectations in the code itself, we need to
    provide a natural language description of them. We'll call these
    **expectations documentation**.


Knowing which questions we need to provide answers for makes it much easier to
document our code, but we still need to know how to do that. The rest of this
document describes the specific approach to documentation used in Origami
Tower's JavaScript projects. Note that they are not a general approach that can
be applied to any other language, even if the principles can be useful for them.


## Documentation with Meta:Magical

Most of the different kinds of documentation described in the previous section
(and more) are attached to JavaScript objects using the
[Meta:Magical](https://github.com/origamitower/metamagical), and then queried in
different ways through the Meta:Magical browser.

Meta:Magical is a project that allows one to attach arbitrary meta-data to
JavaScript objects, and query that meta-data later at runtime. So one would be
able to look at particular pieces of documentation from the REPL, for example.

Documentation may be provided as Meta:Magical doc comments by using the
[Meta:Magical Babel plugin](https://github.com/origamitower/metamagical/tree/master/babel-plugin),
or by assigning an object to the `Symbol.for('@@meta:magical')` property.

```js
// Using the Babel plugin for doc comments

/*~
 * Composes two functions.
 *
 * The compose operation allows function composition. For example, if
 * you have two functions, `inc` and `double`, you can compose them
 * such that you get a new function which has the characteristics of
 * both.
 *
 *     const inc    = (x) => x + 1
 *     const double = (x) => x * 2
 *     const incDouble = compose(double, inc)
 *
 *     incDouble(3)
 *     // => double(inc(3))
 *     // => 8
 *
 * > **NOTE**  
 * > Composition is done from right to left, rather than left to right.
 *
 * ---
 * signature: compose(f, g)(value)
 * type: (β -> γ, α -> β) -> α -> γ
 * category: Combinators
 * tags: ["Lambda Calculus"]
 * stability: stable
 * platforms: ["ECMAScript"]
 * licence: MIT
 */
const compose = (f, g) => (value) => f(g(value))
```


### Social meta-data

Social meta-data describes the social aspects related to code ownership and
distribution. They're important in that they tell people if they can use or rely
on a particular piece of code.


  - `authors : Array<Author/String>` *(inheritable)*  
    — Provides a list of authors for the documented functionality. In general
    this will be just the name of the author, but it might also contain an email
    and/or website, formatted as `"Author Name <email@domain.com>
    (http://web.site.com)"`.

  - `licence : SPDX/String` *(inheritable)*  
    — Describes which licence the code is released under. These will ideally be
    [SPDX licence indexes](http://spdx.org/licenses/), such as `"MIT"`.

  - `since : SemVer/String` *(inheritable)*  
    — Describes which version introduced the functionality. While less useful
    when interacting with Meta:Magical through a REPL, this could be useful for
    documentation generated from the annotations and presented elsewhere. Should
    ideally be a version string conforming to
    [Semantic Versioning](http://semver.org/)

  - `deprecated : String`  
    — Provides the reasoning by which the functionality was deprecated, and
    ideally explains how the user should migrate their application to newly
    provided alternatives, if they exist.

  - `repository : URL/String` *(inheritable)*  
    — Provides an URL for the repository containing the source for the functionality. 

  - `stability : StabilityIndex/String` *(inheritable)*  
    — The stability index tells people how much they can rely on the
    functionality. Meta:Magical just uses
    [Node.js's stability index](https://nodejs.org/api/documentation.html#documentation_stability_index),
    where names are provided in lower case, e.g.: `"stable"`.


### Execution meta-data

Execution meta-data describes expectations about the execution of the code in
the machine.

  - `platforms : Array<String>` *(inheritable)*  
    — A list of platforms where the functionality works, like `["Node.js 4+", "Chrome 40+"]`.

  - `portability : String` *(inheritable)*  
    — Describes whether the functionality is portable (`"portable"`), and if not
    why it isn't portable (`"not portable. Relies on v8's special support for
    stack trace objects."`).

  - `complexity : String`  
    — Describes the algorithimic complexity of the functionality. Ideally all
    functionality that does not have trivial complexity should describe which
    complexity it runs on, for example: `"O(n), n is the number of elements in
    the array."`. Where it matters, also provide the complexity for trivial
    functions (e.g.: those that aren't usually constant time).


### Organisation meta-data

Organisation meta-data allows Meta:Magical to organise the output of meta-data
queries better, and also support filtering.

  - `category : String`  
    — Categorises the functionality such that similar functionality can be
    grouped together. Common categories used in JavaScript code are described
    later in this document. Each functionality has a single category, but it may
    have many associated `tags`.

  - `tags : Array<String>`  
    — Provides keywords that can be associated with the
    functionality. Meta:Magical uses these to help users search for
    functionality, but they have no bearing on how information is displayed.

  - `name : String`  
    — The name to display when showing data about the functionality.

  - `module : String`  
    — Describes which module this functionality belongs to. This is the path to
    the module, from the root of the package, including the package name — in
    other words, the same thing people would put in their `require()` calls.

  - `belongsTo : Object`  
    — When provided, tells Meta:Magical where the functionality is defined. This
    is used by Meta:Magical to support inheriting meta-data from other objects,
    so you don't need to repeat things like `licence`, `authors`, etc. in all
    methods of an object.


### Usage meta-data

Usage meta-data documents how to use a particular piece of code, and what to
expect from using that code.

  - `signature : String`  
    — Provides a signature for the functionality. These are similar to how you
    define or invoke the functionality. Common examples:

    - **Function**: `compose(f, g)(value)`
    - **Function with defaults**: `read(fileName[, encoding = 'utf8'])`
    - **Method**: `.match(pattern)`
    - **Constructor**: `new Point(x, y)`
    - **Class**: `class Branch extends Tree`
    - **Object**: `List :> Collection`

  - `type : String`  
    — Describes the type of the functionality. The type notation is not
    specified yet, and Meta:Magical treats it as an arbitrary String. Eventually
    it might settle on something similar to
    [RDL](https://github.com/plum-umd/rdl) and support type queries, like
    Hoogle.

  - `throws : { String -> String }`  
    — Describes under which situations the functionality throws exceptions. The
    value is an object where the key is the kind of exception thrown, and the
    value a description of when it's thrown and why.

