---
title: Variance for People in a Hurry
date: 2026-02-13 17:30:00 +1100

categories: [Technical]
tags: [csharp,programming,maths,set-theory,variance,generics]
image:
    path: https://www.gcis.co.uk/wp-content/uploads/2021/01/statistics-graph-illustration.jpg
---

In 2024 I wrote [my first post on this blog](https://mukundks2004.github.io/BetweenBooks/posts/what-is-the-co-in-covariant/), on variance. Rereading it I think there is much that can be improved and the whole thing can be shortened, so here it is.

## Requirements

Variance needs two things to exist, without these it doesn't make sense to talk about variance. The first is that the set of types in a programming language need to form a poset. This is the case in C#.

> Let $$P$$ be the set of all types. There is a partial order $$(P, \le)$$ where $$\le \subseteq P \times P$$ satisfying reflexivity, transitivity, and antisymmetry, given by $$a \le b$$ if an instance of $$a$$ can be assigned to an identifier of type $$b$$.

The second is that types need to be able to index other types. C# supports this with generics.

Variance is a property of type constructors. A type constructor `F` (with type parameter `T`) is described as being one of 'covariant', 'contravariant', 'invariant' or 'bivariant' in `T`. When `F` has only one type parameter, `T` is implicit when describing the variance of `F`. We simply say that `F` is covariant, for example.

## The Definition

Variance describes how one variable varies as another varies. Loosely speaking, if they are positively correlated they are covariant and if they are negatively correlated they are contravariant. If one does not vary at all while the other varies they are invariant or bivariant, depending on which is which. This is the same reasoning behind the 'variant' terminology in other fields, such as [this one](https://en.wikipedia.org/wiki/Covariance_matrix).

In the context of programming, the independent variable is the indexing type `T` and the dependent variable is the type `F<T>`. The 'function', or 'functor', or 'transformation' is `F`. This is why it is fitting to describe `F` itself as variant in whatever way as `F` is the transformation that is order preserving or order reversing.

![Variance visualized](https://i.imgur.com/cH0LJQy.png)

## Code Examples

Consider this inheritance chain:

```cs
class Animal {}

class Canine : Animal {}

class Dog : Canine {}

class Poodle : Dog {}
```

This creates the poset `{(Poodle, Dog), (Poodle, Canine), (Poodle, Animal), (Dog, Canine), (Dog, Animal), (Canine, Animal)}`.

We know that $$Poodle \le Dog$$ for example, because we can compile: 
```cs
Poodle a = new Poodle();
Dog b = a;
```

Now fix the type constructor `List`. Will the following code compile?

```cs
List<Poodle> a = new List<Poodle>();
List<Dog> b = a;
```

In other words, does the transformation of the poset $$P$$ by the functor `List` preserve the poset structure?

In this case no. Although `Poodle` $$\le$$ `Dog`, `List<Poodle>` $$\nleq$$ `List<Dog`. Logically, this is because if we did assign `a` to `List<Dog> b`, we would be able to perform `b.Add(new Doberman());` and add a doberman to what is literally a list of poodles masquerading as a list of dobermans. Adding a doberman to a list of poodles violates type safety.

What is an example of a type constructor that is covariant? The `IEnumerable<T>` interface. This code compiles:
```cs
IEnumerable<Poodle> a = new List<Poodle>();
IEnumerable<Dog> b = a;
```
Exercise for the reader: explain, with code examples, why this code compiles.

What is an example of a type constructor that is contravariant? The `Action<T>` delegate. This code compiles:
```cs
Action<Dog> a = (x) => new Dog();
Action<Poodle> b = a;
```
Exercise for the reader: explain, with code examples, why this code compiles.

We can annotate generics as covariant or contravariant using the `in` (covariant) and `out` (contravariant) modifiers in the declaration. 
```cs
public interface IProducer<out T>
{
    T Get();
}
```

But only interfaces and delegates can be annotated as such, because stateful types are not covariant, contravariant, or bivariant. Exercise for the reader: explain, with code examples, why this is the case.

Without annotations, all type constructors are invariant by default.

What is an example that is bivariant? Anything not generic, trivially, although C# doesn't officially support annotating generics to denote them as bivariant.

Lack of conclusion.