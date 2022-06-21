# For loop vs for comprehension

Last time we briefly discussed the difference between the following two lines of code:

```scala
for (i <- 1 to 10) i
```

```scala
for (i <- 1 to 10) yield i
```

At first glance, these two might seem almost identical. But if we try and assign them to a value `x` we can spot an important difference:

```scala
val x = for (i <- 1 to 10) i
// x: Unit = ()
```

```scala
val x = for (i <- 1 to 10) yield i
// x: scala.collection.immutable.IndexedSeq[Int] = Vector(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

The first code snippet above is an example of a **for loop** while the second is a **for comprehension**.

## For loop

Notice how the for loop has a return type of `Unit` and must therefore perform side effect(s) in order to do anything useful.

For instance, it could append each value of `i` to an [array](https://www.scala-lang.org/api/2.13.3/scala/Array.html):

```scala
var myArray: Array[Int] = Array.empty[Int]

for (i <- 1 to 10) {
  myArray = myArray :+ i
}

println(myArray)
// myArray: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

## For comprehension

A more common syntax for the for comprehensions is:

```scala
for {
  i <- 1 to 10
} yield i
```

Since they return a value, for comprehensions do not need to (although they can) perform side effects in order to be useful.

We could for example use a for comprehension to compute the [Cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) of two vectors:

```scala
val product = for {
  i <- 0 until 4
  j <- 0 until 8 by 2
} yield (i, j)
// product: scala.collection.immutable.IndexedSeq[(Int, Int)] = Vector((0,0), (0,2), (0,4), (0,6), (1,0), (1,2), (1,4), (1,6), (2,0), (2,2), (2,4), (2,6), (3,0), (3,2), (3,4), (3,6))
```

### Desugared form

For comprehensions are [syntactic sugar](https://en.wikipedia.org/wiki/Syntactic_sugar) for combining one or more `flatMap` calls with one `map` call.

Another (much less readable) way of computing a Cartesian product is:

```scala
val product = (0 until 4).flatMap { i =>
  (0 until 8 by 2).map { j =>
    (i, j)
  }
}
// product: scala.collection.immutable.IndexedSeq[(Int, Int)] = Vector((0,0), (0,2), (0,4), (0,6), (1,0), (1,2), (1,4), (1,6), (2,0), (2,2), (2,4), (2,6), (3,0), (3,2), (3,4), (3,6))
```

With a 3-dimensional Cartesian product, the benefits of the for comprehension over the desugared `flatMap` + `map` alternative start to become clear:

```scala
val threeDProduct = for {
  i <- 0 until 4
  j <- 0 until 8 by 2
  k <- 0 until 16 by 4
} yield (i, j, k)
// threeDProduct: scala.collection.immutable.IndexedSeq[(Int, Int, Int)] = Vector((0,0,0), (0,0,4), ..., (3,6,8), (3,6,12))
```

```scala
val threeDProduct = (0 until 4).flatMap { i =>
  (0 until 8 by 2).flatMap { j =>
    (0 until 16 by 4).map { k =>
      (i, j, k)
    }
  }
}
// threeDProduct: scala.collection.immutable.IndexedSeq[(Int, Int, Int)] = Vector((0,0,0), (0,0,4), ..., (3,6,8), (3,6,12))
```
