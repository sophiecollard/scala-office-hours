# Defining your own types in Scala

## Case classes

Case classes are basically classes where a lot of the boilerplate (such as arguments immutability or methods for equality checks and pretty-printing) is taken care of for you.

Here's an example of a case class definition and usage:

```scala
case class Person(name: String, age: Int)
val sophie = Person("Sophie", 34)

println(sophie)
// Person(Sophie,34)

sophie == Person("Sophie", 34)
// true

val sophieNextYear = sophie.copy(age = 35)
```

And here's the same thing with a regular class:

```scala
class Person(val name: String, val age: Int)
val sophie = new Person("Sophie", 34)

println(sophie)
// Person@718c736f

sophie == new Person("Sophie", 34)
// false

val sophieNextYear = new Person(sophie.name, 35)
```

## Case objects

Case objects are used to define singletons.

```scala
case object Martin {
  val fullName: String = "Martin Ordersky"
  val livesIn: String = "Lausanne, Switzerland"
}
```

## Sealed traits

Sealed traits can be used for defining _sum_ or _union_ types. In the example below, an instance of `Food` could be `Lettuce` or `Steak` or `Tuna` or something else (`Other`).

```scala
sealed trait Food {
  final def isVegetarian: Boolean = this match {
    case Lettuce              => true
    case Steak | Tuna         => false
    case Other(_, vegetarian) => vegetarian
  }
}

case object Lettuce extends Food
case object Steak extends Food
case object Tuna extends Food
final case class Other(name: String, vegetarian: Boolean = false) extends Food

Lettuce.isVegetarian
// true
Other("Lasagne").isVegetarian
// false
```

### Pattern matching

The implementation of the `isVegetarian` method on `Food` is an example of pattern matching.

You can think of pattern matching as a [switch expression](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/switch-expression) on steroids. Notice how we didn't have to use a catch-all `case _ => ???` and are able to extract the value of the `vegetarian` attribute on the `Other` case class.

### Exhaustive checking

The `sealed` keyword requires that any type extending `Food` be defined in the same file. This allows the compiler to do something known as exhaustive checking.

Let's say I added another type of food:

```scala
case object Ratatouille extends Food
```

If I tried to compile my code without having made any other changes, the compiler would remind me to update my `isVegetarian` method on `Food` to handle my new `Ratatouille` type:

```
warning: match may not be exhaustive.
It would fail on the following input: Ratatouille
  final def isVegetarian: Boolean = this match {
                                    ^
```

To get my code to compile, I would have to update my `isVegetarian` method as follows:

```scala
sealed trait Food {
  final def isVegetarian: Boolean = this match {
    case Lettuce | Ratatouille  => true
    case Steak | Tuna           => false
    case Other(_, isVegetarian) => isVegetarian
  }
}
```

## Sealed abstract classes

Another way of defining _sum_ or _union_ types is via sealed abstract classes.

```scala
sealed abstract class Pet(
  val name: String,
  val favouriteFoods: Set[Food]
)

final case class Cat(
  override val name: String,
  override val favouriteFoods: Set[Food]
) extends Pet(name, favouriteFoods)

final case class Dog(
  override val name: String,
  override val favouriteFoods: Set[Food],
  breedName: String
) extends Pet(name, favouriteFoods)

final case class Turtle(
  override val name: String
) extends Pet(name, Set(Lettuce))
```

## Inheritance rules

Read the following table as: _Can {row} inherit from {column}?_

|                | class | object | case class   | case object | abstract class | trait            |
| -------------- | ----- | ------ | ------------ | ----------- | -------------- | ---------------- |
| class          | yes   | no     | yes          | no          | yes            | yes (multiple)   |
| object         | yes   | no     | yes          | no          | yes            | yes (multiple)   |
| case class     | yes   | no     | **no**       | **no**      | yes            | yes (multiple)   |
| case object    | yes   | no     | **no**       | **no**      | yes            | yes (multiple)   |
| abstract class | yes   | no     | yes          | no          | yes            | yes (multiple)   |
| trait          | yes   | no     | yes          | no          | yes            | yes (multiple)   |

### Case-to-case inheritance

Case-to-case inheritance is prohibited. A `case class` or `case object` cannot extend another `case class`.

### Mixins

While a classes, objects and traits may only extend one other class, they can extends multiple traits. This is sometimes referred to as **mixins**.

```scala
trait HasName {
  def name: String
}

trait Barks {
  final def bark: String =
    "Woof!"
}

final case class Dog(name: String, age: Int) extends HasName with Barks

val hamlet = Dog("Hamlet", 9)
hamlet.name
// Hamlet
hamlet.age
// 9
hamlet.bark
// Woof!
```

## Resources

  * [Wikipedia Article on Algebraic Data Types](https://en.wikipedia.org/wiki/Algebraic_data_type)
