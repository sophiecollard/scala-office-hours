# Referential Transparency


> An expression is referentially transparent if it can be replaced with its corresponding value without changing the program's behavior. This requires that the expression be pure - its value must be the same for the same inputs and its evaluation must have no side effects.

_Source: [Wikipedia](https://en.wikipedia.org/wiki/Referential_transparency)_

## Purity

### Pure functions

For a given set of inputs, a pure function should always return the same set of outputs.

For instance, the function which given the radius of a circle computes its circumference is pure:

```scala
import scala.math.Pi

def computeCircumference(radius: Double): Double =
  2 * Pi * radius
```

### Impure functions

For a given set of inputs, an impure function may return a different set of outputs from execution to the next.

For instance, the function that gets a user's name will return a different output from one execution to the next, depending on what the user inputs in the terminal:

```scala
import scala.io.StdIn

def getUsername(): String =
  StdIn.readLine()
```

### Side effects

Functions perform side effects when they read or modify some state variables outside of their local environment.

Some examples of side effects are:
  * reading from / writing to a database
  * reading from / writing to the terminal
  * mutating a variable

In Scala, most functions that either **a)** do not have arguments (such as our `getUsername` function above) or **b)** have a return type of `Unit` perform side effects and are therefore impure.

## Why it matters

The example below is taken from the [Get Programming with Haskell](https://www.manning.com/books/get-programming-with-haskell?query=get%20programming%20with%20haskell) book.

The following code snippet constitutes valid code in 3 languages: JavaScript, Python and Ruby.

```js
myList = [1, 2, 3]
myList.reverse()
otherList = myList.reverse()
```

What will be the value of `otherList` in each of these languages?

You can check your responses by executing each code snippet:

```sh
node example.js
python3 example.py
ruby example.rb
```
