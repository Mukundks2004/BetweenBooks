---
title: What is the 'Co' in 'Covariant' in C#?
date: 2024-12-07 2:45:00 +1100

categories: [Technical]
tags: [technical,programming,maths,generics]
---

Yesterday at work, a friend and I were having a conversation about a strange dotnet syntactical feature- something he saw in the codebase that he didn't understand. The discussion went as follows.

**Friend**: *You always talk about language quirks, I saw this weird syntax in some dotnet code*\
**Me**: *Alright, what did you see*\
**Friend**: *Well, you know interfaces right?*\
**Me**: *Yeah*\
**Friend**: *Well, it was an interface generic, but those aren't too rare. It was weird because it had a modifier **inside** the generic- new or into or some blue text like that*\
**Me**: *Hold on, you don't mean variant generics?*\
**Friend**: *Well... probably, lol. What's that?*\
**Me**: *\*inhales\**


If you know me, you know I would never pass up the opportunity to talk about maths. And I had a beautiful maths analogy prepared. On top of that, I firmly believe you can't become an experienced developer blissfully unaware of fundamental OO typing features, so it's time to teach variance.

One of the tricky things about learning variance is justifying the 'co' and 'contra' in the names- what makes one of the two naturally order-preserving and the other order-reversing? I hope to clear that up, and in doing so provide a nice framework for thinking about generics more generally. Let's begin.

# Variance of Functors

> This little foray is inspired by functional programming, but the terms I use here aren't coincident with their meanings in the context of Haskell or Idris. Just an FYI. This is also not a maths lesson and I'm not even guaranteeing the maths is correct.

A Functor $$F$$ is a mapping between categories, $$F: C \to D$$. It assigns each objext $$X$$ in $$C$$ an $$F(X)$$ in $$D$$ and each $$f: X \to Y$$ in $$C$$ an $$F(f): F(X) \to F(Y)$$ in $$D$$. Identity morphisms are preserved over $$F$$ and $$F$$ respects composition of morphisms, that is, composing $$f$$ and $$g$$ and then applying $$F$$ yields the same result as composing $$F(f)$$ and $$F(g)$$.

When a Functor preserves order of morphisms- that is, any old Functor- we call it covariant. Functors are covariant by default. Sometimes, a Functor behaves in the way described above, but reverses the order of morphisms. We still call this a Functor, but assign it the adjective "Contravariant" as indication of this special distinction.

## Definitions Out of the Way, on to Some Programming!

Similar to $$Hask$$, we can construct $$Type$$, the category of reference types in a polymorphic OO language. Let a morphism from $$A$$ to $$B$$ exist if $$A$$ is a subtype of $$B$$. Recognize that the "one or none"-ness of this morphism makes the categorical "posetal" in a sense, that there is a partial ordering of all objects in the category. Note that the sybtype ordering on $$Type$$ is not a total ordering, as `Cat` bears no relation to `Dog`. 

> Side note: technically morphisms aren't present strictly if `A` is a subtype of `B` but rather if a reference of type `B` is able to point to an object of type `A`. Practically, this ends up being the same thing, but makes inheritance chains much less ambiguous. Simply write the code and see if it compiles to check it's correctness.

## Ok... So What?

Hold on, we're getting there. It's time to introduce... normal generics. Let's start slow.

Take our faithful `List<T>` from C#. It takes little effort to see that `List<T>` is an EndoFunctor (Functor from $$Type$$ to $$Type$$) as it sends every `T` to a new type, `List<T>`. 

> Two asides. Firstly, anyone coming from functional programming might recognize the similarity of this observation about the behaviour of `List` to the notion of typeclasses in functional programming languages, which behave very similarly and by no accident are also called Functors. Secondly, this behaviour is observed by all generics, not just `List`. But we start simple.

 Back to `List`. Our Functor is not complete without a rigorous description of what it does to our morphisms- but I want to pose that to you first. How does `List` act on a subtype relation?

Well those of you with little C# experience might foolishly call out "It preserves the morphisms! It preserves the morphisms!". While I admire your tenacity and eagerness, you would be wrong. Despite it's tremendous banality, I have to pull out a textbook example to demonstrate your error. It's your fault really, for making such a simple one.

## Consider the Following:

```C#
class Animal {}
class Dog : Animal {}

List<Dog> dogs = new List<Dog>();
List<Animal> animals = dogs;
```

