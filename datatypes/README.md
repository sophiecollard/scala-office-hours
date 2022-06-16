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

When defining case classes, I like to use the `final` keyword to make sure they cannot be extended.

```scala
final case class Person(name: String, age: Int)
```

## Case objects

Case objects are used to define singletons.

```scala
case object Martin {
  val fullName: String = "Martin Ordersky"
  val livesIn: String = "Lausanne, Switzerland"
}
```

## Sealed abstract classes

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
  override val favouriteFoods: Set[Food]
) extends Pet(name, favouriteFoods)

final case class Turtle(
  override val name: String
) extends Pet(name, Set(Lettuce))
```

## Sealed traits

```scala
sealed trait Food {
  final def isVegetarian: Boolean = this match {
    case Lettuce                => true
    case Steak | Tuna           => false
    case Other(_, isVegetarian) => isVegetarian
  }
}

case object Lettuce extends Food
case object Steak extends Food
case object Tuna extends Food
final case class Other(name: String, vegetarian: Boolean = false) extends Food
```

## Usage example

```scala
final case class Person(name: String, age: Int, pets: List[Pet])

val sophie = Person(
  name = "Sophie",
  age = 34,
  pets = List(
    Pet.Dog(
      name = "Hamlet",
      favouriteFoods = Set(
        Food.Steak,
        Food.Other("Dog treats")
      )
    )
  )
)
```

## Resources

  * [Wikipedia Article on ADTs](https://en.wikipedia.org/wiki/Algebraic_data_type)
