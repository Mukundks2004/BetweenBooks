---
title: Type Theory Part II: Proving Natural Addition Commutes from First Principles
date: 2025-09-20 01:00:00 +1000

categories: [Technical]
tags: [lean,maths,computer-science,technical,type-theory,set-theory,peano,programming]
image:
    path: /assets/img/building_blocks.jpg
---

All the code snippets in this article are compiled into a fully working proof accessible [here](https://github.com/Mukundks2004/arithmetic-first-principles)
{: .prompt-info }

## Foreword

I believe proving that natural addition commutes is a good exercise in mathematical foundations and it is usually required in set theory. [I recently completed a textbook on set theory](https://mukundks2004.github.io/BetweenBooks/posts/halmos-set-theory-a-review/) and while I enjoyed it and found the content sufficiently rigorous I was generally unsatisfied with the degree of 'fundamentality' of set theoretic constructions. I went into the book under the assumption everything would be from the ground up. And that was largely the case. But when it came to the first induction-based proofs I couldn't help but feel that I was being pushed in a certain direction when defining and proving things. In my opinion the motivation for steps in a proof or for a new concept should be self evident and if not the exercise should be revisited retroactively once it becomes so. In addition to this I felt that I could not draw the line between 'pure' symbolic manipulation and 'flawed' non-rigorous logical justification. Was my proof that $$0 + n = n$$ for natural $$n$$ correct or did I assume something I shouldn't have? We have exactly the right tools to solve both of these problems now. We'll build everything from the ground up in a way that is both impartial and necessary and the type checker will catch any logical mistakes. Our weapon of choice will be the CIC-based dependently typed programming langauge lean4.

## Defining the Naturals

We'll be assuming the [Peano axioms](https://en.wikipedia.org/wiki/Peano_axioms) to define the naturals- which has been standard practice for centuries. We won't use all of them and importantly no other assumptions will be made! I'll try to document which axioms correspond to which 'boilerplate code', which in this case is code that we need/features we rely on separate to the math we create.

![Peano Axioms](/assets/img/peano_axioms.png)

Only the first 5 axioms are real Peano axioms. Equality precedes the axioms and is built into ZFC itself. This means the orthodox definition does not quantify that 'objects' in an equivalence relation be naturals, and there is no need to introduce this definition when discussing the Peano axioms. Then, there is usually a fourth equality axiom introduced specifically for the naturals stating that they are closed under equality. Since we are 'skipping' sets and only constructing naturals (via types) it is appropriate to axiomise equality. Natural addition is usually constructed by the mathematician for their own needs; it is not inherently part of the Peano axioms. Since we are assuming addition exists to prove commutativity we will axiomize the [standard definition](https://en.wikipedia.org/wiki/Peano_axioms#:~:text=order%20theory%20below.-,Defining%20arithmetic%20operations%20and%20relations,-%5Bedit%5D).


To define a new datatype, the naturals, we use the `inductive` keyword.

```haskell
inductive MyNat : Type where
  | zero : MyNat                                                                            -- by 1.
  | succ : MyNat → MyNat                                                                    -- by 2.
```

We'll call our naturals `MyNat`s to avoid naming conflicts and potential ambiguity when talking about the real lean `Nat`s, which have more structure than the minimal skeleton we define here. The axioms tell us that `0` is a `MyNat` and we have an abstract `S` unary operation we can apply to a `MyNat` to yield another `MyNat`. The first line tells us the kind of `MyNat` is `Type`- so `MyNat` is not indexed by other types. In that sense it is 'simple' as you would expect the naturals to be. `MyNat` has two data constructors- `zero` produces a `MyNat` for us automatically, while `succ` requires a `MyNat` to iterate on. Hence it has the type `MyNat → MyNat`. Lean4 tries to be idiomatic with mathematical expressions, so many functional programming syntaxes are replaced with the unicode symbols that inspired them (e.g. `->` becoming `→`.)

## Addition

```haskell
def myAdd : MyNat → MyNat → MyNat
  | a, .zero => a                                                                           -- by 9.
  | a, .succ b => MyNat.succ (myAdd a b)                                                    -- by 10.
```

Note we have used `.zero` to refer to `MyNat.zero` and `.succ` to refer to `MyNat.succ`, and that we've called our function `myAdd` to keep it distinct from anything preexisting or floating around in the namespace.

These two recursive rules for computing addition result in a definition of addition that is both intuitively correct and has been shown to be consistent. That is, we cannot derive a contradiction within the system such as $$0 = 1$$. While Godel's second incompleteness thoerem shows that we cannot verify the Peano axioms are consistent from within them, Godel himself published [a method for showing the consistency of arithmetic using type theory!](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1746-8361.1958.tb01464.x) An important thing to note is that the 10th axiom assumes recursion has been proved to be valid which we have not shown here. Languages like lean are able to calculate the recursive call will always terminate (which is important if we want our program to type check!) by requiring that recursive functions are defined structurally, as we did. This means that it is easy to see that recursion proceeds on 'structurally smaller' inputs. It also explains why we cannot piggyback off the termination checker to verify [longstanding unsolved conjectures](https://en.wikipedia.org/wiki/Collatz_conjecture). Since addition transforms taking the sum of `a` and `succ b` to `a` and `b`, which are definitionally smaller, lean knows addition will terminate.

## First Theorems

```haskell
theorem add_zero (a : MyNat) : myAdd a .zero = a := by
  rfl                                                                                       -- by 6.

theorem add_succ (a b : MyNat) : myAdd a (MyNat.succ b) = MyNat.succ (myAdd a b) := by
  rfl                                                                                       -- by 6.
```

Our first theorems have no substance- we are just giving definitionally equal terms a name for easy future reference. Theorems are semantically identical to functions, the main difference being the return type of a theorem must be some kind of proposition- which `a = b` is and `TwoPlusTwoEqualsFour` (from the last article) isn't. `add_zero` is otherwise a function that accepts a parameter `a` of type `MyNat` and returns a value of type `myAdd a .zero = a`. Look at how the proposition is formulated- we are calling a function `myAdd` on an unknown input `a` whose type is `MyNat`- this becomes the `lhs` of the return type of the theorem. Additionally, the term `a` will never exist, all we want is the type of `a`. This can be thought of logically as a hypothetical, or an 'if-then'. 'If' `a` is provided 'then' the following proposition must hold. This is the motivation behind the arrow syntax in functional programming, to be consistent with logical implication. 

Remember the only tool we have so far for constructing proofs of equality is `Refl`, known as `rfl` in lean (`refl` with a lowercase 'r' also exists but has a different meaning). Remember that `rfl` takes a term `x` implicitly and constructs a term of the type `x = x`. We didn't provide this term- it is implicit and the lean4 elaborator (part of the compiler) simply guessed that it should use `a` as `x`. Lean remembers the definition for `myAdd` which says that `myAdd a MyNat.zero` is definitionally equal to `a`. So it is able to use that rule to reduce the required type from `myAdd a .zero = a` to `a = a` and then construct it from the parameter `a`, by invoking `rfl`. This process of changing the required type is known as manipulating the 'goal' and is a central part of proofs. We can manipulate goals in all kinds of complex ways such as splitting them up into cases, proving things via contradiction, etc. Techniques for doing so are called 'tactics'- we just used the `refl` tactic. For the theorem `add_succ` lean does something similar, this time using the `myAdd` definition for `a, MyNat.succ b`. Every time we invoke `rfl` we are using the 6th axiom- we are assuming `a = a` in order to show it is inhabited.

Before we can prove commutativity (we will refer to this theorem from now on as `add_comm`) we need to prove some supporting theorems. Unfortunately I can't just explain why we need these particular theorems. Halmos' text quite literally gave instruction to prove exactly these specific supporting theorems and use them in the commutativity proof, which (as mentioned) I found unsatisfying. With the convenience of a vigilant type checker, I was able to explore freely trying to prove `add_comm`, warned whenever I skipped a step or assumed something incorrectly. It felt good to arrive at a conclusion on my own: I ended up figuring out it was easiest to use exactly the theorems that Halmos laid out. Let's prove them.

## Mathematical Induction

```haskell
theorem zero_add (a : MyNat) : myAdd .zero a = a := by                                      -- the 'by' keyword precedes 'induction' to enter induction
  induction a with                                                                          -- by 5.
  | zero => rfl                                                                             -- by 6.
  | succ b ih =>                                                                            -- by 5.
    rw [add_succ, ih]                                                                       -- by 8.
```

Side note: notating each step to declare with exact precision how we were able to get to that step reminds me of the [style of proof when proving logical equivalence](https://math.stackexchange.com/questions/1237713/proving-p-to-q-landp-to-r-equiv-p-toq-land-r-using-logic-laws-short?rq=1) in discrete math. After all, that's exactly what we're doing- 'rigid symbol manipulation'.
{: .prompt-info }

Now we declare a non trivial theorem. `zero_add` is the theorem that $$\forall n \in \mathbb{N} : 0 + n = n$$, not to be confused with `add_zero` which is definitionally true. Since we're not working exclusively with theorems where `lhs = rhs`, we need a new tactic. We'll use `5.`, induction. The premise of mathematical induction is simple. Let $$P(x)$$ be a proposition on a natural, $$x$$. If $$P(0)$$ and $$P(n)$$ implies $$P(n + 1)$$ for all $$n$$, there is a natural cascading effect which proves $$P$$ for all naturals- $$P(0)$$ implies $$P(1)$$ which implies $$P(2)$$ and so on. Much like a toppling domino, triggering the next one in the sequence.

![Mathematical induction](/assets/img/induction.png)

The amazing thing about induction in lean is that there is no inhererent logic in the induction keyword itself. Remember that in lean the elaborator knows about all the finitely many ways to construct an object. So if you can prove that no matter how an object $$x$$ is constructed, that $$P(x)$$ holds, you have proved $$P$$ for all $$x$$! This is why GADTs in lean are known as 'inductive' data types- because they are defined inductively. The data type itself is where the induction 'comes from'- which means you can use the induction tactic on any data type, like `List` or `Tree`. Consider the fact that to construct a `MyNat` using the `.succ` data constructor you need another `MyNat`. This naturally corresponds to the induction hypothesis, that $$P$$ holds for some $$k$$, that is a usable assumption when proving $$P$$ holds for $$k + 1$$, which is incredible!

What the induction tactic does is, for each data constructor, provide us with the relevant inductive hypotheses. These constructors must be handled one by one in the pattern match syntax using `|` (if) and `=>` (then). Since `.zero` is constructed from nothing, it must be proven in a vacuum with no hypotheses. Luckily for us, the zero case is trivially true (from the definition of `myAdd` in case you forgot.) For `.succ` we are provided `ih` (the inductive hypothesis)- which is a theorem we can use exclusively inside the `succ` block in `zero_add`. The VS Code lean extension is fantastic when it comes to elucidating goals and the types of variables. On hover, we can see the type of the inductive hypothesis is the goal of `zero_add` and the mini-goal is the goal of `zero_add` with `b` replaced by `.succ b`.

![The type of the inductive hypothesis](/assets/img/induction_zero_add.png)

This makes our working expression `myAdd .zero (.succ b) = .succ b` which we hope to will manipulate into something we know is definitionally true- so we can use `rfl` (which is still the only way we really know how to close a proof). Notice how we've abstracted beyond producing terms of a type now- we're able to interpret what we're doing purely as algebraic/logical manipulation and rearrangement. To manipulate our working expression into the expected expression, we will introduce another tactic: `rw`. `rw` stands for rewrite- it takes an equality proposition eg `a = b` and replaces all instances of `a` with `b` in the working expression. `rw` also tries to close the goal with `rfl` and fails silently if it cannot. `rw` can also chain multiple rewrites together as we will do. By applying `add_succ`, which states that `myAdd x (.succ y)` is the same as `.succ (myAdd x y)`, we rewrite `myAdd .zero (.succ b) = .succ b` into `.succ (myAdd .zero b) = .succ b`. `ih` states that `myAdd .zero a = a`. Applying this, our goal not only becomes `.succ b = .succ b` but is successfully closed by `rfl`.

You might start to notice that despite all this fancy technology and tactics, we are still proving things basically the same way we would on paper. Fundamentally, a proof assistant is only an assistant and a human operator must do the work of writing out the definitions and proofs. But we will hopefully see in future proofs just how advanced and automatic tactics can get. When coupled with copilot, proof writing becomes not just more formally correct but much, much faster than it could ever be on paper.
{: .prompt-info }

```haskell
theorem succ_add (a b : MyNat) : myAdd (MyNat.succ a) b = MyNat.succ (myAdd a b) := by
  induction b with                                                                          -- by 5.
  | zero => rfl                                                                             -- by 6.
  | succ b ih =>                                                                            -- by 5.
    rw [add_succ, ih]                                                                       -- by 8.
    rw [← add_succ]                                                                         -- by 7.
```

The proof of `succ_add` is similarly straightforward. The only new addition is the backwards arrow `←` in `rw [← add_succ]`. `rw a = b` has a very specific, algorithmic purpose- it substitutes `b` in every place where there was previously an `a` in the working expression. `←` reverses the substitution, writing `a` in every place where there was previously a `b`. This is an application of the symmetry of equality- if `a` is equal to and replaceable by `b`, `b` is equal to and replaceable by `a`.

## Putting It All Together

```haskell
theorem add_comm (a b : MyNat) : myAdd a b = myAdd b a := by
  induction b with                                                                          -- by 5.
  | zero =>                                                                                 -- by 5.
    rw [add_zero, zero_add]                                                                 -- by 8.
  | succ b ih =>                                                                            -- by 5.
    rw [add_succ, succ_add, ih]                                                             -- by 8.
```

Having meticulously defined and practiced all the tactics and constructs we need in previous theorems, the proof for `add_comm` becomes simple. This proof is verified at compile time with a double blue tick in the editor- without ever needing to call the theorem as a function to execute it. Isn't it rewarding to chunk down a (relatively) complex proof into manageble lemmas, cracking them one by one before finally tackling the final boss? To need a nonobvious substitution midway through an advanced manipulation and to have it readily available? I hope I have both equipped you with the tools and knowledge necessary to start proving things on your own, as well as provided you with inspiration for doing so.

As a parting gift, here's the proof for associativity of natural addition for comparison- you can see the rewrites it uses are simpler, even though there are many of them. This is why usually associativity is proven before commutativity, it doesn't need as many supporting lemmas.

```haskell
theorem add_assoc (a b c : MyNat) : myAdd (myAdd a b) c = myAdd a (myAdd b c) := by
  induction c with
  | zero =>
    rw [add_zero, add_zero]
  | succ c ih =>
    rw [add_succ, add_succ, add_succ, ih]
```

## Ending Note

This article was a ton of fun to write. I wrote it at the same time as Part I and decided at the last minute to cut them up since they sort of fell into two halves naturally. I've been studying type theory in an academic context for about a year now and I've recently starting proving my software specifications with lean, so I felt comfortable writing about type theory without any prior research. If you have any suggestions for which direction to take this series in, submit a PR with your request [here](https://github.com/Mukundks2004/BetweenBooks). For now I'll give type theory a break and move on to some more artsy subjects.