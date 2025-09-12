---
title: Halmos' Set Theory- a Review
date: 2025-01-31 13:30:00 +1100

categories: [Books Film and Music]
tags: [maths,review,set-theory,book,halmos,pedagogy]
image:
    path: /assets/img/principia.png
---

Recently my grandparents visited from overseas, and they brought with them a treasure trove of literary and mathematical wonders. The thinnest and most approachable of these was the humble but classic *Set Theory* by Paul R Halmos.

It is a truth universally acknowledged that a single man after completing a good book must be in want of writing a review, and Halmos' publication after all has its own fair share of academic controversy. Scholars both renowned and underground have said everything there is to be said about this book, from praise to denouncement. Here is my take, as a (sort of) undergraduate with little foundation in mathematial logic.

## Naive vs Axiomatic

One of the first things users are confronted with when reading this text is the decision to accept this text as either a naive or axiomatic one. Naive set theory as a discipline refers to the set theory that we are taught in early undergrad- it delineates an informal approach to sets, often relying heavily on intuition and natural language rather than formal statements. Axiomatic set theory on the other hand is defined by its rigorous structure and definitions of the fundamental axioms that constitute set theory, such as the the axiom of extensionality, the axiom of pairing and the axiom of the empty set. The text appears not to leave any room for confusion or misinterpretaion, making clear the intentions of the author in the title itself. However, when turning to the very contents page of the book most readers (myself included) are confronted by chapters bearing names prefixed by "The Axiom of".

![Contents](/assets/img/set_theory_contents.png)

In fact, this confusion is only worsened by the author's attempt at clarifying this inconsistency. He says in the preface:

> In set theory "naive" and "axiomatic" are contrasting words. The present treatment might best be described as axiomatic set theory from the naive point of view. It is axiomatic in that some axioms for set theory are stated and used as the basis of all subsequent proofs. It is naive in that the language and notation are those of ordinary informal (but formalizable) mathematics.

Which only made my head hurt even more as I tried to linearly progress through the book. Returning, I now understand how exactly something can be both naive and axiomatic simultaneously- it is axiomatic in the literal sense of the word, in that it presents the english translations of the axioms. It is naive in all other regards. In retrospect, the title of the book is appropriate- but the decision to name the axioms is still preferable and in fact easier (imo) than committing to being truly naive and simply explaining set theory axiom-less, as a contiguous block or in text segmented arbitrarily, as the inherent structure of set theory and logical progression chosen is educationally valuable. I could have made the choice no better myself.

## Everything Is a Set  

This is mostly a consequence of set theory itself, but also heavily aided by Halmos in his writing style: every "thing" in formal mathematics is a set. Operations on sets produce more sets, and other objects that are frequently used in conjunction with sets or on sets are, under the hood, sets. I agree with the author's decision not to obscure this, and can liken it to the same pedagogical principle that compels univiersities to teach programming in C before moving on to higher level languages: the lack of management leaves no ambiguity in what more abstract ideas actually are concretely, or if the abstractions are even allowed to exist (more on this later). Analogously, you don't want to teach a foreach loop in C# without first understanding what an iterator or while loop is, since that's the "brain" behind how the loop works. Here are some examples of things that (once understood) we refer to as a single, undecomposable, atomic black box despite actually sets under the hood! They might start simple- it is quite easy to think of an "unordered pair" as a set with two elements- but very quickly we enter territory where even seasoned mathematicians may find themselves asking "Really? *That's* a set??"

- Intersections and Unions of Sets
- Unordered pairs
- Complement of a set
- Symmetric difference of sets
- Cartesian products of sets
- Powersets
- Ordered pairs
    - Tuples
- Relations
- Functions
    - Domain of a function
    - Range of a function
    - Inverse functions
    - Composition of functions
    - Characteristic functions and projections
- Index Sets
- Families
- The naturals, and some arithmetic (how naturals behave under addition, multiplication, exponentiation in terms of set operations)
    - Ordinals
    - Cardinals

And although not explicitly detailed, it is heavily implied that had the book gone on longer the next topics would have had to do with constructing integers, the rationals and the reals from set theoretic postulates. The fact that everything is a set is quite reminiscent of how everything in programming is actually a 1 or a 0, and using words to describe entities like classes and generic types or even bigger, more complex ideas such as "webpages" or "databases" is abstracting over those ones and zeroes. It is critical that we have names for concepts that then interact with other concepts to produce more concepts, defining everything in terms of sets is as useless as writing all modern software in machine code. Abstractions are necessary, no one is denying it. But you can only abstract if there is something to abstract over.

## Take Nothing For Granted

On that note, remember that it is equally if not more important to know what things really are for a number of reasons- not least of which is fundamentally knowing if the thing exists or not. If this sounds a bit bizarre, I can quote the example where we prove the recursion theorem.

> Recursion theorem: If $$a$$ is an element of a set $$X$$ and if $$f$$ is a function from $$X$$ to $$X$$ then there exists a function $$u$$ from $$\omega$$ into $$X$$ such that $$u(0) = a$$ and such that $$u(n^{+}) = f(u(n))$$ for all $$n$$ in $$\omega$$.

If you were following, the first thing you would have said is... "wtf??" Of course such a function exists! There are so many of them and we have been using them since primary school! For example, the factorial function. Do I mean to say that the factorial function doesn't exist? Well of course not. All this is doing is proving that mathematically, recursive definitions are valid if they match the form specified in the recursion theorem.

