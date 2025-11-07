---
title: "Type Theory Part III: Verifying Formal Specifications"
date: 2025-11-05 20:30:00 +1100

categories: [Technical]
tags: [lean,programming,maths,computer-science,technical,type-theory,leftpad,verification,formal-verification,formal-methods,proof]
image:
    path: https://www.rmit.edu.au/content/dam/rmit/au/en/study-with-us-orderable/seo-info-pages/how-to-become/engineering/how-to-become-an-engineer-main-image-1220x732.jpg
---

> All the code snippets in this article are compiled into a fully working proof accessible [here](https://github.com/Mukundks2004/leftpad)
{: .prompt-info }

> You can read Part II [here](https://mukundks2004.github.io/BetweenBooks/posts/proving-natural-addition-commutes-from-first-principles/)
{: .prompt-info }


Our previous ventures into type theory were all self-motivated and highly theoretical. In this entry in the series we'll explore the utility of type theory in the real world. First we'll talk about requirements and what it means for an engineered product to be [verified](https://en.wikipedia.org/wiki/Formal_verification). We'll go on to write a [formal specification](https://en.wikipedia.org/wiki/Formal_specification) for a simple software and implement it. Finally, we'll perform [software verification](https://en.wikipedia.org/wiki/Software_verification) using the specification via [type theoretic methods](https://en.wikipedia.org/wiki/Formal_methods), thereby guaranteeing the software's [correctness](https://en.wikipedia.org/wiki/Correctness_(computer_science)).

## What Is a Formal Specification

Engineers, including software engineers, design solutions to problems. A core, central part of the engineering process is clearly defining the problem and what it means for a solution to exist. Typically, this is formalized by writing what are called **functional requirements**. You can imagine the solution to the problem as a point that inhabits a higher dimensional design space. There are dimensions for every measurable criteria of the solution, like budget, size and time. By writing requirements you constrain the design space and limit degrees of freedom. Provided that the solution meets the requirements, that it is within the parameters set by the engineer, you can be agnostic to the details- success is determined by the fact the constraints are satisfied and any unspecified implementation details or side effects are out of scope. This concept of what it means philosophically to have succeeded at completing a task is so important to engineering that an entire subfield of engineering, [requirements engineering](https://en.wikipedia.org/wiki/Requirements_engineering) (a central part of [systems engineering](https://en.wikipedia.org/wiki/Systems_engineering)) is built around this.

## Writing Requirements

Requirements look different depending on the domain of the problem- whether it is conceptual, related to product design, management, architecture, mechanical or manufactured systems, software, etc. On top of this, requirements can fall in different buckets such as business requirements, user requirements, functional/non functional requirements and constraints/limitations. Luckily for us, our domain is limited to a single self-contained software program so our requirements will be exclusively functional.

Writing out the requirements will feel mathemtical and algebraic- this is deliberate and the purpose of this is multifold. Firstly it is a nice logical progression from the previous exercise where our proofs *were* mathematical. Secondly, this makes requirements easy to both write out and prove since the observable/quantifiable effects of the program we will write are naturally mathematical, even though the program itself is not. Thirdly, any sufficiently defined system, no matter the domain, will have requirements that are logically algebraic. This is foundational to the field of [constraints programming](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem), a conceptually related rigid/automated/computational variation of formal verification. In constraints programming, degrees of freedom and unknown variables are literally given variable identifiers and an attempt is made to 'resolve' the system or find a solution that satisfies the constraints and optionally simultaneously optimizes a cost function. This representation of a system with requirememts can be used to derive a canonical/normal form, useful for comparing systems and uncovering potential hidden isomoprhisms between different systems that have the same requirements. Although constraints programming is partially related to type theory through denotational semantics, it is beyond the scope of this article. Fourthly, mathematical definitions remove ambiguity, make the output clear and predictable, make testing easy, make communication of requirements easier and are beneficial in any area related to rigidity and clarity.

The set of requirements is known as a specification or spec for short.

## Leftpad

The program we are writing is known as leftpad. We'll be writing it in lean4. Leftpad is conceptually simple. TW: this is the informal, imprecise, non-rigorous definition to familiarize the reader preceding the actual definition. Leftpad is a program that accepts 3 parameters: a list of elements $$L$$, an element $$x$$, and a length $$n$$ (I tried to do this without introducing variables but it is really difficult and harder to communicate). Described in an imperative fashion, it returns $$L$$ after 'padding' it on the left with $$x$$ to make it $$n$$ long. Hence leftpad.

### Some Leftpad History

Despite being a trivial program to write in both imperative and functional styles, when leftpad functionality was required in node projects leftpad was commonly outsourced to the npm package '[left-pad](https://www.npmjs.com/package/left-pad)' rather than being implemented directly. In 2016, the left-pad package was taken down from the registry due to an argument and 1000s of projects that depended on it including [Babel](https://babeljs.io/) and [React](https://react.dev/) where unable to be built.

Since then, leftpad has been built into js itself under a new name. [You can read more about the incident here.](https://en.wikipedia.org/wiki/Npm_left-pad_incident)

![Contents of leftpad at the time of its removal](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7b/Left-pad_0.0.3_contents.png/500px-Left-pad_0.0.3_contents.png).

### The Specification

Three things are required of a program to be leftpad. Using the variables above:
1. The length of the output is the maximum of $$n$$ and the length of $$L$$
2. The output ends with $$L$$
3. Denote the length of $$L$$ by '$$k$$'. The output starts with $$n - k$$ copies of $$x$$, where $$-$$ is [natural subtraction](https://en.wikipedia.org/wiki/Monus#Natural_numbers).

> We never specified anything regarding implementation details, only declarative rules. Leftpad carries imperative connotations, that there should be some kind of 'repeated padding' implying a loop or similar. But our specification does not include this. The leftpad solution (program) may not be unique. There may be many different programs that all satisfy the leftpad requirements, yet have measurably different outputs. We are indifferent to such distinctions, these hypothetical programs are equivalent 'up to leftpad'.
{: .prompt-info }

While this spec is a great start and practically precise, it still uses the English language and there is arguably room for interpretation. Let us redo the previous section in lean for the pedants (me).

## Infrastructure

In order to write and verify leftpad (from first principles, relying only on the expressive power of lean and no libraries) we need to define the following data structures and operations. Technically these have their own specs too! We will use the standard definitions.

> The following datatypes and functions are named according to lean convention *but* are prefixed by 'My' to avoid naming conflicts with preexisting constructs. This section is also slightly hard to follow in terms of logic. I explain it the way I went about writing it, defining data structures and functions on demand. While it has the benefit of not introducing seemingly random code off the bat, it is not 100% self-motivating and there are places I assert things that are not obvious that were only made clear to me after multiple attempts at a definition.
{: .prompt-info }

The signature of the leftpad program is the following. `α` is of type `Type` and is implicit- which means it is an identifier that can be used but cannot be provided, it is pulled from `x` and `xs`. `leftpad` is provided a `MyNat`, `n`, to pad to, an `α`, `x`, to pad with and a `MyList α` to pad to the left of.

```lean
def Leftpad.leftpad {α : Type} (n : MyNat) (x : α) (xs : MyList α) : MyList α
```

## Lists

Lists are how we model collections. `leftpad` will be padding to the left of a `MyList`.

```lean
inductive MyList (α : Type) where
  | my_nil : MyList α
  | my_cons : α → MyList α → MyList α
```

## Naturals

The natural (or counting) numbers `MyNat` are used to measure length such as the length to pad to.

```lean
inductive MyNat where
  | my_zero : MyNat
  | my_succ : MyNat → MyNat
```

## The Naive Implementation

Naively, leftpad repeats `x` $$n - k$$ times and puts it behind $$L$$. This requires being able to repeat an element to create a list, then application of this process to the result of natural subtraction. List concatenation must also be defined to combine this with $$L$$.

```lean
def MyList.replicate {α : Type} : MyNat → α → MyList α
  | MyNat.my_zero, _ => my_nil
  | MyNat.my_succ n, x => my_cons x (MyList.replicate n x)

def MyNat.my_sub : MyNat → MyNat → MyNat
  | m, my_zero => m
  | my_zero, _ => my_zero
  | my_succ m, my_succ n => my_sub m n

def MyList.my_append {α : Type} : MyList α → MyList α → MyList α
  | my_nil, ys => ys
  | my_cons x xs, ys => my_cons x (xs.my_append ys)
```

We can now implement `leftpad`.

```lean
def Leftpad.leftpad {α : Type} (n : MyNat) (x : α) (xs : MyList α) : MyList α :=
  xs.my_append (MyList.replicate (n.my_sub xs.my_length) x)
```

This marks the end of the beginning. The rest of the article will be about assembling the machinery to prove that this is correct.

## Tests vs Proofs

How do we know our program has met the specification? Traditionally, developers would perform [TDD](https://en.wikipedia.org/wiki/Test-driven_development) and write [UT](https://en.wikipedia.org/wiki/Unit_testing), often before the actual implementation (analogous to how requirements are written before the system is built). This is a robust solution and still the industry standard. But it has the flaw of not ever ensuring complete coverage and of testing based on implementation and not based on requirements. [Gherkin](https://en.wikipedia.org/wiki/Cucumber_(software)#Gherkin_language) and [BDD](https://en.wikipedia.org/wiki/Behavior-driven_development) aim to, and arguably succeed at, solving the latter problem. But code coverage is still an issue. Other developments that have been made are [instrumentation](https://en.wikipedia.org/wiki/Instrumentation_(computer_programming)) and runtime analysis to determine test coverage, which is a step better in theory but in the case of complex logic the burden is on the developer to find a way to test the path that has not been tested to slowly add tests and crawl to 100% coverage. Additionally, this only guarantees that all logical paths have been tested, not all possible inputs. [Property testing](https://en.wikipedia.org/wiki/Property_testing) solves this problem practically through a heuristic approach where many guesses are made with a huge array of input values- but even this does not ensure complete correctness. The only way to gurantee that, is with a [proof](https://en.wikipedia.org/wiki/Computer-assisted_proof).

Remember from the previous articles, by the Curry-Howard Correspondence we can consider a proposition proved by encoding it in a type and constructing a term of the type, or a 'witness'. We will now proceed to encode the spec into types in lean and run the type checker to verify our implementation is correct.

## The Specification (Round II)

Recalling the three rules written above in plain English, we need some additional structure to write the spec. The first rule specified the length of a list as the result of a 'max'.

```lean
def MyList.my_length {α : Type} : MyList α → MyNat
  | my_nil => MyNat.my_zero
  | my_cons _ xs => xs.my_length.my_succ

def MyNat.my_max : MyNat → MyNat → MyNat
  | my_zero, n => n
  | m, my_zero => m
  | my_succ m, my_succ n => my_succ (my_max m n)
```

The second and third rules were phrased in terms of prefixes and suffixes respectively. To do a content comparison (which is what prefixes and suffixes are) we first need to introduce equality.

### Booleans & Equality

Booleans are used to define equality. Equality for us will be a `MyBool` type rather than a custom `Prop` object- to say things are true, rather than constructing a witness to `P` we will construct an element of `P = true`. The equality typeclass contains two 'fields', an equality function (which is straightforward) and a more interesting custom refl method used to generate a proof of equality. `my_eq_refl` takes an element `x` and returns a proof that it is equal to itself. An instance of `MyEq`, which is any datatype that implements the class, has a way to produce a proof that any element is equal to itself.

```lean
inductive MyBool where
  | my_true : MyBool
  | my_false : MyBool

class MyEq (α : Type) where
  my_eq : α → α → MyBool
  my_eq_refl : (x : α) → my_eq x x = MyBool.my_true
```

We show that `MyBool` is comparable in the following implementation. `my_eq` returns true if both arguments are the same and false otherwise. `my_eq_refl` will be called on an `x` and has to return a proof that `my_eq x x` is equal to `MyBool.my_true`. But we know that `x` can only be `my_true` or `my_false` and in either case, `my_eq` will return `my_true` so this is definitionally true, and the goal can be closed with `rfl`.

```lean
instance : MyEq MyBool where
  my_eq
    | .my_true, .my_true => .my_true
    | .my_false, .my_false => .my_true
    | _, _ => .my_false

  my_eq_refl x := by
    match x with
    | .my_true => rfl
    | .my_false => rfl
```

### Prefix and Suffix

`is_suffix_of` needs to do a match on both `x` and `xs` in the `cons cons` case. Since we will write the `is_suffix_of` method with our bool implementation (over custom `Prop`), we need to define logical `AND` to encode the multiple conditions. We can also save time writing the `is_prefix_method` by using the fact that `a.is_prefix_method b` is the same as `a.reverse.is_suffix_of b.reverse`. `[MyEq α]` is the syntax for encoding a generic type constraint on `α`.

```lean
def MyBool.my_and : MyBool → MyBool → MyBool
  | .my_true, .my_true => .my_true
  | _, _ => .my_false

def MyList.my_reverse {α : Type} : MyList α → MyList α
  | my_nil => my_nil
  | my_cons x xs => xs.my_reverse.my_append (my_cons x my_nil)

def MyList.is_suffix_of {α : Type} [MyEq α] : MyList α → MyList α → MyBool
  | my_nil, _ => MyBool.my_true
  | my_cons a as, my_cons b bs => (MyEq.my_eq a b).my_and (as.is_suffix_of bs)
  | _, _ => MyBool.my_false

def MyList.is_prefix_of {α : Type} [MyEq α] : MyList α → MyList α → MyBool
  | as, bs => (as.my_reverse).is_suffix_of (bs.my_reverse)
```

We now have the tools to *define*, not prove, our spec. `sorry` is the lean placeholder for a proof that hasn't been written yet. It is effectively equal to showing an implication `P → Q` in place of `Q` straight up when `P`'s truth value is uncertain. Lean will type check `sorry`s with warnings. `sorry` speeds up development as I can `sorry` a lemma I am fairly confident is true when proving something that is much more unclear.

```lean
theorem Leftpad.leftpad_length {α : Type} (n : MyNat) (x : α) (xs : MyList α) : (Leftpad.leftpad n x xs).my_length = n.my_max xs.my_length := by sorry

theorem Leftpad.leftpad_prefix {α : Type} [MyEq α] (n : MyNat) (x : α) (xs : MyList α) : (MyList.replicate (n.my_sub xs.my_length) x).is_prefix_of (Leftpad.leftpad n x xs) = MyBool.my_true := by sorry

theorem Leftpad.leftpad_suffix {α : Type} [MyEq α] (n : MyNat) (x : α) (xs : MyList α) : xs.is_suffix_of (Leftpad.leftpad n x xs) = MyBool.my_true := by sorry
```

## Proving the Specification

> To keep this section understandable I've decided to remove the definitions for all proofs. Make sure to check the github linked above for details.
{: .prompt-info }

```lean
theorem Leftpad.leftpad_length {α : Type} (n : MyNat) (x : α) (xs : MyList α) : (Leftpad.leftpad n x xs).my_length = n.my_max xs.my_length := by
  match n with
  | MyNat.my_zero =>
    rw [MyNat.zero_max, Leftpad.leftpad_rep_def, MyNat.zero_sub, MyList.zero_rep_is_nil, MyList.append_nil]
  | MyNat.my_succ n =>
    match xs with
    | MyList.my_nil => rw [Leftpad.leftpad_rep_def, MyList.nil_length, MyNat.sub_zero, MyList.nil_append, MyList.rep_length, MyNat.max_zero]
    | MyList.my_cons y ys =>
      rw [Leftpad.leftpad_rep_def, MyList.cons_length, MyNat.succ_sub_succ, MyList.append_length, MyList.cons_length, MyList.rep_length, MyNat.succ_max_succ, MyNat.succ_add, MyNat.add_sub_eq_max, MyNat.max_comm]

theorem Leftpad.leftpad_prefix {α : Type} [MyEq α] (n : MyNat) (x : α) (xs : MyList α) : (MyList.replicate (n.my_sub xs.my_length) x).is_prefix_of (Leftpad.leftpad n x xs) = MyBool.my_true := by
  rw [Leftpad.leftpad_rep_def, MyList.prefix_prepend]

theorem Leftpad.leftpad_suffix {α : Type} [MyEq α] (n : MyNat) (x : α) (xs : MyList α) : xs.is_suffix_of (Leftpad.leftpad n x xs) = MyBool.my_true := by
  rw [Leftpad.leftpad_rep_def, MyList.suffix_append]
```


## Applications
Formally verifying software requirements as we have done is useful whenever there are non-negotiable needs that software must perform according to the spec. The more important the code is, the more useful formal methods like the above become- because there is a need to **guarantee** that the software is correct. Here are some real world examples of critical systems that have been verified- not all of them have been verified with type theory, but TT is the most expressive of the verification methods (tied with HOL) and easier to use than HOL when your proofs are oriented around programs, involving data structures and are not just mathematical statements.
- [CompCert](https://www.absint.com/compcert/) is a formally verified optimizing C compiler. It was proven using [Rocq](https://en.wikipedia.org/wiki/Rocq) (a type theory based language). Stated on the website: "Its intended use is compiling safety-critical and mission-critical software written in C and meeting high levels of assurance."
- [sel4](https://sel4.systems/Verification/) is a formally verified OS kernel. It was proven using [Isabelle](https://en.wikipedia.org/wiki/Isabelle_(proof_assistant)) (a HOL based language). Stated on the website: "Its uniqueness lies in the formal mathematical proof that it behaves precisely as specified and enforces strong security boundaries for applications"
- NASA's [FRET](https://en.wikipedia.org/wiki/FRET_%28software%29) requirements engineering tool is built on the dependently typed language [PVS](https://en.wikipedia.org/wiki/Prototype_Verification_System)
- Intel and ARM model hardware such as processors as [FSMs](https://en.wikipedia.org/wiki/Finite-state_machine) or [RTL abstractions](https://en.wikipedia.org/wiki/Register-transfer_level) and formally verify them as detailed [here](https://www.researchgate.net/publication/305253125_End-to-End_Verification_of_Processors_with_ISA-Formal)

## Recap

In this article we showed the practical applications of type theory. We used slightly more advanced lean language features to create (the infrastructure around and then) a non-trivial, practical program. Then we encoded the specification for this program in lean and proved that the spec was obeyed, verifying that our program was correct.

What a journey! Although it was entertaining, and different, it was far too applied for me. In the next installment we'll tackle some advanced and seemingly unrelated pure maths concepts that we can surprisingly marry with type theory and use to both further the bleeding edge and redefine foundations.

