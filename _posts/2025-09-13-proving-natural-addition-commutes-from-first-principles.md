---
title: Proving Natural Addition Commutes from First Principles
date: 2025-09-20 01:00:00 +1000

categories: [Technical]
tags: [lean,maths,computer-science,technical,type-theory]
image:
    path: /assets/img/building_blocks.jpg
---

This article is the first installment in a series on type theory. Type theory is a field of math that is not typically covered in an undergrad math or CS degree, but is quite beginner friendly. When talking about it with friends I often draw parallels between it and category theory since they are both closely related to programming/are CS-adjacent. They are also both learnable without a formal background in math. In fact, [mathematics itself can be founded on either of these](https://www.andrew.cmu.edu/user/awodey/preprints/stcsFinal.pdf) without the need to involve sets. Yet, to encounter either field in classical (not CS) mathematical contexts one must traverse a vast array of terrains over years of study peaking in advanced algebraic topology/homotopy theory. And the reason this is is that to truly appreciate the purpose and motivation of either of these fields you must be able to conjure examples from areas of math that someone without significant academic maturity would not know of. The prototypical motivating example of category theory is the deliberately abstract product construction- which to understand requires that a student has experienced various instances of products from domains such as graph theory, group theory, set theory, and topology, after which the student can of their own see the need for a common unifying definition.

With that being said, we won't cover anything that advanced immediately. So buckle up, let's introduce type theory.

## Type Theory

When I was studying theoretical computer science, my Professor, Dr Luke Matthieson, told me that us computer scientists were lucky because unlike other mathematicians we had a fully realised model of the thing we were studying- a Turing machine. I remember at the time having reservations about that since a computer (while computationally equivalent to, modelled from, and functionally able to simulate a Turing machine) does not help prove things like decidability or completeness which is what we were trying to do. TCS is about abstract models and is machine independent. Well with type theory, we actually do have useful realised models. There are many type theories just as there are many set theories, dependening on which axioms one assumes. And for each of the main theories there are what we call *dependently typed programming languages* that are implementations of the theories. These languages are very much relied on both to better understand the field of type theory experimentally and unlike a Turing machine, are a tool for formally verifying, studying and developing, all maths- including, of course, [type theory itself](https://www.thi.uni-hannover.de/fileadmin/thi/abschlussarbeiten/urschumzew-ba.pdf).

Type theory is the study of types and terms. It also includes the rules for constructing and reasoning about types and terms. A type is an abstract thing. All terms belong to (or are 'of') a type. This much must be taken for granted, similar to how we just accept sets without thinking too hard about what membership really means ([read this dialogue to learn about the difficulty of using logic and reasoning to defend itself](https://www.physixfan.com/wp-content/files/GEBen.pdf#page=199)). Similar to the variety of set theories such as ZFC, ZF, and SP, we have many type theories. The theory we are going to be working within today is the Calculus of Inductive Constructions (CIC), a variant of the Calculus of Constructions (CoC) with the added benefit of inductive types. The only important thing to know about this is that it is intuitionistic rather than classical- it does not assume the law of the excluded middle. Most people would agree that for any proposition `P` either `P` is true or 'not `P`' is true. However, intuitionistic logic makes no such assumptions. For anything to be true it must be constructed.

### A Note on Terms and Programs

Type theory often substitutes the word "terms" with "programs". Loosely speaking, a program is a set of instructions. If you have written code before, computer programs in functional languages (e.g. Haskell functions, Nix flakes) are examples of programs because they always return something. Consider the minimal program `2 + 3` in a language such as Haskell. The type of this program is `Int` because it evaluates to the term `5`, which is of type `Int`. That the program evaluates to a fixed value is the same as it "returning" or "outputting" that value. Similarly to terms, programs are categorised by their types. The program `7` is different to the program `2 + 3` but they have the same type. The question of whether `2 + 3` and `5` are the same program is a little bit harder to answer but the academic consensus is yes, as [they reduce to the same normal form in most implementations](https://lean-lang.org/doc/reference/latest/The-Type-System/#:~:text=In%20addition%20to%20having%20types%2C%20terms%20are%20also%20related%20by%20definitional%20equality.%20This%20is%20the%20mechanically%2Dcheckable%20relation%20that%20syntactically%20equates%20terms%20modulo%20their%20computational%20behavior.%20Definitional%20equality%20includes%20the%20following%20forms%20of%20reduction%3A). 

## The Curry-Howard Correspondence

The Curry-Howard correspondence (CH) is a powerful association that asserts that types and programs of a type in type theory are isomorphic to propositions and proofs of a proposition in formal logic. Take the proposition `P` that $$2 + 2 = 4$$. We know this is true. We can prove this algebraically by evaluating the left hand side, evaluating the right hand side and show they are definitionally equal. According to CH, this process is semantically identical to declaring a type `P` representing the notion that $$2 + 2 = 4$$ and constructing a term of the type `P`. For example, the `Int` type could correspond to this proposition. Therefore, by declaring a function that returns integers and implementing it, so that it returns an integer, we are 'proving' `P`. It sounds a little hand wavey- can't I declare that `Int` corresponds to the proposition that `2 + 2 = 5`, or something else wrong? Yes and no. Let's define what's required the language you are working on (and from you!) to be able to prove propositions. We'll show that our hypothetical `Int` function is a proof, that it is relatively meaningless, and how to construct meaningful types such as `2 + 2 = 4` as a type.

> For this next section I use `P` to mean proposition and type interchangeably

### The Language Must Be Statically Typed

The compiler has to verify that the function really does produce an element of type `P`. This is already an industry standard in robust enterprise languages like C#. It is known as "static typing" and is defined as knowing at compile time what the types of all data will be at runtime. Guaranteeing that a function that promises to return `Int`s actually does is just a subset of this. Here's how it works: at compile time the code is parsed into an AST and the symbols are resolved (mapped to types) and type checked. Now getting our program to compile is proof that we have produced a term of this type, and proven `P`, even if we don't actually call the function to get the type. The point is, we know it can be done. The technical way to describe this is that the type `P` is *inhabited*. But big deal, we know that `Int`s exist! Maybe if the return type we were promising to be constructible were more exotic its existence would be noteworthy...

> Side note
{: .prompt-info }

If you are not familiar with the arrow syntax `->`, it is common in functional languages to denote inputs and outputs in signatures. If `A` is a type and `B` is a type, then `A -> B` is a type representing a function that accepts `A` and returns `B`. The chaining of arrows appears when we want to denote multiple input parameters. The `->` operator is right associative so `A -> B -> C` is interpreted as `A -> (B -> C)`. This makes the expression a function that accepts `A` and returns (a function accepting `B` and returning `C`). You can provide both parameters by invoking the 'larger' function twice first providing `A` then with `B`. Functions can't return more than one thing- if you desperately need to return many things create a record/tuple/product type and return a single instance of that. After establishing these rules, functions now take and return one thing, which is conceptually fundamental. The tradeoff is that we ended up passing functions into and getting them from other functions (e.g. our return type above was a function of type `B -> C`). We have naturally derived 2 core tenets of functional programming (FP). Firstly functions that manipulate functions exist and they are called higher order functions (and here is why we need them). Secondly there exists a process to restructure a multi-parameter function to take one parameter at a time, and we can pass multiple parameters to it via successive invocations. This is called currying.

### Our Types Need to Be Expressive

`Int` is about as simple a type as you can get. The same goes for `Bool` and `String` and `Console` and `Animal` and really any type that would be declared `public class MyClass {}` in C#. We don't care about the contents of the type but the simplicity of the class declaration, which amongst the four preceding examples is identical. Proving that these are inhabited is neither difficult nor useful. The way to build complex types is to create types that have information built into them out of other types:

1) Parametric Polymorphism (C# Generics) allow you to index a type using another type. For example, the `List` type in C#. `List` is a type constructor, and has *kind* `Type -> Type`. A kind is like the type of a signature- a standard type has kind `Type` which is the simplest kind. `Type` here is the type of types, it is inhabited by terms such as `Int` and `Bool`. A kind of `Type -> Type` (such as in the case of `List`) takes a type `T` and produces a new type `List<T>`. It constructs types, hence the name. Notice the currying syntax to describe kinds. We can interpret kinds as requiring types and returning types, just like functions. The kind itself determines the kind of its parameters. By repeatedly 'calling' the kind on concrete types we gradually simplify the kind until the final concrete types is created which we cannot apply to more types. These types have terms themselves- we are beginning to build hierarchies similar to sets of sets, but with more information encoded in the hierarchy.

2) Dependent types (no C# equivalnet) allow you to index a type with a value, to create a type constructor that uses a value to create another type. Imagine if the `List` above accepted 2 parameters, a type `T` and a concrete `int` representing how long it would be. Both these parameters would be compile time information stored in the object itself, a list with length 3 is a fundamentally different type to a list with length 2. In dependently typed languages this is a standard data structure and is usually given the name `Vec`. The kind of `Vec` is `Type -> Nat -> Type`- to construct the type we need to provide not just a `Type` (as we would in C#) but a `Nat` too (e.g. 3.) This is critical type information and must be respected to preserve type safety. This is similar to but more powerful than C++ template params, because the parameter does not need to be a compile time constant. This is a huge leap in the complexity of types- they are now much more powerful!

3) ($$\Sigma$$ types/$$\Pi$$ types), also known as the (dependent pair/dependent function) describe the type of a (tuple/function) where the type of the (second elemenet in the pair/output of the function) changes based on the value of the (first element of the pair/input to the function). For example, when `printf` is provided the string literal `My age is %d My favourite letter is %c` as an argument its signature changes to require an integer and a char (`String -> Int -> char`.) However, if it were called with argument `Hello, World!` it would require no additional arguments (`String`). `printf` is an example of a dependently typed function in the C language. But while `printf` is hardcoded into the language, languages with actual support for dependent types let us build these structures from scratch. Phrased more technically, the dependent pair and dependent function can be described as $$(X, P(X))$$ and $$X \to P(X)$$ respectively, where $$P$$ is a function that accepts a value and returns a type. It is a function whose domain is the set of all elements of a type and whose codomain is the set of types. If you happened to notice that the previous definition was unnervingly set theoretic for a blog article about types, please don't think too much about it- we'll formalize type theory another time.

4) Inductive Data Types (ADTs/GADTs) are an entire topic on their own, but essentially allow you to create data types that are constructed from instances of the same data type (hence the name.) For example, in C#, having a constructor for a type `T` that requires an instance of `T` as a parameter. Unlike in C#, the compiler is aware of all of the finitely many ways to construct an object and lets you pattern match on the constructor to choose how to treat an instance of `T` in a function depending on how it was created. Inductive types can return different types depending on which data constructor (FP constructors are called data constructors) was called to create the instance. So it's possible to create a data type like this:

```haskell
data FruitBag a where
    OrangeBag :: [Orange] -> Bag [Orange]
    AppleBag :: [Apple] -> Bag [Apple]
```

where you can define a function that expects data from a particular constructor, such as `giveMeApples :: Bag [Apple] -> ...` while still writing polymorphic code. Unlike in the C# code: `class Bag<T> where T : IFruit`, you can change the type based on constructor logic and you can encode logic such as only some constructors being valid for some `T`s.

We can now generate very complex types, such as type safe expressions `Expr` built recursively from subexpressions, type safe `printf` that knows how to change its signature based on the provided argument, `Tree`s and `List`s become trivial and we can model the simply typed lambda calculus (STLC) in a type safe way. This is incredible and already allows us much more flexibility in writing code contracts and describing type data for day-to-day purposes. But we started this tangent with the purpose of showing how terms of a type constitute proofs of a proposition. And so far all we can do is show that any of our exotic types are inhabited. How does this prove anything? Well, the above language features are technically enough to encode complex types with arbitrary structure and constraints. All we need is the translation service to interpret a type as a logical proposition.

### Our Types Don't Mean Anything (to Us, Yet)

Let's return to our goal of constructing an element of the type `2 + 2 = 4`. Declare a type:

```haskell
data TwoPlusTwoEqualsFour = MyProof
```

We can construct only one term of this type, `MyProof`. Have we proved our goal? This is a philosophical question. From a mathematical standpoint we have constructed a term of a type, so the freestanding program `MyProof` is a proof. But what does this type/proposition mean? [GEB](https://en.wikipedia.org/wiki/G%C3%B6del,_Escher,_Bach) is a famous philosophical book that tries to answer the question of where meaning comes from and how intrinsic it can be. Hofstadter invents many formal systems inside which he probes for the source of meaning such as the [$$pq$$ system](https://www.physixfan.com/wp-content/files/GEBen.pdf#page=59). Perhaps the system more in line with our goal of `2 + 2 = 4` is the typographical number theory, a framework for expressing theorems about the naturals. It is referred to as TNT, so abbreviated because any attempts made to recurse, formulate meta-theorems, or otherwise self-reference invariably result in explosion. In TNT, number-theoretical reasoning can be done by rigid symbol manipulation. The reason moving symbols around on a piece of paper corresponds to the creation of tautologies of mathematics is that we have [chosen an interpretation](https://www.physixfan.com/wp-content/files/GEBen.pdf#page=58) where there is an isomorphism between symbolic manipulation and [preservation of meaning in reality](https://www.physixfan.com/wp-content/files/GEBen.pdf#page=61). This is exactly what we want! Our types can be in such a one-to-one with mathematical propositions if we use typing features to construct and manipulate types the same way we construct and manipulate proposition. This way symbols in the code are codifications of rules of logic such as De Morgan's, transitivity of equality, etc., which themselves are simply codifications of patterns of thought. It's isomorphisms all the way down.

> I really encourage reading the full chapter on interpretations of formal systems. There are many powerful ideas explored in that chapter that I did not raise here. My personal favourite is the exploration of the differences between formal reasoning and human thought.
{: .prompt-info }

Equality, from a logical standpoint, constructs a proposition from two terms (it proposes that `x` is equal to `y`). In FP, this is a type constructor that is indexed by two types.

```haskell
data (=) : a -> b -> Type where
  Refl : x = x
```

The sole data constructor is `Refl`, standing for the reflexive property of equality- the idea that all terms (including Types!) are definitionally equal to themselves. `Refl` takes one argument, `x`, and produces a term of the type `x = x`. Remember this is no more complex than

```csharp
public class Equal<T, U>
{
    public Equal(T value)
    {
    }
}
```

The complexity is a function of `T`, of the domain of values `T` and `U` can take on. In C# they are limited to 'simple' types of kind `Type` while in dependently typed languages they can be arbitrarily complex. In C# we also can't use the fact the code compiles (the fact that `T` equals `T`). In a dependently typed language, we can use this at compile time provided the code type checks. 

Using the `Refl` data constructor, we can create trivial proofs that terms are equal to themselves. `Refl` only accepts one parameter and proves it is equal to itself. So it definitively only proves correct theorems. We are ready to prove our first theorem.

```haskell
twoPlusTwoEqFour : 2 + 2 = 4
twoPlusTwoEqFour = Refl
```

Here is a breakdown of how this works:
- The above is a function named twoPlusTwoEqFour
- It declares it will return an element of type `2 + 2 = 4`
- We can only an element of the equality type using `Refl`
- The function invokes `Refl` which implicitly realises (via type inference) it must produce a term of the type `lhs = lhs` for the function to type check
- The `lhs` is `2 + 2`
- `Refl` constructs an element of type `2 + 2 = 2 + 2`
- The type `2 + 2 = 2 + 2` is equivalent to the type `4 + 4`
- The expected return type is equivalent to the type `4 + 4`
- `lhs = rhs` QED

Now that we understand the Curry-Howard correspondence in theory and in practice, we can construct our first non trivial proof- proving that natural addition commutes.

## Foreword

I thought this would be a good exercise in mathematical foundations. I recently completed a [textbook on set theory](https://mukundks2004.github.io/BetweenBooks/posts/halmos-set-theory-a-review/) and while I generally enjoyed it and found the content sufficiently rigorous I was generally unsatisfied with the degree of 'fundamentality' of set theoretic constructions. I went into the book under the assumption everything would be from the ground up. And that was largely the case. But when it came to the first induction-based proofs of the naturals I couldn't help but feel that I was being pushed in a certain direction when defining and proving things. In my opinion the motivation for steps in a proof or for a new concept should be clear and if not the exercise should be revisited retroactively. In addition to this I felt that I could not draw the line between 'pure' symbolic manipulation and 'flawed' non-rigorous logical justification. Was my proof that $$0 + n = n$$ for natural $$n$$ correct or did I assume something I shouldn't have? We have exactly the right tools to solve both of these problems now. We'll build everything from the ground up in a way that seems impartial and necessary and the type checker will catch any logical mistakes. Our weapon of choice will be lean4.

## Defining the Naturals

We'll be assuming the [Peano axioms](https://en.wikipedia.org/wiki/Peano_axioms) to define the naturals- which has been standard mathematics practice for centuries. We won't use all of them and importantly no other assumptions will be made! I'll try to document which axioms correspond to which 'boilerplate', which in this case is code that we need/features we rely on separate to our actual math.

![Peano Axioms](peano_axioms.png)

To define a new datatype (for the naturals) we use the `inductive` keyword.

```haskell
inductive MyNat : Type where
  | zero : MyNat                -- by 1)
  | succ : MyNat → MyNat        -- by 6)
```

We'll call our naturals `MyNat`s to avoid naming conflicts and potential ambiguity when talking about the real lean `Nat`s which have more structure than the minimal skeleton we define here.