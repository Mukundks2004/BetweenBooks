---
title: "Type Theory Part III: Verifying Formal Specifications"
date: 2025-11-05 20:30:00 +1100

categories: [Technical]
tags: [lean,programming,maths,computer-science,technical,type-theory,leftpad,verification,formal-verification]
image:
    path: https://www.rmit.edu.au/content/dam/rmit/au/en/study-with-us-orderable/seo-info-pages/how-to-become/engineering/how-to-become-an-engineer-main-image-1220x732.jpg
---

Our previous ventures into type theory were all self motivated and highly theoretical. In this entry in the series we'll explore the utility of type theory in the real world. First we'll talk about requirements and what it means for an engineered product to be [verified](https://en.wikipedia.org/wiki/Formal_verification). We'll go on to write a [formal specification](https://en.wikipedia.org/wiki/Formal_specification) for a simple software and program it. Finally, we'll perform [software verification](https://en.wikipedia.org/wiki/Software_verification) on the specification using type theoretic [formal methods](https://en.wikipedia.org/wiki/Formal_methods), thereby guaranteeing the software's [correctness](https://en.wikipedia.org/wiki/Correctness_(computer_science)).

## What Is a Formal Specification

Engineers, including software engineers, design solutions to problems. A core, central part of the engineering process is clearly defining the problem and what it means for a solution to exist. Typically, this is formalized by writing what are called **functional requirements**. You can imagine the solution to the problem as a concept that inhabits a higher dimensional design space. There are dimensions for every measurable criteria of the solution, like budget, size and time. By writing requirements you constrain the design space and limit degrees of freedom. Provided that the solution meets the requirements, that it is within the parameters set by the engineer, you can be agnostic to the details- success is determined by the fact the constraints are satisfied and any unspecified implementation details or side effects are irrelevant. This concept of what it means philosophically to have succeeded at completing a task is so important to engineering that an entire subfield of engineering, [requirements engineering](https://en.wikipedia.org/wiki/Requirements_engineering) (part of [systems engineering](https://en.wikipedia.org/wiki/Systems_engineering)) is built around this.

## Writing Requirements

Requirements look different depending on the domain of the problem, whether it is conceptual, related to product design, management, solution architecture, mechanical or manufactured systems, software, etc. On top of this, requirements can fall in different buckets such as business requirements, user requirements, functional/non functional requirements and constraints/limitations. Luckily for us, our domain is a single software program so our requirements will be exclusively functional.

Writing out the requirements will feel mathemtical and algebraic- this is deliberate and the purpose of this is multifold. Firstly it is a nice logical progression from the previous exercise where our proofs were mathematical. Secondly, this makes requirements easy to both write out and prove since the observable/quantifiable effects of the program we will write are naturally mathematical, even though the program itself will not be. Thirdly, any sufficiently defined system, no matter the domain, will have requirements that are logically algebraic. This is foundational to the field of [constraints programming](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem), a conceptually related rigid/automated/computational variation of formal verification. In constraints programming degrees of freedom and unknown variables are literally given variable identifiers and an attempt is made to 'resolve' the system or find a solution that satisfies the constraints and optionally simultaneously optimizes a cost function. This representation of a system with requirememts can be used to derive a canonical/normal form, useful for comparing systems and uncovering potential hidden isomoprhisms between different systems that have the same requirements. Although this partially related to type theory through denotational semantics, it is beyond the scope of this article. Fourthly, mathematical definitions remove ambiguity, make the output clear and predictable, make testing easy, make communication of requirements easier and basically any other beneficial side effects related to rigidity and clarity.

The set of requirements is known as a specification or spec for short.

## Leftpad

The program we are writing is known as leftpad. We'll be writing it in lean4. Leftpad is conceptually simple. TW: this is the informal, imprecise, non-rigorous definition to familiarize the reader preceding the actual definition. Leftpad is a program that accepts 3 parameters: a list of elements $$L$$, an element $$x$$, and a length $$n$$ (I tried to do this without introducing variables but it is really difficult and harder to communicate). It returns a new list ending in the old list but of different length; the new list has many instances of $$x$$ to the left of $$L$$ such that the total length is $$n$$. If this length condition is unsatisfiable, as the length of $$L$$ was greater or equal to $$n$$, $$L$$ is returned. When viewed in an imperative fashion, it is acceptable to say the list has been 'padded' on the left with $$x$$ to make it $$n$$ long. Hence leftpad.

### Some Leftpad History

Despite being a trivial program to write in both imperative and functional styles, when leftpad functionality was required in node projects leftpad was commonly outsourced to the npm package '[left-pad](https://www.npmjs.com/package/left-pad)' rather than being implemented directly. In 2016, left-pad package was taken down from the registry due to an argument and 1000s of projects that depended on it including [Babel](https://babeljs.io/) and [React](https://react.dev/) where unable to be built.

Since then, leftpad has been built into js itself under a new name. [You can read more about the incident here.](https://en.wikipedia.org/wiki/Npm_left-pad_incident)

![Contents of leftpad at the time of its removal](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7b/Left-pad_0.0.3_contents.png/500px-Left-pad_0.0.3_contents.png).

### The Specification

Three things are required of a program to be leftpad. Using the variables above:
1. The length of the output is the maximum of $$n$$ and the length of $$L$$
2. The output ends with $$L$$
3. Denote the length of $$L$$ by '$$k$$'. The output starts with $$n - k$$ copies of $$x$$, where $$-$$ is [natural subtraction](https://en.wikipedia.org/wiki/Monus#Natural_numbers).

Note: we never specified anything regarding implementation details. Leftpad carries imperative connotations, that there should be some kind of 'repeated padding' implying a loop or similar. But our specification does not include this. The leftpad solution (program) may not be unique. There may be many different programs that all satisfy the leftpad requirements, yet have measurably different outputs. We are indifferent to such distinctions, these hypothetical programs are equivalent 'up to leftpad'.

While this spec is a great start and practically precise, it still uses the English language and there is arguably room for interpretation. Let us redo the previous section in lean for the pedants (me).

## Infrastructure

In order to write and verify leftpad (from first principles, relying only on the expressive power of lean and no standard libraries) we need to define the following data structures and operations. Technically these have their own specs too! We will use the accepted definition.

- Naturals (for measuring the length of lists)
    - Addition
    - Subtraction

- Booleans (for equality- two terms of the same type are either equal or not)
    - Logical AND

- Equality typeclass (our leftpad will be polymorphic so the generic parameter must be an instance of the equality typeclass to be comparable)

- List (duh)
    - isPrefixOf
    - isSuffixOf
    - length
    - repeat
    - toString

## Define Leftpad (lean)

With the foundations out of the way, we can define leftpad as follows:

Visually and logically it looks correct. We all hopefully understand that the goal now is to back this claim up with some machinery, to show our requirements have been met. 

## Tests vs Proofs

But how do we know our program has met the specification? Traditionally, developers would perform [TDD](https://en.wikipedia.org/wiki/Test-driven_development) and write [UT](https://en.wikipedia.org/wiki/Unit_testing), often before the actual implementation (analogous to how requirements are written before the system is built). This is a robust solution and still the industry standard. But it has the flaw of not ever ensuring complete coverage and of testing based on implementation and not based on requirements. [Gherkin](https://en.wikipedia.org/wiki/Cucumber_(software)#Gherkin_language) and [BDD](https://en.wikipedia.org/wiki/Behavior-driven_development) aim to, and arguably succeed at, solving the latter problem. But code coverage is still an issue. Other developments that have been made are [instrumentation](https://en.wikipedia.org/wiki/Instrumentation_(computer_programming)) and runtime analysis to determine test coverage, which is a step better in theory but in the case of complex logic the burden is on the developer to find a way to test the path that has not been tested to slowly add tests and crawl to 100% coverage, and even this only guarantees that all logical paths have been tested not all possible inputs. [Property testing](https://en.wikipedia.org/wiki/Property_testing) solves this problem practically through a heuristic approach where many guesses are made with a huge array of input values- but even this does not ensure complete correctness. The only way to gurantee that, is with a [proof](https://en.wikipedia.org/wiki/Computer-assisted_proof).

Remember from the previous articles, by the Curry-Howard Correspondence we can consider a proposition proved by encoding it in a type and constructing a term of the type, or a 'witness'. We will now proceed to encode the spec into types in lean and run the type checker to verify our implementation is correct.

## The Specification (Round II)


## Applications
Formally verifying software requirements as we have done is useful whenever there are non-negotiable needs that software must perform according to the spec. The more important the code is, the more useful formal methods like the above become- because there is a need to GUARANTEE that the software is correct. Here are some real world examples of critical systems that have been verified- not all of them have been verified with type theory, but TT is the most expressive (tied with HOL) and easier to use than HOL when your proofs are oriented around programs, involving data structures and are not just mathematical statements.
- [CompCert](https://www.absint.com/compcert/) is a formally verified optimizing C compiler. It was proven using [Rocq](https://en.wikipedia.org/wiki/Rocq) (a type theory based language). Stated on the website: "Its intended use is compiling safety-critical and mission-critical software written in C and meeting high levels of assurance."
- [sel4](https://sel4.systems/Verification/) is a formally verified OS kernel. It was proven using [Isabelle](https://en.wikipedia.org/wiki/Isabelle_(proof_assistant)) (a HOL based language). Stated on the website: "Its uniqueness lies in the formal mathematical proof that it behaves precisely as specified and enforces strong security boundaries for applications"
- NASA's [FRET](https://en.wikipedia.org/wiki/FRET_%28software%29) requirements engineering tool is built on the dependently typed language [PVS](https://en.wikipedia.org/wiki/Prototype_Verification_System)
- Intel and ARM model hardware such as processors as [FSMs](https://en.wikipedia.org/wiki/Finite-state_machine) or [RTL abstractions](https://en.wikipedia.org/wiki/Register-transfer_level) and formally verify them as explored [here](https://www.researchgate.net/publication/305253125_End-to-End_Verification_of_Processors_with_ISA-Formal)

## Recap

In this article we showed the practical applications of type theory. We used slightly more advanced lean language features to create (the infrastructure around and then) a non-trivial, practical program. Then we encoded the specification for this program in lean and proved that the spec was obeyed, verifying that our program was correct.

What a journey! Although it was entertaining, and different, it was far too practical for me. In the next installment we'll tackle some advanced and seemingly unrelated pure mathematical concepts that we can surprisingly bring to type theory and use to both further the bleeding edge and redefine foundations.

