# HardHat
Practice functional language.
The emphasis is on testing out a few features I have not 
seen in other languages that I think would be nice. 

## Implementation
I hope to implement the parser in Haskell with Parsec. 

## Syntax

### Basics
HardHat only has expressions that are evaluated to:
- Values: functions or conditionals that 'return' something. 
- Definitions: Setting values equal to something, whether a variable or a function. 
Definitions are done using the `:=` operator. 
- Actions: Something that acts on a state (e.g. I/O). 

### Types
HardHat has a few base types. One is a `String`, denoted with an opening and closing 
double-quote pair: `"Hello, World!"` \
The Numbers are `Int`, `Float`, and `Complex`.
HardHat will automatically convert `Int` to `Float` and from `Float` to `Complex` but will error if they have to be 
caste the other direction.  
Booleans are named `Bool`. 

For a value of a type we can say: `x :: type` \
for a function of one argument that takes in one argument of a type and outputs a different type: `foo :: type1 -> type2` \
for a function of two arguments of the same type and outputs a different type: `foo :: type1, type1 -> type2`.

I want to implement dependent types. Types can be made, these types are themselves governed by a 
language that allows the construction of new types at compile time. 
An example I hope to use is the `Matrix[r, c]` type, which takes in two Integers and then compile time checking 
will be able to enforce the proper matric multiplication. If a matrix has the same `r` and `c` then we will be able to 
find its eigenvalues for example. 

### Conditionals
if statements are in the form:
`if [Bool] then [value] else [value]` \
This statement evaluates to the type of the `[value]`.

### Functions
Functions are defined with:
```
foo (x?, y?) := [value using x and y as variables]
```
They are not curried so when a function has two or more parameters you need to put the elements into a tuple.
```
add :: Int, Int -> Int
result := add (1, 2)
# result is 3

plus1 :: Int -> Int
result := plus1 4
# result is 5
```

You can partially apply functions by replacing an argument with `?`.
```
plus1 := add (1, ?)
```

### Arrays
The default list-like object is an array (fixed-size contiguous block of memory). 
To define an array use: `xs := [0, 2, 4, 6, 8, 10]`. There is also a syntax sugar for 
arrays: `xs := [0, 2..=10]` or using the array comprehension syntax: 
`xs := [x for x in [0..=10] | isEven x]` (work in progress).

Indexing arrays is easy with `xs[i]` for the element at index `i`. 

The type of an array is `Array[type]` where the type is the type of the element in the array. 
If we have a function `foo` with the signature `foo :: type -> type`. 
An array of `type`s cannot be used as input since it does not have the correct type. 
```
xs := [1, 2, 3]
ys := [10, 20, 30]
# add :: Int, Int -> Int

result := add (1, 10)
# result is 11

result := add (xs, ys)
# This will throw a type error and not compile

result := add (&xs, &ys)
# result is [11, 22, 33]

result := add (@xs, @ys)
# result is [11, 21, 31, 12, 22, 32, 31, 32, 33]
```

### Actions
...

### Examples

#### fizzbuzz
Produce the fizzbuzz code for the numbers from 1 to 100. 
```
fizzbuzzN N? := if N % 3 == 0 then "fizz" else ""
             ++ if N % 5 == 0 then "buzz" else ""

result := fizzbuzzN &[1..=100]
```

#### combinations

```
group3 (a?, b?, c?) := [a, b, c]
xs := [0, 1]
ys := [0, 1]
zs := [0, 1]

result := group3 (@xs, @ys, @za)
# result is [[0, 0, 0], [0, 0, 1], [0, 1, 0], [0, 1, 1],
#            [1, 0, 0], [1, 0, 1], [1, 1, 0], [1, 1, 1]]
```
