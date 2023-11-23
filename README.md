# HardHat
Practice functional language.
The emphasis is on testing out a few features I have not 
seen in other languages that I think would be nice. 

## Implementation
I hope to implement the parser in Haskell with Parsec. 

I believe I will then convert to C applying appropriate optimizations. 

## Syntax

### Basics
HardHat only has expressions that are evaluated to:
- Values: functions or conditionals that 'return' something. 
- Definitions: Setting values equal to something, whether a variable or a function. 
Definitions are done using the `:=` operator. Considering adding the `<=` assignment
operator denoting a variable is linear (can only be used once). 
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
An example I hope to use is the `Matrix{Int, Int, type}` type, which takes in two Integers and then compile time checking 
will be able to enforce the proper matric multiplication. If a matrix has the same `Int`s then we will be able to 
find its eigenvalues for example. 

### Conditionals
if statements are in the form:
`if [Bool] then [type] else [type]` \
This statement evaluates to the type of the `type`.

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

The type of an array is `Array{type}` where the type is the type of the element in the array. 
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
(similar to monads I think)...

### Units
Units can be added to Numbers (and probably other types) by using `'[unitName][number]*`. 
The number is treated as. Input if the unit's input is not manually declared then the number will 
be treated as an exponent.
For example: 
```
mass := 4.5'kg  # 4.5 kg
velocity := 30'm'/s  # 30 m/s
energy := 9'J
energy := 9'kg'm2'/s2
```
In this example, the units for energy can be written as either Jules or in Base SI units. 
You can define conversions for units:
```
conversion: 's's = 's2
conversion: 'J = 'kg'm2'/s2
# this implies that 'J2 == 'kg'm2'/s2'kg'm2'/s2 == 'kg2'm4'/s4
```
By default, the left-hand side of the unit conversion is considered preferred. 
The internal representation of the units should not matter, when used they will perform. 
Adding different units will not throw an error, but it will not combine the results:
```
result = 1'kg + 4'm'/s + 6'm / 3's
# result is 1'kg + 6'm'/s
```
Since you can take in a value that has terms with different units, they can be 
used as objects in a way, where you just pull a certain term out. 

!! I have not decided how the units perform with the type system !!

### Undefined Varbales / Symbols
...

### Parallelization / Concurrency / Multithreading
(I want to use the `functionName||threadCount (@xs, ...)` format)...

### Ideas
#### ArrayLists
ArrayLists act as lists, you can append to them, remove from them, etc. They will be implemented as blocks of memory, each an array, 
where the last element in the array points to the next array. The type is `ArrayList{Int, type}` where the `Int` in the type 
construction is the size of the contiguous blocks that contain the values minus the amount for the pointer. 
`ArrayList{4, Bit] is 0110-> 0001-> 101__`

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
