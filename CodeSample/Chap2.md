# Chapter 2. Why function purity matters

## 2.1. What is function purity?

We want function to do something - meaning to have side effect.
So let see the different between pure & impure function

| Pure functions                                      |                     Impure functions                      |
| --------------------------------------------------- | :-------------------------------------------------------: |
| The output depends entirely on the input arguments. | Factors other than input arguments may affect the output. |
| Cause no side effects.                              |                  May cause side effects.                  |

### 2.1.1. Purity and side effects

#### Impure functions

A function is said to have
side effects if it does any of the following:

* Mutates global state—“Global” here means any state that’s visible outside of the
  function’s scope. For example, a private instance field is considered global because it’s
  visible from all methods within the class.
* Mutates its input arguments
* Throws exceptions
* Performs any I/O operation— This includes any interaction between the program and the
  external world, including reading from or writing to the console, the filesystem, or a
  database, and interacting with any process outside the application’s boundary.

#### Pure functions

Pure functions have no side effects, and their output is solely determined by their
inputs.

### 2.1.2. Strategies for managing side effects

Ok, let’s aim to use pure functions whenever possible. But is it always possible? Is it ever
possible? Well, if you look at the list of things considered side effects, it’s a pretty mixed bag, so
the strategies for managing side effects depend on the types of side effects in question.

#### Isolate I/O effects

First with IO, which is always considered a side effect. First, here are a few examples that
will clarify why functions that perform I/O can never be pure:

* A function that takes a URL and returns the resource at that URL will yield a different
  result any time the remote resource changes, or it may throw an error if the connection is
  unavailable.
* A function that takes a file path and contents to be written to a file may throw an error if
  the directory doesn’t exist, or if the process hosting the program lacks write permissions.
* A function that returns the current time from the system clock will return a different result
  at any instant.

  As you can see, any dependency on the external world gets in the way of function purity because
  the state of the world affects the function’s return value. On the other hand, if your program is to
  do anything of use, there’s no escaping the fact that some I/O is required. Even a purely
  mathematical program that just performs a computation must perform some I/O to communicate
  its result. So some of your code will have to be impure.

Consider the following code:

```cs
WriteLine("Enter your name:");
var name = ReadLine();
WriteLine($"Hello {name}");
```

This trivial program (assume it’s wrapped in a Main method) mixes I/O with logic that could be
extracted in a pure function:

```cs
static string GreetingFor(string name) => $"Hello {name}";
```