But one cannot deny that on a certain level, the requirement that everything must be proven is strange and unnervingly unnecessary. The text takes nothing for granted and numerous times asks the users to prove not just that given certain circumstances that a number exists (some of you might be used to finding examples as proofs in highschool)- but that a combination of mathematical properties and verbal descriptions produce a function, or even a set that exists! Presumably as opposed to one that doesn't. For instance:

> If, similarly, $$X$$ has an element $$a$$ such that $$x \le a$$ for every $$x$$ in $$X$$ then $$a$$ is the *greatest* (*largest*, *last*) element of $$X$$. It too is unique (if it exists at all)

> If $$A$$ and $$B$$ are sets, does there exist a set that contains all the ordered pairs $$(a, b)$$ with $$a$$ in $$A$$ and $$b$$ in $$B$$?

Don't forget, this formalization requirement was only produced as a means to an end- after sufficient mathematical innovation some degree of formalization is unavoidable. I'll leave just one example of why obvious things aren't always obvious. Prove this: *Every infinite set has a countably infinite subset*. And you can't use the Axiom of Choice!

## No Examples

Back to something easier to explain: one of my biggest qualms with this book is its abject lack of examples. Set theory is one of the most concrete, least abstract fields of math out there. Yet for some reason Halmos produced hardly any examples of the often complex and difficult theorems and lemmas, leaving me to struggle for hours and eventually consult math exchange. Oh how much time I could have saved had Halmos had the humility to stoop down from his throne of abstraction and theory to give me, the starving peasant, a simple single set that would have satisfied his previous definition. Call it retaining purity, call it a skill issue on my part, but modern books are often constructed around ease of use and are saturated with both worked answers and more hands off examples and Halmos definitely was conservative in this regard. Even though it was probably fine for the 1960s that shit would not fly today. *All I ask for is one example of a family Halmos! It's all I ask for!*. I feel like it's especially big talk coming from a guy who supposedly said this: ![Halmos Quote](/assets/img/halmos_quote.png) (can access it [here](https://books.google.it/books?id=7VblBwAAQBAJ&pg=PA63#v=onepage&q&f=false)) An old boss of mine swore by the zone of proximal development, and I have hardly felt it more relevant than here. "The whole book is an exercise" stfu Paul, let us walk before we run.

## Bite Sized Chapters

But the text does not fall entirely flat when it comes to readability and niceness. While it is true that sometimes the thinnest books are often the most painful (I'm coming for you next Baby Rudin!) such is not the case for set theory, which is relatively easy and beginner friendly. And while I'm sure Halmos could have *chosen* to make it more difficult, he didn't. The shortness of the chapters is satisfactory for me. And the way in which they are naturally segregated makes it that much easier to make real "progress" throughout the book (as opposed to say, abstract algebra, where learning the fundamentals of representation theory hardly prepares you for polynomial rings). Sure, you could argue that set theory lends itself to this sort of gradual layering of concepts but I call it as I see it and this deserves to be celebrated.

## The Proofs Will Make You Feel Amazing, if You Can Prove It!

More on "the whole book is an exercise"- I enjoyed the proofs. I felt as though they were somewhat out of my reach but by relaxing the rules to various (but allowable) degrees by allowing some reasoning in addition to strict statements (Betrand Russell would not be happy), they were all accessible. The recursion theorem above is probably my favourite, but the generalized associativity and commutativity laws for sets also come to mind when I try to recall memorable proofs. It is remarkable how quickly the difficulty jumps from "I learnt this stuff in year 7" to "What the hell is going on, is $$X$$ a family or a set or a function here?". And just for completeness and to be fair to the author, I will mention that the book has its own fair share of "standard" classical proofs, such as the proof of commutativity for integers, or that $$m \lt n \rightarrow m + k \lt n + k$$. That being said, the major problem with the exercises- apart from there being too few of them, and the lack of specification in the exercises themselves- is that it is not entirely clear firstly when the reader has definitively proved something and secondly (though on the same point) whether the proof was allowable, or in other words what constitutes an allowable step when working "within the system" of "set theory logical proofs". The answer would be a straightforward "apply only the laws of deductions and axioms themselves" but as Halmos said in the preface, the fact that the book has been written in a naive way means that there is no avoiding some degree of conflict here. I personally think this is single greatest barrier that makes this text inaccessible to someone that is entirely new to maths despite the subject having no formal prerequisites- or in other words, the prerequisites are purely that the reader possesses some minimal mathematical maturity. I also believe this is largely a product of Halmos' writing style and the era in which the book was written (originally published 1960, with a reprint in 1974! This book is old.) This is also responsible for other complaints I have heard, such as naming the first chapter of the book "the axiom of extension" as opposed to "extensionality" which unfortunately makes that much less sense due to generic linguistic drift.

## In Summary

This book has recieved significant acclaim and my final judgement is that it is deserved. On one hand not everything was new to me but on the other I didn't even learn everything that was new to me! I did learn a good amount- you could argue that if I knew any less I would have struggled to understand what little of the book I've claimed to have processed. The exercises were challenging both in ways they intended to be and ways they didn't (with language and insufficient question specification). I would recommend it to any students *that already know some minimal set theory and logic* provided they either have a large amount of experience with basic propositional/predicate logic or a strong drive to learn more. In Australia, we offer the subject extension II maths in highschool, and I would suggest high ext II marks (but no additional knowledge) are required to understand the majority of the content. Even to students who do not fit this description, I still recommend it if you enjoy that older writing style- just beware you may not absorb as much on a first read.
