# Optional Functor from scratch!
## :christmas_tree:Kodskolan winter hack camp writeup:christmas_tree:

:mask:  Please!! :mask:

Ever wondered what a Functor is? Are your Optionals not Monads yet? Haven't used the tie-fighter?

Hey, lets do this!:dark_sunglasses:

## We want Optionals!
First of we need a "type" of `Optional`. This is the "wrapper" the "Context" the "Event" talked about in all `Functor` tutorials. We use some god old type declarations in Haskell.

```haskell
module Optional where

data Optional a = Some a | None deriving (Eq, Show)
```
There we have it! We are using the a sum type, either we can construct our value of `Optional` as a `Some a` or as a `None`. The part about deriving is just us telling Haskell to make our `Optional` printable and comparable. We are above doing such basic implementations. We are aiming for `Monads`(a bit later, for now `Functors`) remember!

So lets declare some variables to work with.
```haskell
optional1 = Some 1
optional2 = Some 2
optional3 = None
```
In the world of fancy words we do not have much. If you are reading the entertaining blog posts about `Functors` what we have here is just our own `FA` wrapper type.

## We want functors!:alien:	
Well we have all heard about the functor laws, right? Well lets ignore them. A `Functor` for us should just be able to do two things.

* Take all elements from any type `A` and wrap it in a type `FA`.
* Take all functions that takes a value from a type `A` and returns a value of type `B`, and translates these functions to work with values of `FA` to `FB` instead.

In Haskell we can write is as types where we want our `Functor` to be able to:
* `a -> f a`
* `(a -> b) -> (f a -> f b)`

The first one is easy. We already have it in our constructor `Some a`. It takes a value of type `A` and returns a value of type `Optional A`.

```haskell
aNum = 3 :: Integer
optional4 = Some aNum :: Optional Integer
```
To speak functorish, our `Some a` satesfies `a -> f a` the first requirement.

The second requirement will need a small detour.

### The well known Functor: List
We all know about lists. And we all know about `map`, that distributes a function over a `List`. How is this a `Functor`? Well we have a way of putting values of type `A` in a list `[A]`. We just ... put them in the list.

```haskell
anotherNum = 4 :: Integer
listOfNum = [aNum, anotherNum] :: [Integer] -- [3, 4]
```
Then we can `map` a function over our list.
```haskell
addOne a = a + 1
mappedListOfNum = map addOne listOfNum -- [4, 5]
```
Now it is time to take a mental leap into the conceptual world of mathematics. What just happened here? In programming terms we just distributed the function `addOne` over the elements of a list. But at the conceptual level our `map` function actually converted a function `a -> b` into a function `f a -> f b` (in our examples here we actually have a function `a -> a` becaus of us just working in number land. But there is nothing in the declaration of `a -> b` that says that `a` cannot equal `b`). Haskell's currying can show us this.
```haskell
addOneToElementsOfList = map addOne :: Num b => [b] -> [b]
```
As we can see Haskell knows we are only working with `Num`. But the interesting part is that `addOneToElementsOfList` is now a function between wrapped values! That is: map fulfills the second condition of a `Functor` for the "wrapper"("Context", "Event", whatever) type `List`. :massage:

### Back to our Optionals!

So what did this detour teach us? What we want to have to fulfill the second requirement is a `map` for our `Optional`. In haskell there is a "typeclass" called `Functor` which encapsulates this requirement. It kind of assumes that we already have the first requirement nailed by our constructor, so the only requirement is to implement a function `fmap` for our data type. Lets compare the type signatures for `fmap` and `map`.
```haskell
-- fmap :: Functor f => (a -> b) -> f a -> f b
-- map :: (a -> b) -> [a] -> [b]
```
What we can decipher here is that `map` is just a `fmap` for `List`. Lets do this for `Optional`.
```haskell
instance Functor Optional where
    fmap g None = None
    fmap g (Some a) = Some (g a)
```
If we try to "map" a function over `None` we just get `None` back. If we try to do it over `Some a` we just apply the value inside out `Optional` to the function and then again wrapping it in a `Some`. Thats it. `Optional` is now officially a `Functor`. :money_mouth_face:

## Lets use it!:bomb:
Now for some `Functor` fiesta on our `Optional`. (Remember addOne, optional1 and optional3 from before?)
```haskell
-- addOne :: Num a => a -> a, actually a + 1
-- optional1 :: Some Integer, actually Some 1
-- optional1 :: Some Integer, actually None
maybeResult1 = fmap addOne optional1 -- Some 2
maybeResult2 = fmap addOne optional3 -- None
```
:tada::tada: Wow good work `Optional` you `Functor`! :tada::tada:

5000 :clap:
