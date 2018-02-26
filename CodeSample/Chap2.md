# Chapter 2. Why function purity matters

## 2.1. What is function purity?

We want function to do something - meaning to have side effect.
So let see the different between pure & impure function

| Pure functions                                      |                     Impure functions                      |
| --------------------------------------------------- | :-------------------------------------------------------: |
| The output depends entirely on the input arguments. | Factors other than input arguments may affect the output. |
| Cause no side effects.                              |                  May cause side effects.                  |

### Impure functions

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

### Pure functions

Pure functions have no side effects, and their output is solely determined by their
inputs.
