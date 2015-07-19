How do I open source a new project?
===================================

More often than not we don't find a satisfactory solution for the problems we're
dealing with in our programs, and so we sit down and devise our own. Since that
solution may be helpful to other people facing the same problem, we would also
like to share it so other people can benefit from our work.

## Table of Contents

 -  [Why should other people care?](#why-should-other-people-care)
 -  [Why would I *not* use your project?](#why-would-i-not-use-your-project)
 -  [Can people use it?](#can-people-use-it)
     -  [Does it run at all?](#does-it-run-at-all)
     -  [Should I use the project *today*?](#should-i-use-the-project-today)
     -  [Anything I should be aware of when using it?](#anything-i-should-be-aware-of-when-using-it)
 -  [Great! I want to use it. How do I do that?](#great-i-want-to-use-it-how-do-i-do-that)
     -  [How do I install it?](#how-do-i-install-it)
     -  [How do I make sure it works on my platform?](#how-do-i-make-sure-it-works-on-my-platform)
     -  [How do I figure out which pieces I need to solve **my** problem?](#how-do-i-figure-out-which-pieces-i-need-to-solve-my-problem)



## Why should other people care?

Since you want people to use your work, the very first question that you need to
answer is: “Why should people care about this project?” This motivation is more
important than the project itself. If people don't know why they should care
about your project, then they won't be looking at it in the first place.

You might think “Oh, but it's so obvious why people should care about this!”,
but if that were the case, someone would have written it before you. The thing
is: **something** motivated you to go write this project because nothing else
did what you're doing, in the way you're doing. You should communicate that to
the people who could be interested in using your project so they can ponder
whether your proposed solution fits their use case or not.

A good way to answer this question is to place the motivation in your [README][]
file, which is one of the first places people will look for information. Similar
to an article or research paper, you'd start describing what your project
solves, and then providing a motivation to why people should be interested in
it.

Here's an example:

```markdown
# core.arity

This module allows restricting the arity of functions in JavaScript.

## Why?

Since all functions in JavaScript are variadic, programmers often take advantage
of this fact by providing more arguments than what a function takes, and the
callee can just ignore them. With curried functions, calling a binary function
with three arguments ends up invoking the return value of the function with the
extra argument!

    var curry = require('core.lambda').curry;
    
    function add(a, b) {
      return a + b;
    }
    
    var cadd = curry(2, add);

    [1, 2, 3].reduce(function(result, item, index, array) {
      return add(result, item)
    }, 0);
    // => 6

    [1, 2, 3].map(cadd, 0) // => Error: 1 is not a function
    
To fix this, one would need to wrap the curried function such that the wrapper
only passes two arguments to it, and ignores the additional ones:

    var bcadd = function(a, b){
      return cadd(a)(b)
    }

    [1, 2, 3].map(bcadd)  // => 6

The `core.arity` module automates that by providing wrappers for common arities
for you, so you don't need to write it yourself:


    var binary = require('core.arity').binary;
    
    [1, 2, 3].map(binary(cadd)) // => 6
```

Here we tell the potential user of the library what problem the library solves,
and quickly follow with why they would be interested in a solution for that
problem. In the motivation examples are used to provide a concrete use case that
people can instantly connect to, rather than trying to come up with one from a
series of words talking about generic things. You can describe how your solution
generalises a particular problem later.

Your motivation should be short and concise. People shouldn't have to read a 10
pages academic paper, or a 400 pages book before they're able to figure out
whether your project will help them or not. Sometimes, however, you might want
to extend the motivation a bit by comparing it with existing projects in the
same problem domain, when it's not obvious why someone would choose your project
over the rest.

If I were to describe why one would choose the [Specify][] framework over
Mocha or Tape, which are well-known and popular choices in the Node
community, I could write the following:

```markdown
# Specify

Specify is a cross-platform library for defining and running tests using a BDD
style. It supports pluggable assertions, custom reporters, and custom
interfaces.


## Why not <insert-popular-testing-library-here>?

Mocha, and other popular BDD testing frameworks, have been designed with a
particular environment in mind (Node for Mocha, Browser for Jasmine), so while
they can be extended, it's often awkward to use them in different
environments. In particular, running Mocha under CIs like Testling requires
specific Mocha extensions.

Furthermore, most popular testing frameworks and libraries (like Mocha and Tape)
are designed for asynchronous testing with callbacks. This isn't a deal breaker,
but it's a minor inconvenience since one would need to provide wrappers for
using Promises or Tasks. Specify uses Tasks, and provides wrappers for other
styles out of the box, which makes just writing tests simpler.
```


## Why would I *not* use your project?

As important as the motivation for using your project is any drawbacks that
people should be aware of. Truth be said, your project will not be better than
all of the alternatives in all possible contexts (unless no alternative exists,
in which case the comparison doesn't make sense). Your users should be aware of
this, so they can make a better informed decision about whether to use your
project or not.

Drawbacks might include performance or efficiency problems, in which case you
might want to tell people why they are there. Maybe you've decided that
simplicity or extensibility were more important than raw performance? That's a
reasonable decision, but your user might be looking for an efficient solution
instead and they wouldn't be able to use your project in that context.

Or maybe you've done the opposite, and decided to forgo purity for the sake of
performance. For example, suppose you're designing a `Map` implementation for
JavaScript, and the major difference from the standard shim is that your
implementation has O(1) lookups. Well, from that description alone, it would be
obvious for anyone that they should be using *your* project, because why would
they use anything else when your project is clearly more efficient?

Whatever the considerations are, that's what this section is supposed to tell
your users:


```markdown
# JSMap

A mutable Map<Object, Object> implementation for JS that supports O(1) lookups.


## Drawbacks

JSMap is able to have O(1) lookups by mutating the objects inserted in it and
adding a special `__id` key, with a unique identifier that is used for looking
up information for later retrieval. If you need the additional efficiency
guarantees, mutating the keys may be OK.

If you don't want key objects to be mutated, you can use ES6-shim's Map, which
has O(n) lookups on objects, but doesn't mutate keys.
```


## Can people use it?

At this point, the person has decided that your project can help them solve the
problems they're experiencing, but they do not yet know if they can use your
project or not. This is a very important question, which people usually don't
even bother trying to answer, and it makes the experience of dealing with your
project frustrating.


### Does it run at all?

There are a few major questions that will dictate whether the person can use
your project or not. The first one is: “Does this project run on the platform
I'm targeting?”. If that person needs their program to run on Linux, but your
project only works on Windows, they'll be in to a very disappointing journey if
you don't tell that upfront.

But platforms have to be considered even outside of the world of operating
systems. In Browsers, if you're targeting only modern browsers, you have to tell
people that your project will not work on IE7. If you're targeting Node, you'll
want to tell people that it only works on Node 0.12+. Or if you're targeting
Windows, you might want to tell people that it only works on Win7 and Win8
64bit.

“I haven't tested my project in platform X, but it should work in theory. Do I
say it works on platform X?” The answer, if you care about your users'
experience, is a resounding **no, you should not**. If you can't guarantee that
your project work on Platform X, then you can't say that it works there. Telling
someone that it works just so they can find it doesn't makes it an even more
frustrating experience than not telling them anything about supported platforms
in the first place. You can, however, say that your project *might* work on
Platform X, but it hasn't been tested. That way the user can decide if they
should take the time to try it or not.

Here's an example of how to provide platform information:

```markdown
# net.http-client

( ... )

## Platform

Node 0.12+, IE8+, Safari 5+, and recent versions of Chrome, Firefox and Opera
have been tested and work. Mobile browsers and older versions of Node, Safari,
Chrome, Firefox and Opera might work with this library, but have not been
tested.

For Safari 5, Safari6, IE8, and IE9 it's necessary to use the `es5-shim` library
to provide ES5 functionality that's lacking in those browsers.

Cross-origin requests are not supported in IE8 or IE9 (patches for using
XDomainRequest are welcome).

IE10 and Safari have limitations on using CORS (see
http://caniuse.com/#feat=cors).
```

Note that it provides not only the platforms that are strictly supported, but
the ones where things might still work (although there are no guarantees of
such). It also lists, on the supported platforms, any limitations and
restrictions that they might have, so people are aware of those problems when
deploying their application on those platforms.


### Should I use the project *today*?

Besides being able to use the library at all, people are also faced with the
question of whether they should be using your project today, or whether they
should wait until it has matured more. For example, some applications (like
Chrome), have beta channels, where people can run the next release before it
gets officially released. Projects also have these kinds of testing versions,
where earlier adopters can use things before they are officially done.

For people who need to deploy production applications, they'll generally prefer
stable solutions, so they don't need to worry about debugging your library in
addition to debugging their own application code. But some people are more
adventurous, and depending on your project, using an early-release version might
out-weight the cost of coming up with the solution themselves.

Whether people are willing to use early-releases or not, you still have to tell
them in which state your project is, and any known problems they should be aware
of if they exist. That will help your user make a decision of using your project
or not, without getting mad at you a month later when things start randomly
breaking.

For software projects, [semantic versioning][semver] is a great way of showing
the general stability of the project. Your potential users can look at a single
piece of information and see whether your project is in the experimental stage
(<1.0.0), in an unstable stage, but with stable versions that they can use
(e.g.: 1.2.3-beta2), or in a stable state (e.g.: 1.2.3).

While semantic versioning is great, it's not fine-grained enough. What if your
potential user is only interested in a particular sub-module of your project,
and wants to know in which state that module is? This is something that Node's
[stability index][] can inform users of. By noting whether each individual
module is stable or not, the user can figure out what they can use, and what
they should avoid using for now.


### Anything I should be aware of when using it?

Lastly, it's important for people to know if there are any outstanding problems
that might prevent someone from using the project (or parts of it), together
with workarounds, if they exist. It is okay for these descriptions to exist in
an issue tracker, but they should be linked from the README when applicable, so
people can be aware of blocking problems before trying out anything else.


## Great! I want to use it. How do I do that?

The user has decided that your project solves the problem they need to solve,
and they've also asserted that they can use your project in the context they
need to run it. That's great! You've got yourself a new user!

Too bad your user is now completely lost and can't figure out how they can use
your project to solve their problems.

At this point, it's essential to answer a few questions that your user will
have:

 -  “How do I install this in my platform?”
 -  “How do I make sure this works in my platform?”
 -  “How do I figure out which pieces I need to solve **my** problem?”

Let's tackle each of these questions separately, since there's a lot to be said
about each one of them.


### How do I install it?

Before people can use your project, they have to be able to install it on their
platform. This usually consists of downloading some distribution artifact and
then loading that artifact somehow in the platform they're targeting.

This section should include prominently the recommended way of installing your
project, and should also include sections for other supported ways of installing
it.

It should also be noted that this section should include information on how to
install your projects on **all** platforms you strictly support, as stated in
the "Platform Support" section of your project. It should also include
information on how to perform the installation using the native tools in those
platforms, even if you recommend people to use tool X for that (in which case
you also need to provide information on how to get tool X).

For example, if you're distributing a JavaScript library:

```markdown
# My Library

A short description.

## Platform support

Browsers (IE8+, Chrome, Safari 7+, Firefox, Opera), Node 0.10+.


## Installing

The recommended way is to install My Library through [npm][]:

    $ npm install my-library

> **NOTE**
>
> If you don't have npm, you'll need to install Node.js and npm in your system
> before installing My Library. For browsers, you can follow the alternative
> installation instructions instead.

In the browser, you will need to use [Browserify][] to use the module.


### Using My Library without modules

If you're not already using Browserify for modules, you can
[download a pre-compiled bundle][], or create a browser bundle by running:

    $ npm install browserify --save-dev
    $ ./node_modules/.bin/browserify -r my-library -s MyLibrary > my-library.js

After that, you can load My Library by just adding a `<script>` tag and using
the `MyLibrary` global object:

    <script src="/path/to/my-library.js"></script>
    <script>
      MyLibrary.doThings();
    </script>
```

In some contexts you won't have a package manager that solves the prerequisites
problem for you. In those cases you need to tell people what they need before
they can install your project, and how they can get what they need on all
different platforms you support.

For example:

```markdown
## BigInt

Native module providing arbitrary precision integers for Node.

## Installing

### Prerequisites

You'll need an environment correctly configured for compiling Node native
modules (a C++ compiler toolchain, such as CLang or GCC on Linux, or MSVSC++ on
Windows). See the [node-gyp's installation] documentation for more information
on that.

### Dependencies

BigInt depends on the [libgmp][] library, which you'll need to install in your
system and configure it so the linker can find it.

On Ubuntu you can install libgmp through apt:

    $ sudo apt-get install libgmp3-dev

On Arch you can install libgmp through pacman:

    $ sudo pacman -Ss libgmp-dev

On Mac with [Homebrew][]:

    $ brew install gmp

For all other platforms, you can try finding a suitable package on your package
manager, or [install from source][] using the information provided by the libgmp
package.

### Install with npm

To install BigInt with npm:

    $ npm install bigint
```


[README]: ./how-to-write-a-good-readme.md
[Specify]: http://specify.origamitower.com/
[semver]: http://semver.org/
[stability index]: https://nodejs.org/api/documentation.html#documentation_stability_index
