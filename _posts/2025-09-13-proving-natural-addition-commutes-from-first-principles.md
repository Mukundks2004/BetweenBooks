---
title: Proving Natural Addition Commutes from First Principles
date: 2025-09-13 01:00:00 +1000

categories: [Technical]
tags: [lean,maths,computer-science,technical,type-theory,computer-science]
image:
    path: /assets/img/building_blocks.jpg
---

This article is the first installment in a series on type theory. Type theory is a field of math that is not typically covered in an undergrad math or CS degree, but is quite beginner friendly. When talking about it with friends I often draw parallels between it and category theory since they are both closely related to programming and are CS-adjacent. They are both learnable without a formal background in math. In fact, mathematics itself can be founded on either of these without the need to involve sets. Yet, to encounter either field in classical (not CS) mathematical contexts one often must traverse a vast array of terrains over years of study peaking in advanced algebraic topology/homotopy. And a side effect of that point is that to truly understand purpose and motivation of either of these fields you must study examples in areas of math that someone without significant academic maturity would not understand. The prototypical motivating example of category theory is the deliberately abstract product construction- which to understand requires that a student has experienced various instances of products from a fields such graph theory, group theory, set theory and topology, after which they can of their own see the need for a common unifying definition.

With that being said, we won't cover anything that advanced immediately. So buckle up, let's introduce type theory.

## Type Theory

When I was studying theoretical computer science, my Professor Dr Luke Matthieson told me that us computer scientists were lucky- because unlike other mathematicians, we had a fully realized model of the thing we were studying, a Turing machine. I remember at the time having reservations about that since a computer, while computationally equivalent to and modelled off and able to (to an arbitrary degree of precision) simulate a Turing machine, does not exactly help prove things like decidability or completeness which is what we were trying to do. Well, type theory is the real deal. There are many type theories just as there are many set theories, dependening on which axioms one assumes. And for each of the main theories there are what we call *dependently typed programming languages* that for all practical purposes are pure implementations of the theories. And unlike a Turing machine, these languages are very much relied on both to better understand the field of type theory experimentally *and* as a tool for formally verifying all maths- including, of course, type theory itself.

Type theory is the study of types and how they classify terms. It also includes the rules for constructing and reasoning about types and terms. A type is an abstract thing. A type can have many terms that are 'of' the type. This much must be taken for granted, similar to how we just accept sets without thinking too hard about what membership really means. Similar to the variety of set theories such as ZF and ZFC, we have many type theories. The theory we are going to be studying today is Per Martin-Lof's intuitionistic type theory (abbreviated as MLTT).

## The Curry-Howard Correspondence

Type theory often substitutes "terms" with "programs". Loosely speaking, a program is a set of instructions. Programs are categorised by their types. If you have written code before, computer programs in functional languages are examples of programs. Consider the minimal program `2 + 3` in a language such as Haskell. It compiles, so it is a valid, freestanding program. The type of this program is `Int` because it evaluates to `5`, which is an `Int`. The program `2 + 3` is interchangeable with the term `5`.

The fact the program evaluates to a fixed value is saying the same thing as saying it "returns" or "outputs" that fixed value. Other numbers like `7` or `8` are also `Int`s- so if the program evaluted to them it would have the same type `Int` despite evluating to a different term.

The Curry-Howard correspondence is a powerful association that asserts that types and terms of a type in type theory are the same as propositions and proofs of the proposition in formal logic. Take the proposition `P` that double an odd number is even.

## Topic

some text
- point one
- point two
- point three