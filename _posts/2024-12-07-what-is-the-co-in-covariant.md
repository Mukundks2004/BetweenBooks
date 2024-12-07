---
title: What is the 'Co' in 'Covariant' in C#?
date: 2024-12-07 2:35:00 +1100

categories: [Technical]
tags: [technical,programming,maths,generics]
---

Yesterday at work, a friend and I were having a conversation about a strange C# syntactic feature- something he saw in the codebase that he didn't understand. The discussion went as follows.

**Friend**: *You always talk about language quirks, I saw this weird syntax in some dotnet code*\
**Me**: *Alright, what did you see*\
**Friend**: *Well, you know interfaces right?*\
**Me**: *Yeah*\
**Friend**: *It was an interface generic, but those aren't too rare. It was weird because it had a modifier **inside** the generic- "new" or "into" or some blue text like that*\
**Me**: *Hold on, you don't mean variant generics?*\
**Friend**: *Uhh... probably, lol. What's that?*\
**Me**: *\*inhales\**


If you know me you know I would never pass up an opportunity to talk about maths and I had a great maths analogy prepared. On top of that, I firmly believe you can't become an experienced developer blissfully unaware of fundamental OO typing features, so it's time to teach variance.

One of the tricky things about learning variance is justifying the 'co' and 'contra' in the names (we describe variant structures as either covariant or contravariant)- what makes one of the two naturally order-preserving and the other order-reversing? I hope to clear that up, and in doing so provide a nice framework for thinking about generics more universally. Let's begin.

## Variance of Functors

> This little foray is inspired by functional programming, but the terms I use here aren't coincident with their meanings in the context of Haskell or Idris. This is also not a maths lesson, nor is it a beginner's guide. I assume some familiarity with maths and C#.

A functor $$F$$ is a mapping between categories, $$F: C \to D$$. It assigns each objext $$X$$ in $$C$$ an $$F(X)$$ in $$D$$ and each $$f: X \to Y$$ in $$C$$ an $$F(f): F(X) \to F(Y)$$ in $$D$$. Identity morphisms are preserved over $$F$$ and $$F$$ respects composition of morphisms, that is, composing $$f$$ and $$g$$ and then applying $$F$$ yields the same result as composing $$F(f)$$ and $$F(g)$$.

When a functor preserves the order of morphisms- that is, any old functor- we call it covariant. Functors are covariant by default. Sometimes, a functor behaves in the way described above, but reverses the order of morphisms. We still call this a functor, but assign it the adjective "contravariant" as indication of this special distinction.

## Definitions Out of the Way, On to Some Programming!

Similar to $$Hask$$, we can construct $$Type$$, the category of reference types in a polymorphic OO language. Let a morphism from $$A$$ to $$B$$ exist if $$A$$ is a subtype of $$B$$. Recognize that the "one or none"-ness of the class of morphisms between objects makes the category "posetal" in a sense, as there is a partial ordering of all objects in the category. Note that the sybtype ordering on $$Type$$ is not a total ordering, as the object `Cat` bears no relation to `Dog`. 

> Side note: technically morphisms aren't present strictly if `A` is a subtype of `B` but rather if a reference of type `B` is able to point to an object of type `A`. Practically, this ends up being the same thing, but makes inheritance chains much less ambiguous. Simply write the code and see if it compiles to check its correctness.

## Ok... So What?

Hold on, we're getting there. It's time to introduce... normal generics. Let's start slow.

Take our faithful `List<T>` from C#. It takes little effort to see that `List<T>` is an endofunctor (a functor from $$Type$$ to $$Type$$) as it sends every `T` to a new type, `List<T>`. 

> Two asides. Firstly, anyone coming from functional programming might recognize the similarity of this observation about the behaviour of `List` to the notion of a subset of type classes in functional programming languages (those that have an fmap), which behave very similarly and by no accident are also called functors. However, we are applying functors to types and not to instances of those types, so it is important to consider these two types of functors separate. Secondly, the classification of `List<T>` as an endofunctor applies to all generics, not just `List`. But we will start simple.

Back to `List`. Our functor is not complete without a rigorous description of what it does to our morphisms- but I want to pose that to you first. How does `List` act on any particular subtype relation?

Well those of you with little C# experience might foolishly call out "It preserves the morphisms! It preserves the morphisms!". While I admire your eagerness, you would be wrong. Despite its tremendous banality, I have to pull out a textbook example to demonstrate your error. It's your fault really, for making such a simple one.

## Consider the Following:

```csharp
class Animal {}
class Dog : Animal {}

List<Dog> dogs = new List<Dog>();
List<Animal> animals = dogs;
```

Simple code, 4 lines. Will it compile?

**NO!**

The problem is, we are referring to a `List` of `Dog`s as a `List` of `Animal`s, allowing us to call a method belonging to `List<Animal>`, such as a harmless `Add`. But `List<Dog>` is *NOT* a subtype of `List<Animal>`, it can't do the things `List<Animal>` can! For instance, `List<Dog>` cannot accept a `Cat`!

```csharp
animals.Add(new Cat()); // If the code above compiled this would be allowed, but it is unsafe!
```

We just added a `Cat` to a `List<Dog>`!

From this example, it is obvious that generally a morphism from $$A$$ to $$B$$ does not imply a morphism from $$F(A)$$ to $$F(B)$$ (bonus question: when is this morphism preserved?). So generics are invariant, in any capacity, by default. They simply remove all the morphisms, making our whole category theory analogy moot. End of story.

## Or Is It?

You see, if we consider `List` in a very specific context- the context that it is read-only, it actually does preserve morphisms! The trick is to realize that in its read-only capacity (let's take advantage of the `IEnumerable` interface here, using it as a read-only list), referring to an `IEnumerable<Dog>` as an `IEnumerable<Animal>` merely means we are referring to the result of pulling a `Dog` out of the former and treating it as an `Animal`. This is perfectly valid. In code:

```csharp
class Animal {}
class Dog : Animal {}

IEnumerable<Dog> dogs = new List<Dog>();
IEnumerable<Animal> animals = dogs;
```

The above snippet compiles. And in fact, it is widely recognized that `IEnumerable<out T>` is covariant to allow this functionality. Not only is it "out-only" (we can get things out of it but not write to it), but has been marked as such using the `out` modifier, asking the compiler to verify this property to mark the interface as covariant. Now we can understand the full meaning of the word, applying the `IEnumerable<T>` functor to $$Type$$ associates every type with a new one, preserving the morphisms between types after the transformation.

## And For Completeness, Contravariance?

I'm glad you asked, because this example is a little mind-bendy. A well-known contravariant generic is the `Action<T>` delegate, which accepts a `T` but does not return one.

```csharp
class Animal {}
class Dog : Animal {}

Action<Animal> animalAction = (animal) => {};
Action<Dog> dogAction = animalAction;
```

We've... reversed the order of morphisms? Reversed the inheritance chain?? Oh god, what have we done! Jeff Goldblum was right all along!

## Calm Down!

It starts to make sense when you analyze it logically. `animalAction` depends on an `Animal`. By pointing to it using a reference of type `Action<Dog>` you are simply promising that if you ever invoke it, you will provide it with a `Dog`, an acceptable substitute for an `Animal`. Now the definition of morphisms existing "if a reference of type `B` is able to point to an object of type `A`" makes more sense. Of course, all this breaks down if `Action` is able to return `T` too, but thankfully that won't happen soon. And if it does, the compiler will be the first to complain!

## Bivariance

The last type of variance is bivariance, a functor that both preserves the original morphism and creates a corresponding and equal but otherwised reversed morphism in the destination category is bivariant. We know covariance can only be established by making our type out-only and contravariance by making our type in-only, so bivariance would need to meet both these conditions, and neither take nor return `T`, ever. And believe it or not, the maths checks out. Such a type would both preserve the morphism from `I<Dog>` to `I<Animal>` and also create a reversed morphism, from `I<Animal>` to `I<Dog>`. In such a bizarre case, the generic isn't even being used (it neither takes nor returns `T`!), making the references interchangeable and thus the distinction totally useless. From a maths perspective, in a partial order, $$a \le b$$ and $$b \le a$$ implies $$a = b$$ (we call this antisymmetry). And so it is, as the types have become equivalent! From a software engineering perspective, I can't imagine ever needing this. Neither can the compiler, it won't let you have both.

## What Next?

Now we know what 'co' and 'contra' actually mean. I hope to write a follow-up extending this analogy in several dimensions. Although I might consult an expert first, because I need to make sure my facts are in order before putting pen to paper. The next entry will explore:
- Multiple generic parameters as bifunctors (especially in ways where they are both covariant and contravariant in different parameters) and then multifunctors
- The $$Forget$$ adjoint functor which reverses the embedding of a type into a functoral ecosystem by extracting `T`
- Natural transformations as polymorphic functions that map between generics

Thank you for reading!