Simple code, 4 lines. Will it compile?

**NO!**

The problem is, we are referring to a List of `Dog`s as a List of `Animal`s, allowing us to call a method belonging to `List<Animal>`, such as a harmless `Add`. But `List<Dog>` is *NOT* a subtype of `List<Animal>`, it can't do the things `List<Animal>` can! For instance, `List<Dog>` cannot accept a `Cat`!

```C#
animals.Add(new Cat()); // Oh no! If our code above compiled this would be allowed... but it is wrong!
```

We just added a `Cat` to a `List<Dog>`!

From this example, it is obvious that generally a morphism from $$A$$ to $$B$$ does not imply a morphism from $$F(A)$$ to $$F(B)$$ (bonus question: when does this hold?). So generics are invariant, in any capacity, by default. They simply remove all the morphisms, making our whole category theory analogy moot. End of story.

## Or Is It?

You see, if we consider `List` in a very specific context- the context that it is read only, it actually does preserve morphisms! The trick is to realize that in its read-only capacity (let's take advantage of the `IEnumerable` interface here), referring to an `IEnumerable<Dog>` as an `IEnumerable<Animal>` merely means we are referring to the result of pulling a `Dog` out of the former and treating it as an `Animal`. This is perfectly valid. In code:

```C#
class Animal {}
class Dog : Animal {}

IEnumerable<Dog> dogs = new List<Dog>();
IEnumerable<Animal> animals = dogs;
```

The above snippet compiles. And in fact, it is widely recognized that `IEnumerable<out T>` is covariant to allow this functionality. Not only is it "out-only", but has been marked so using the `out` modifier, asking the compiler to verify this property to mark the interface as covariant. Now we can understand the meaning of the word, taken from a programming context- applying the `IEnumerable<T>` Functor to $$Type$$ associates every type with a new one, preserving the morphisms between types. 

## And For Completeness, Contravariance?

I'm glad you asked, because this example is a little mind-bendy. A well-known contravariant Functor is the `Action<T>` delegate, which accepts a `T` but does not return one.

```C#
class Animal {}
class Dog : Animal {}

Action<Animal> animalAction = (animal) => {};
Action<Dog> dogAction = animalAction;
```

We've... reversed the order of morphisms?? Reversed the inheritance chain??? Oh god, what have we done! Jeff Goldblum was right all along!

## Calm Down!

It starts to make sense when you analyze it logically. `animalAction` depends on an `Animal`, by pointing to it using a reference of type `Action<Dog>` you are simply promising that if you ever invoke it, you will provide it with a `Dog`, an acceptable substitute for an `Animal`. Now the definition of morphisms existing if "a reference of type `B` is able to point to an object of type `A`" makes more sense. Of course, all this breaks down if `Action` is able to return `T` too, but thankfully that won't happen soon. And if it does, the compiler will be the first to complain!

## Bivariance

The last type of variance is bivariance, a Functor that both preserves the original morphism and creates a corresponding and equal but otherwised reversed morphism in the destination category is bivariant. It's 2:55AM and I can't think of any good examples right now. We know covariance can only be established by making our type out only and contravariance by making our type in only, so bivariance would need to meet both these rules, and neither take nor return `T`, ever. And believe it or not, the maths checks out. Such a type would both preserve the morphism from `I<Dog>` to `I<Animal>` and also reverse, `I<Animal>` to `I<Dog>`. In such a bizarre case, the generic isn't even being used (it neither takes not returns `T`!), making the references interchangeable and thus the distinction totally useless. From a maths perspective, in a partial order, $$a <= b$$ and $$b <= a$$ implies $$a = b$$. And so it is, as the types have become equivalent! From a software engineering perspective, I can't imagine ever needing this. Neither can the compiler, it won't let you have both. 

## What Next?

Now we know what the 'co' and 'contra' actually mean. I hope to write a follow up extending this analogy (honestly hope someone will reach out to tell me this makes sense, because at this stage it could either be totally obvious to an experienced programmer or plain incorrect. I have a feeling this is woefully basic.) in several dimensions. The next entry will explore:
- Multiple Generic Parameters as BiFunctors (especially in ways where they are both covariant and contravariant in different parameters) and then MultiFunctors
- The Forget adjoint Functor which reverses the embedding of the type into a Functoral ecosystem by extracting T
- Natural transformations as polymorphic functions that map between generics

Thank you for reading!