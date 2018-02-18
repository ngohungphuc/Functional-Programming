# Chapter 1. Introducing functional programming

## 1.1. What is this thing called functional programming?

What exactly is functional programming? At a very high level, it’s a programming style that
emphasizes functions while avoiding state mutation. This definition is already twofold, as it
includes two fundamental concepts:

* Functions as first-class values
* Avoiding state mutation

### 1.1.1. Functions as first-class values

In a language where functions are first-class values, you can use them as inputs or outputs of
other functions, you can assign them to variables, and you can store them in collections. In other
words, you can do with functions all the operations that you can do with values of any other type.

> Example

```csharp
Func<int, int> triple = x => x * 3;
var range = Enumerable.Range(1, 3);
var triples = range.Select(triple);
triples // => [3, 6, 9]
```

In this example, you start by declaring a function that returns the triple of a given integer and
assigning it to the variable triple. You then use Range to create an IEnumerable<int> with the
values [1, 2, 3].

You then invoke Select (an extension method on IEnumerable), giving it the
range and the triple function as arguments; this creates a new IEnumerable containing the
elements obtained by applying the triple function to each element in the input range.

### 1.1.2. Avoiding state mutation

> Example

```csharp
int[] nums = { 1, 2, 3 };
nums[0] = 7;
nums // => [7, 2, 3]
```

Such updates are also called destructive updates, because the value stored prior to the update is
destroyed. These should always be avoided when coding functionally. (Purely functional
languages don’t allow in-place updates at all.)

Following this principle, sorting or filtering a list **should not modify the list in place but should
create a new, suitably filtered or sorted list without affecting the original**

## 1.2. How functional a language is C#?

C#
had support for functions as first-class values from the earliest version of the language through
the `Delegate` type, and the subsequent introduction of `lambda expressions` made the syntactic
support even better

As for supporting a programming model that avoids in-place updates, the fundamental
requirement in this area is that a language have garbage collection. Because you create modified
versions, rather than updating existing values in place, you want old versions to be garbage
collected as needed. Again, C# satisfies this requirement.

Ideally, the language should also discourage in-place updates. This is C#’s greatest shortcoming:
everything is mutable by default, and the programmer has to put in a substantial amount of effort
to achieve immutability. Fields and variables must explicitly be marked `readonly` to prevent
mutation.

### 1.2.1. The functional nature of LINQ

LINQ offers implementations for many common operations on lists (or, more generally, on
“sequences,” as instances of `IEnumerable` should technically be called), the most common of
which are `mapping`, `sorting`, and `filtering`

> Example

```csharp
Enumerable.Range(1, 100).
Where(i => i % 20 == 0).
OrderBy(i => -i).
Select(i => $"{i}%")
// => ["100%", "80%", "60%", "40%", "20%"]
```

Notice how `Where`, `OrderBy`, and `Select` all take functions as arguments and don’t mutate the given
`IEnumerable`, but return a new `IEnumerable` instead

**Common operations on sequences**

The LINQ library contains many methods for performing common operations on sequences, such
as the following:

* `Mapping` — Given a sequence and a function, mapping yields a new sequence with the
  elements obtained by applying the given function to each element in the given sequence
  (in LINQ, this is done with the Select method).

```csharp
Enumerable.Range(1, 3).Select(i => i * 3) // => [3, 6, 9]
```

* `Filtering` — Given a sequence and a predicate, filtering yields a new sequence consisting
  of the elements from the given sequence that pass the predicate (in LINQ, Where).

```csharp
Enumerable.Range(1, 10).Where(i => i % 3 == 0) // => [3, 6, 9]
```

* `Sorting` — Given a sequence and a key-selector function, sorting yields a new sequence
  ordered according to the key (in LINQ, OrderBy and OrderByDescending).

```csharp
Enumerable.Range(1, 5).OrderBy(i => -i) // => [5, 4, 3, 2, 1]
```

## 1.3. Thinking in functions

### 1.3.2. Representing functions in C

There are several language constructs in C# that you can use to represent functions:

* Methods
* Delegates
* Lambda expressions
* Dictionaries

#### Methods

Methods are the most common and idiomatic representation for functions in C#. For example,
the `System.Math` class includes methods representing many common mathematical functions.
Methods can represent functions, but they also fit into the object-oriented paradigm—they can be
used to implement interfaces, they can be overloaded, and so on.

The constructs that really enable you to program in a functional style are delegates and lambda
expressions.

#### Delegates

Delegates are type-safe function pointers. Type-safe here means that a delegate is strongly typed:
the types of the input and output values of the function are known at compile time, and
consistency is enforced by the compiler.

Creating a delegate is a two-step process: you first declare the delegate type and then provide an
implementation. (This is analogous to writing an interface and then instantiating a class
implementing that interface.)

The first step is done by using the delegate keyword and providing the signature for the delegate.
For example, .NET includes the following definition of a `Comparison<T>` delegate.

#### The Func and Action delegates

The .NET framework includes a couple of delegate “families” that can represent pretty much any
function type:

* `Func<R>` represents a function that takes no arguments and returns a result of type R.
* `Func<T1, R>` represents a function that takes an argument of type T1 and returns a result of
  type R.
* `Func<T1, T2, R>` represents a function that takes a T1 and a T2 and returns an R.

Since the introduction of `Func`, it has become rare to use custom delegates. For example, instead
of declaring a custom delegate like this,
`delegate Greeting Greeter(Person p);`

you can just use the type:

`Func<Person, Greeting>`

The type of Greeter in the preceding example is equivalent to, or “compatible with” `Func<Person, Greeting>`. In both cases it’s a function that takes a Person and returns a Greeting.

There’s a similar delegate family to represent actions—functions that have no return value, such
as void methods:

* `Action` represents an action with no input arguments.
* `Action<T1>` represents an action with an input argument of type T1.
* `Action<T1, T2>` and so on represent an action with several input arguments.

#### Lambda expressions

`Lambda expressions`, called `lambdas` for short, are used to declare a function inline. For example,
sorting a list of numbers alphabetically can be done with a lambda like so.

> Example Declaring a function inline with a lambda

```csharp
var list = Enumerable.Range(1, 10).Select(i => i * 3).ToList();
list // => [3, 6, 9, 12, 15, 18, 21, 24, 27, 30]
list.Sort((l, r) => l.ToString().CompareTo(r.ToString()));
list // => [12, 15, 18, 21, 24, 27, 3, 30, 6, 9]
```

If your function is short and you don’t need to reuse it elsewhere, lambdas offer the most
attractive notation. Also notice that in the preceding example, the compiler not only infers the
types of x and y to be int, it also converts the lambda to the delegate type `Comparison<int>`
expected by the Sort method, given that the provided lambda is compatible with this type.
