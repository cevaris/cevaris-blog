What is referential transparency? FP dude.

<!--more-->

Currently looking into [Functional Programming in Scala](http://www.manning.com/bjarnason/), also have [Haskell](http://www.haskell.org/haskellwiki/Haskell) in the sights. There are many definitions in this book about functional programming (FP) theory. Coming from a Object Oriented background, writing Java (lots of Java), serving with Ruby, and processing with Python. All the while, staying clear away from programming languages (PL) courses while studying Computer Science. It has always been focusing on high level application coding, using frameworks like Django, Rails, or Swing; never really having get into the nitty gritty of languages. How wants to learn something new, while it is so easy to put up a website or a data processing server. But there are many ways to do the same things.

Here we chat about [referential transparency](http://en.wikipedia.org/wiki/Referential_transparency_(computer_science\)). We will use [substation model](http://en.wikipedia.org/wiki/Substitution_(logic\)) to help define a function as a **pure function** or not. The following an attempt to define Referential Transparency for those developers with a non-funtional programming background.

## Referential Transparency

### Example `reverse`

The global replacement of a function with its value, while preserving meaning of program. 

Pretend we defined a variable `foo` with an Array `[1,2,3,4,5]`.

	scala> val foo = Array(1,2,3,4,5)
	foo: Array[Int] = Array(1, 2, 3, 4, 5)

Now we want to do some transformation on the result of `foo`.

	scala> val r1 = foo.reverse
	r1: Array[Int] = Array(5, 4, 3, 2, 1)

	scala> val r2 = foo.reverse
	r2: Array[Int] = Array(5, 4, 3, 2, 1)
	
Notice, invoking `reverse` method is consistent and returns the same result for the given array `[1,2,3,4,5]`. Further we want to use the **Substitution Method** to test for Referential Transparency. We simple have to replace the value of `foo` with the actual value `[1,2,3,4,5]`. If we get the same results, then the method `reverse` is Referential Transparent.

	scala> val r1 = Array(1, 2, 3, 4, 5).reverse
	r1: Array[Int] = Array(5, 4, 3, 2, 1)

	scala> val r2 = Array(1, 2, 3, 4, 5).reverse
	r2: Array[Int] = Array(5, 4, 3, 2, 1)

Notice that in both examples, the result of r1 and r2 are the same even though we replace the reference variable `foo` with the actual data `[1,2,3,4,5]`.


### Example `append` 
	
	
	scala> val bar = new StringBuilder
	bar: StringBuilder = 

	scala> val r1 = bar.append("Hello ")
	r1: StringBuilder = Hello 

	scala> val r2 = bar.append("Hello ")
	r2: StringBuilder = Hello Hello 

	scala> val r3 = bar.append("Hello ")
	r3: StringBuilder = Hello Hello Hello 

	
Notice, calling `append` multiple times `r1,r2, and r3` returns different values. This is because the `append` method transforms the internal state of the value that `bar` is referencing.
	



 