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

## 1.4. Higher-order functions

HOFs are functions that take other functions as inputs or return a function as output, or both.

### 1.4.1. Functions that depend on other functions

Some HOFs take other functions as arguments and invoke them in order to do their work

`List.Sort`, when called with a `Comparison` delegate, is a method that says: “OK, I’ll sort myself, as
long as you tell me how to compare any two elements that I contain.” Sort does the job of
sorting, but the caller can decide what logic to use for comparing.

> Example A HOF that optionally invokes the given function

```csharp
class Cache<T> where T : class
{
public T Get(Guid id) => //...
public T Get(Guid id, Func<T> onMiss)
=> Get(id) ?? onMiss();
}
```

The preceding examples illustrate HOFs that take a function as input (often referred to as a
callback or a continuation) and use it to perform a task or to compute a value. This is perhaps
the most common pattern for HOFs, and it’s sometimes referred to as inversion of control: the
caller of the HOF decides what to do by supplying a function, and the callee decides when to do
it by invoking the given function.

### 1.4.2. Adapter functions

Some HOFs don’t apply the given function at all, but rather return a new function, somehow
related to the function given as an argument. For example, say you have a function that performs
integer division:

> Example

```csharp
Func<int, int, int> divide = (x, y) => x / y;
divide(10, 2) // => 5
```

You can write a generic HOF that modifies any binary function by swapping the order of its
arguments:

> Example

```csharp
static Func<T2, T1, R> SwapArgs<T1, T2, R>(this Func<T1, T2, R> f)
=> (t2, t1) => f(t1, t2);
```

Technically, it would be more correct to say that SwapArgs returns a new function that invokes the
given function with the arguments in the reverse order. But on an intuitive level, I find it easier to
think that I’m getting back a modified version of the original function.
You can now modify the original division function by applying SwapArgs:

> Example

```csharp
var divideBy = divide.SwapArgs();
divideBy(2, 10) // => 5
```

### 1.4.3. Functions that create other functions

Sometimes you’ll write functions whose primary purpose is to create other functions—you can
think of them as function factories. The following example uses a lambda to filter a sequence of
numbers, keeping only those divisible by 2:

> Example

```csharp
var range = Enumerable.Range(1, 20);
range.Where(i => i % 2 == 0)
// => [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
```

What if you wanted something more general, like being able to filter for numbers divisible by
any number, n? You could define a function that takes n and yields a suitable predicate that will
evaluate whether any given number is divisible by n:

> Example

```csharp
Func<int, bool> isMod(int n) => i => i % n == 0;
```

## 1.5. Using HOFs to avoid duplication

### 1.5.1. Encapsulating setup and teardown into a HOF

> Example

```csharp
using Dapper;
using static ConnectionHelper;
public class DbLogger
{
string connString;
public void Log(LogMessage message)
=> Connect(connString, c => c.Execute("sp_create_log"
, message, commandType: CommandType.StoredProcedure));

public IEnumerable<LogMessage> GetLogs(DateTime since)
=> Connect(connString, c => c.Query<LogMessage>(@"SELECT *
FROM [Logs] WHERE [Timestamp] > @since", new {since = since}));
}
```

### 1.5.2. Turning the using statement into a HOF

### 1.5.3. Tradeoffs of HOFs

```csharp
// initial implementation
public void Log(LogMessage msg)
{
    using (var conn = new SqlConnection(connString))
    {
        int affectedRows = conn.Execute("sp_create_log"
        , msg, commandType: CommandType.StoredProcedure);
    }
}

// refactored implementation
public void Log(LogMessage message)
=> Connect(connString, c => c.Execute("sp_create_log"
, message, commandType: CommandType.StoredProcedure));
(
```

This is a good illustration of the benefits you can get from using HOFs that take a function as an
argument:

* `Conciseness—` The new version is obviously more concise. Generally speaking, the more
  intricate the setup/teardown and the more widely it’s required, the more benefit you get by
  abstracting it into a HOF.
* `Avoid duplication`— The whole setup/teardown logic is now performed in a single place.
* `Separation of concerns`— You’ve managed to isolate connection management into the
  ConnectionHelper class, so DbLogger need only concern itself with logging-specific logic.

## 1.6. Benefits of functional programming

* `Cleaner code`— Apart from the previously mentioned conciseness, FP leads to more
  expressive, more readable, and more easily testable code. Clean code is not just a
  developer’s intellectual pleasure, but it also leads to huge economic benefits for the
  business through reduced maintenance costs.
* `Better support for concurrency`— Several factors, from multi-core CPUs to distributed
  systems, bring a high degree of concurrency to your applications. Concurrency is
  traditionally associated with difficult problems such as deadlocks, lost updates, and more;
  FP offers techniques that prevent these problems from occurring.
* `A multi-paradigm approach`— They say that if the only tool you have is a hammer, every
  problem will look like a nail. Conversely, the more angles from which you can view a
  given problem, the more likely it is that you’ll find an optimal solution.
