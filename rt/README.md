# Referential Transparency

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
