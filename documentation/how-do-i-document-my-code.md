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

  - **What is it?** — A program is made out of several abstract concepts, and
    these concepts compose to express an idea. In order for one to understand
    the idea, they must first understand the concepts. This question can be
    asked while trying to understand both, and is ideally answered by the code
    itself.

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


  - **What does it do?** — Sometimes we're not interested only in the conceptual
    understanding of the ideas expressed in the program, but also in how the
    computer will effectively execute the program. We ask these questions when
    we need to optimise a particular piece of code, for example, and it is also
    ideally answered through the code itself.

    In pretty much all cases we're interested in an answer to this question,
    we'll be dealing with **how the machine understands our code**. And
    sometimes we might want to provide higher-level descriptions of that to help
    programmers to get in the right mindset.


  - **Why does it exist?** — Besides understanding what a concept is, it's often
    important to understand the motivation behind that concept. These
    motivations allow us to decide when a concept should or shouldn't be used,
    and also to evaluate alternatives to that concept.

    This kind of intent is impractical to express in code itself (although
    things like dependent types bring us closer to that), so they're described
    in natural language itself. We'll call this **intent documentation**.


  - **How do I use it?** — We need to understand how to use a particular concept
    in our programs. This question is partly addressed by language features,
    such as function signatures, interfaces, and types, and as much as possible
    we should try to rely on those features to answer this question. Sometimes,
    certain aspects of a concept can't be captured by the features the language
    offers, and for these we're again left with high-level, natural language
    descriptions. We'll call this **usage documentation**.


  - **What are the effects of using it?** — Last, but equally important, we need
    to set people's expectations about some piece of functionality. Any
    observable effect or limitation needs to be properly communicated to the
    programmer, such that they can understand, in context, whether they should
    use it or not. Most languages have a pretty bad support for specifying
    expectations, with types being the usual way of doing it. When we can't
    specify these expectations in the code itself, we need to provide a natural
    language description of them. We'll call these **expectations documentation**.


Knowing which questions we need to provide answers for makes it much easier to
document our code, but we still need to know how to do that. The rest of this
document describes the specific approach to documentation used in Origami
Tower's JavaScript projects. Note that they are not a general approach that can
be applied to any other language, even if the principles can be useful for them.


