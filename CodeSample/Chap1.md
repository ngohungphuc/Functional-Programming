### 1.1.1. Functions as first-class values

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
