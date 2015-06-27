### Free monads

Here we're going to examine the monad called `Free`:

```haskell
data Free f a
    = Pure a
    | f (Free f a)
```

Curiously, when `f` is a functor, `Free f` is a monad:

```haskell
    instance Functor f => Monad (Free f) where ...
```

And that's where I'm going to stop. You can learn a lot more about free monads by combing through the Haskell blogosphere, and I highly recommend it. My goal here is to help build an intution of what free monads _are_ by showing simple _examples_ of free monads, defined over various functors.

This goes against the idea of using "freeness" as the primary means of understanding a structure. A free _foo_, after all, is the "simplest" _foo_ that obeys the _foo_ laws and no more. Any other more specialized _foo_ is an unscrupulous distraction from _foo_, and examining its operational details will only guide you down an incorrect path of understanding. _Don't try to understand monoids by looking at integers under addition_, so they say, _just look at lists. Otherwise, you may walk away believing that the associative binary operator `<>` must also be commutative, as is `+`_.

As long as you are cognizant of this trap, there is no harm in asking, "forget all of this left-adjoint to a forgetful functor monad homomorphism crap, what the hell _is_ a free monad?". It's a valid question. There are many valid paths to understanding a new concept, and I am not discouraging a principled, bottom-up understanding. I just happen to learn top-down. This small series of articles is an attempt at that. But to echo the advocates of abstraction: using examples and analogies is a necessary evil when trying to explain an abstract concept in terms of something more concrete, as in the integers-form-monoids-under-addition example above. The most dangerous part of learning by example is learning by _wrong_ example. Take every comparison with a grain of salt, because in the end, it's true: a free monad is exactly what it's defined as, and nothing more.

### Functors

Now, the only prerequisite for understanding `Free f` is having a good understanding of `f`, which we can just assume will always be a `Functor`. You can play around using `Free` with a non-`Functor` `f` if you want, but I don't think you will get very far. You certainly don't get the `Monad` instance for free.

Anyways, `Functor` is simultaneously one of the simplest typeclasses, and one of the hardest to develop a correct intuition for. Like monads, monoids, any any other Haskell concept that toes the line of category theory, the internet is rife with incorrect intuitions written by amateur bloggers. Many readers will include me in that list.

_A functor is like a value inside a box._ Is it really? _Say you have an `a` thing, but you don't really, because it's a `Maybe a`. It's an `a`, but in a box, and that box might have nothing, or `Nothing`, ha-ha! Anyways, now let's write the `Functor` instance for `Maybe` because we can._ 

Next, please.

_A functor is like a value with a context._ What's... a "context"? _Well, erm, okay look at `Maybe` for example. Its context is "might be null". Alright now look at `[]`. Its context is "zero or more". So I suppose you just have to use functors to understand them because each functor has a different context._ Concluding that you have to use functors to understand them. Then what's the point of mentioning the word "context" at all?

Allow me to offer my own intuition: a functor `f a` is a producer of `a`. It may produce no `a`, it may produce many. It may produce an `a` only when certain conditions are met, such as being provided some other value. It may produce an `a` and also produce some `Int`. It may produce an `a` after having an observable effect on the world. 

There is no way to ask this producer for its `a`, ever, even if it has one to give, because it also might not even have one. How does this analogy compare to reality, wherein a functor is exactly what it's defined as, and no more? Well, this structure `f a` can, when the planets align, produce an `a`. We have no way of observing that `a` for ourselves, but surely if we give this producer an action `(a -> b)`, to perform on that `a`, it can do so. And that's `fmap`.

```haskell
class Functor f where
    fmap :: (a -> b) -> f a -> f b
```
---
NB: The other, less useful intution for a functor (to me, anyway) comes from category theory, where a functor maps the morphisms and objects from one category to another. Look at `fmap` in its curried form: 
```haskell
fmap :: (a -> b) -> (f a -> f b)
```
We see that it maps morphims in one category to another (both happen to be `Hask`, but the latter is some subset of objects in `Hask`), and the type constructor `f` maps the objects.
