---
title: "Type Theory Part I: Introduction to Type Theory"
date: 2025-09-13 01:00:00 +1000

categories: [Technical]
tags: [maths,computer-science,technical,type-theory,category-theory,haskell,idris,programming,godel-escher-bach,philosophy,philosophy-of-meaning,tcs,dependent-typing,set-theory]
image:
    path: /assets/img/type_theory_background.png
---

This article is the first installment in a series on type theory. Type theory is a field of math that is not typically covered in an undergrad math or CS degree, but is quite beginner friendly. When talking about it with friends I often draw parallels between it and category theory since they are both closely related to programming/are CS-adjacent. They are also both learnable without a formal background in math. In fact, [mathematics itself can be founded on either of these](https://www.andrew.cmu.edu/user/awodey/preprints/stcsFinal.pdf) without the need to involve sets. Yet, to encounter either field in classical (not CS) mathematical contexts one must traverse a vast array of terrains over years of study peaking in advanced algebraic topology/homotopy theory. And the reason this is is that to truly appreciate the purpose and motivation of either of these fields you must know of examples from areas of math that someone without significant academic maturity simply would not have learnt. The prototypical motivating example of category theory is the deliberately abstract product construction- which to understand requires that a student has experienced various instances of products from domains such as graph theory, group theory, set theory, and topology, after which the student can of their own see the need for a common unifying definition.

With that being said, we won't go through anything advanced. So buckle up, we have a lot of ground to cover!

## Type Theory

When I was studying theoretical computer science, my Professor, [Dr Luke Matthieson](https://profiles.uts.edu.au/Luke.Mathieson), told me that us computer scientists were lucky because unlike other mathematicians we had a fully realised model of the thing we were studying- a Turing machine. I remember at the time having reservations about that since a computer (while computationally equivalent to, modelled from, and functionally able to simulate a Turing machine) does not help prove things like decidability or completeness which is what we were trying to do. TCS is about abstract models and is machine independent. Well with type theory, we actually do have useful realised models. There are many type theories just as there are many set theories, dependening on which axioms one assumes. And for each of the main theories there are what we call *dependently typed programming languages* that are implementations of that theory. These languages are very much relied on both to better understand the field of type theory experimentally and (unlike a Turing machine) are a tool for formally verifying, studying and developing, all maths- including, of course, [type theory itself](https://www.thi.uni-hannover.de/fileadmin/thi/abschlussarbeiten/urschumzew-ba.pdf).

Type theory is the study of types and terms. It also includes the rules for constructing and reasoning about types and terms. A type is an abstract thing. All terms belong to (or are 'of') a type. This much must be taken for granted, similar to how we just accept sets without thinking too hard about what membership really means ([there's a discussion to be had about the difficulty of using logic and reasoning to defend itself](https://www.physixfan.com/wp-content/files/GEBen.pdf#page=199).) Types themselves are terms which belong to a 'Type' of types, which itself belongs to a higher class of Type of types of types. The nesting here is needed to solve the problem of [membership paradoxes](https://en.wikipedia.org/wiki/Type_theory#:~:text=Girard%27s%20paradox%20shows%20that%20type,system%20must%20have%20uninhabited%20types.) conceptually similar to paradoxes associated with the notion of a [set of all sets](https://en.wikipedia.org/wiki/Russell%27s_paradox). Just like the variety of set theories such as ZFC, ZF, and SP, we have many type theories. The theory we are going to be working within is the Calculus of Inductive Constructions (CIC), a variant of the Calculus of Constructions (CoC) with the added benefit of inductive types. The only important defining characteristic of CIC is that it is intuitionistic rather than classical- it does not assume the law of the excluded middle. Most people would agree that for any proposition `P` either `P` is true or 'not `P`' is true. However, intuitionistic logic makes no such assumptions; for anything to be true it must be constructed.

That concludes all the 'theory' relevant to type theory. The rest of the article will be about how we work within the theory using dependently typed languages to prove things.

### A Note on Terms and Programs

Type theory often substitutes the word 'term' with 'program'. Loosely speaking, a program is a set of instructions. If you have written code before, computer programs in functional languages (e.g. Haskell functions, Nix flakes) are examples of programs because they always return something (as opposed to a C# method which might have a `void` return type). Consider the minimal program `2 + 3` in a language such as Haskell. The type of this program is `Int` because it evaluates to the term `5`, which is of type `Int`. A program evaluating to a fixed value is the same as it 'returning' or 'outputting' that value. Similarly to terms, programs are categorised by their types. The program `7` is different to the program `2 + 3` but they have the same type. The question of whether `2 + 3` and `5` are the same program is a little bit harder to answer but the academic consensus is yes, as [they reduce to the same normal form in most implementations](https://lean-lang.org/doc/reference/latest/The-Type-System/#:~:text=In%20addition%20to%20having%20types%2C%20terms%20are%20also%20related%20by%20definitional%20equality.%20This%20is%20the%20mechanically%2Dcheckable%20relation%20that%20syntactically%20equates%20terms%20modulo%20their%20computational%20behavior.%20Definitional%20equality%20includes%20the%20following%20forms%20of%20reduction%3A). 

## The Curry-Howard Correspondence

The Curry-Howard correspondence (CH) is a powerful association that asserts that types and programs of a type in type theory are isomorphic to propositions and proofs of a proposition in formal logic. Take the proposition `P` that $$2 + 2 = 4$$. We know this is true. We can prove this algebraically by evaluating the left hand side, evaluating the right hand side and showing that they are definitionally equal. According to CH, this process is semantically identical to declaring a type `P` representing the notion that $$2 + 2 = 4$$ and constructing a term of the type `P`. For example, the `Int` type could correspond to this proposition. Therefore, by declaring a function that returns integers and implementing it, so that it returns an integer, we are 'proving' `P`. It sounds a little hand wavey- can't I declare that `Int` corresponds to any arbitrary proposition to prove anything? Couldn't `Int` equally be the proposition that `2 + 2 = 5`? To answer this, let's define what's required of the language we are working in (and of us, the mathematicians) to be able to prove propositions more conventionally. We'll show that our hypothetical `Int` function is a proof, that it is relatively meaningless, and how to construct meaningful propositional types (such as `2 + 2 = 4` as a type) and then prove them.

For this next section I use `P` to mean proposition and type interchangeably

### A Quick Note on Arrow Syntax

If you are not familiar with the arrow syntax `->`, it is common in functional languages to denote inputs and outputs in signatures. If `A` is a type and `B` is a type, then `A -> B` is a type representing a function that accepts `A`s and returns `B`s. The chaining of arrows appears when we want to denote multiple input parameters. The `->` operator is right associative so `A -> B -> C` is interpreted as `A -> (B -> C)`. This makes the expression a function that accepts `A`s and returns 'a function that accepts `B`s and returns `C`s'. You can provide both parameters by invoking the 'larger' function first, providing a term of `A`, receiving a term of type `B -> C` and then invoking this providing a term of `B`. Functions can't return more than one thing- if you desperately need to return many things, create a record/tuple/product type and return a single instance of that. If these rules are upheld, functions now take and return one thing, which makes them (practically) easier to reason about and is more consistent with functions in maths. The tradeoff is that we ended up passing functions into and getting them from other functions (e.g. our return type above was a `B -> C`). We have naturally derived 2 core tenets of functional programming (FP). Firstly functions that manipulate functions exist and they are called higher order functions. Secondly there exists a process to restructure a multi-parameter function to take one parameter at a time, and we can pass multiple parameters to it via successive invocations. This is called currying.

## Requirement 1) The Language Must Be Statically Typed

The compiler has to verify functions really do produce an element of the type they promise to produce, e.g. `P`. This is already an industry standard in robust enterprise languages like C#. It is known as "static typing" and is defined as knowing at compile time what the types of all data will be at runtime. It works by parsing source code into an AST at compile time, resolving all the symbols (mapping them to types), and type checking all data. Now getting our program to compile is proof that we can produce a term of this type, and therefore is proof of `P`, even if we don't actually call the function to get the type. The point is that we know it can be done. The technical way to describe this is that the type `P` is *inhabited*. But big deal, we know that `Int`s exist! Maybe if the return type we are promising is constructible were more exotic its existence would be noteworthy...

## Requirement 2) Our Types Need to Be Expressive

`Int` is about as simple a type as you can get. The same goes for `Bool` and `String` and `Console` and `Animal` and really any type that would be declared `public class MyClass {}` in C#. We don't care about the contents of the type (fields, methods, properties) but about the complexity of the class declaration, which amongst the four preceding examples is identical. Proving that these are inhabited is neither difficult nor useful. The way to build complex types is to create types that have information built into them out of other types:

1. Parametric Polymorphism (C# Generics) allow you to index a type using another type. For example, the `List` type in C#. `List` is a type constructor, and has *kind* `Type -> Type`. A kind is like the signature of a type as though it were a function- a standard type has kind `Type` which is the simplest kind. `Type` here is the type of types, it is inhabited by terms such as `Int` and `Bool`. A kind of `Type -> Type` (such as in the case of `List`) takes a type `T` and produces a new type `List<T>`. It constructs types, hence the name 'type constructor'. Notice the currying syntax to describe kinds. We can interpret kinds as requiring types and returning types, just like functions. The kind itself determines the kind of its parameters. By repeatedly 'calling' the type constructor on concrete types we gradually concretise the type and simplify the kind by removing its parameters until the final concrete type is created which we cannot index using another type.

2. Dependent types (no C# equivalent) allow you to index a type with a value, to create a type constructor that uses a value to create another type. Imagine if the `List` above accepted 2 parameters, a type `T` and a concrete `int` representing how long it would be. Both these parameters would be compile time information stored in the object itself, a list with length $$3$$ has a fundamentally different type to a list with length $$2$$. In dependently typed languages this fixed-length list is a standard data structure and is usually given the name `Vec`. The kind of `Vec` is `Type -> Nat -> Type`- to construct the type we need to provide not just a `Type` (as we would in C#) but a `Nat` too (e.g. $$3$$.) The integer parameter is critical type information and must be respected to preserve type safety. This is similar to but more powerful than C++ template params, because the parameter does not need to be a compile time constant. This is a huge leap in the complexity of types- our types are now much more powerful!

3. ($$\Sigma$$ types/$$\Pi$$ types), also known as the (dependent pair/dependent function) describe the type of a (tuple/function) where the type of the (second elemenet in the pair/output of the function) changes based on the value of the (first element of the pair/input of the function). For example, when C's `printf` is provided the string literal `My age is %d My favourite letter is %c` as an argument its signature changes to require an integer and a char (`String -> Int -> Char`.) However, if it were called with argument `Hello, World!` it would require no additional arguments (`String`). `printf` is an example of a dependently typed function in the C language. But while `printf` is hardcoded into the language, languages with actual support for dependent types let us build these structures from scratch. Phrased more technically, the dependent pair and dependent function can be described as $$(X, P(X))$$ and $$X \to P(X)$$ respectively, where $$P$$ is a function that accepts a value and returns a type. It is a function whose domain is the set of all elements of a type and whose codomain is the set of types. If you happened to notice that the previous definition was unnervingly set theoretic for a blog article about types, please don't think too much about it- we'll formalize type theory another time.

4. Inductive Data Types (ADTs/GADTs) are an entire topic on their own, but essentially allow you to create data types that are constructed from instances of the same data type (hence the name.) For example, in C#, having a constructor for a type `T` that requires an instance of `T` as a parameter. Unlike in C#, dependently typed compilers are aware of all of the finitely many ways to construct an object and let you pattern match on the constructor to choose how to treat an instance of `T` in a function depending on how it was created. Inductive types can return different types depending on which data constructor (FP constructors are called data constructors) was called to create the instance. So it's possible to create a data type like this:

```haskell
data FruitBag a where
    OrangeBag :: [Orange] -> Bag [Orange]
    AppleBag :: [Apple] -> Bag [Apple]
```

where you can define a function that expects data from a particular constructor, such as `giveMeApples :: Bag [Apple] -> ...` while still writing polymorphic code. Unlike in the C# code: `class Bag<T> where T : IFruit`, you can change the type based on constructor logic and you can encode logic such as only some constructors being valid for some `T`s.

We can now generate very complex types, such as type safe expressions `Expr` built recursively from subexpressions, type safe `printf` that knows how to change its signature based on the provided string template, `Tree`s and `List`s become trivial and we can even model the simply typed lambda calculus (STLC) in a type safe way. This is incredible and already allows us much more flexibility in writing code contracts and describing type metadata for day-to-day software engineering purposes. But we entered this exposition of language features with the purpose of showing how terms of a type constitute proofs of a proposition. And so far all we can do is show that any of our exotic types are inhabited. How does this prove anything? Well, the above language features are technically enough to encode complex types with arbitrary structure and constraints. All we need is the translation service to interpret a type as a logical proposition.

## Requirement 3) Our Types Should Mean Something

Let's return to our goal of constructing an element of the type `2 + 2 = 4`. Declare a type:

```haskell
data TwoPlusTwoEqualsFour = MyProof
```

We can construct only one term of this type, `MyProof`. Have we proved our goal? This is a deeply philosophical question. From a technical standpoint we have constructed a term of a type, so the freestanding program `MyProof` is a proof. But what does this type/proposition mean? [GEB](https://en.wikipedia.org/wiki/G%C3%B6del,_Escher,_Bach) is a famous philosophy book that tries to answer the question of where meaning comes from and how intrinsic it can be. Hofstadter invents many formal systems and probes for the source of meaning within them such as the famous [$$pq$$ system](https://www.physixfan.com/wp-content/files/GEBen.pdf#page=59). Perhaps the formal system most in line with our goal of `2 + 2 = 4` is the typographical number theory, a framework for expressing theorems about the naturals. It is referred to as TNT, so abbreviated because any attempts made to recurse, formulate meta-theorems, or otherwise self-reference invariably result in explosion. In TNT, number-theoretical reasoning can be done by rigid symbol manipulation. The reason moving symbols around on a piece of paper corresponds to creating/reasoning about tautologies in mathematics is that we have [chosen an interpretation](https://www.physixfan.com/wp-content/files/GEBen.pdf#page=58) where [there is an isomorphism between symbols and reality that preserves meaning in both directions when manipulated](https://www.physixfan.com/wp-content/files/GEBen.pdf#page=61). This is exactly what we want! Our types can be in such a one-to-one with mathematical propositions if we use typing features to construct and manipulate types the same way we construct and manipulate propositions. This way symbols in the code are codifications of rules of logic such as De Morgan's, transitivity of equality, etc., which themselves are simply codifications of patterns of thought. It's isomorphisms all the way down.

> I really encourage reading the full chapter on interpretations of formal systems. There are many powerful ideas discussed in that chapter that I did not touch here. My personal favourite is the exploration of the differences between formal reasoning and human thought.
{: .prompt-info }

Here's how it works in practice. Equality, from a logical standpoint, constructs a proposition from two terms (it proposes that `x` is equal to `y`). In FP, this is a type constructor that is indexed by two types.

```idris
data (=) : a -> b -> Type where       -- note that the brackets around '=' indicate that it is infix
  Refl : x = x
```

This equality type is more or less analogous to the following in C#:

```csharp
public class Equal<a, b>
{
    public Equal(a value)
    {
    }
}
```

> The power in `=` comes from the complexity of `a`, it comes from the domain of values `a` and `b` can take on. In C# they are limited to 'simple' types of kind `Type` while in dependently typed languages they can be arbitrarily complex. In C# we also can't use the fact the code will compile as a guarantee/proof to write more maths. In a dependently typed language, we *can* and *do* use this fact at compile time- this is the basis for proving multiple theorems that rely on each other.
{: .prompt-info }

The definition of `=` is what separates our "trivial" type `TwoPlusTwoEqualsFour`, or a function that returns an `Int` from a proposition that has meaning. We build the type from the ground up in a way that is consistent with how logical propositions are constructed in first order logic. Propositions need not be of the form $$a = b$$, they can look like $$a \implies b$$, or $$a \wedge b$$ and there are similar type constructors to build these propositions, all of which have meaning as decided by the definition of the appropriate type constructor. How do we prove these propositions? We must construct an element of the right type.

The sole data constructor for `=` is `Refl`, standing for the reflexive property of equality- the idea that all terms (types are terms too) are definitionally equal to themselves. `Refl` takes one argument, `x`, and produces a term of the type `x = x`. Using the `Refl` data constructor, we can create trivial proofs that terms are equal to themselves. `Refl` only accepts one parameter and proves it is equal to itself so it definitively only proves correct theorems. `Refl` implicitly uses the first parameter's type- calling `Refl` for `a = b` will create an instance of `a = a`, but the compiler realises (via type inference) that this needs to be equal to `a = b` for the function to type check. If `a` is not the same as `b` this will not be possible, our statically typed programming language won't let us promise to return `a = b` but then return `a = a`. All the features of the dependently typed language are working together.

We are finally ready to prove our first theorem.

```haskell
twoPlusTwoEqFour : 2 + 2 = 4
twoPlusTwoEqFour = Refl
```

That's the proof. Here is a breakdown of how this works:
- The above is a function named twoPlusTwoEqFour
- It declares it will return an element of type `2 + 2 = 4`
- We can only create an element of the equality type using `Refl`
- The function invokes `Refl` which will implicitly take the `lhs` as a parameter to construct an element of equality. 
- The `lhs` is `2 + 2`
- `Refl` constructs an element of type `2 + 2 = 2 + 2`
- The type `2 + 2 = 2 + 2` is equivalent to the type `4 + 4` (dependently typed programming languages have a huge set of methods to check/verify that types are equivalent)
- The expected return type is equivalent to the type `4 + 4`
- We have shown that the proposition `2 + 2 = 4` is inhabited. Therefore `2 + 2 = 4`
- Q.E.D.

## Summarizing

In this article we have defined type theory, types, terms, and programs. We learnt about the Curry-Howard correspondence and about the validity of logical methods within type theory. We learnt of the existence of dependently typed programming languages and the cool ways we can build complex types. Then we looked at how those types can be engineered to have meaning. Finally, we constructed a simple non trivial type representing a proposition and proved it. Be proud of yourself for making it this far! This is more type theory than most mathematicians will cover in 4 years of undergraduate study. In the next article we will construct our first multi-step proof: proving that natural addition commutes.