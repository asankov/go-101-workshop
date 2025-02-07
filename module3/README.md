# Module 3 - Structs, methods and pointers

## Structs

As programmers we always end up needing structured data. Structured in this context will mean structured as in an object with predefined fields and typed values, your stereotypical dog with legs and a greeting or a person with names, age and gender. In Go we store structured data in `structs`. That's a defined type with a name and a number of fields with values of a given type. The stereotypical dog would look like so:
```go
type dog struct {
	legs int
	greeting string
	cuteness uint64
}
```

How do we control access to structs or their fields? By default a struct is accessible within the current package. If we want to export it, we just capitalize its first letter. Same for its fields. If we want anything to be able to accessible from outside the current package, capitalization is the way. Here's a very public Dog:

```go
type Dog struct {
	Legs int
	Greeting string
	Cuteness uint64
}
```

We can define types of any type, not just structs. Here's an example with a personal project of mine, where I was implementing a chess board and used byte for the underlying type of a Figure and one example that you're going to see in a lot of projects, try and figure out why is it useful:

```go
type Figure byte
type ID string
```

## Methods
Now, what can we do with structs? Well, just about anything we can do with any other type, except for using them with logical or mathematical operators (`<`, `==`, `+` and so on). Another quite important thing we can do with self-defined types is to use them as the receivers for methods. What does that mean? In short, the following.

```go
func (d *dog) Greet() string {
	return d.Greeting
}
```

We can attach a function to be executed on an instance of a type. What’s that again? Well, basically, the following example, but with sugar coating.

```go
func Greet(d *dog) string {
	return d.Greeting
}
```

This way, instead of having to call it with `packagename.Greet(d)`, we can use the syntax we're used to from other languages and call it with a method syntax - `d.Greet()`. Here are some more full-blooded examples:

```go
package main

import (
	"animals"
	
	"fmt"
)

func() {
	d := animals.Dog{
		Legs:4,
		Greeting: "Woof!",
		Cuteness: ^uint64(0),
	}

	// No sugar coating
	fmt.Println(animals.Greet(&d))
	
	// Sugar coating
	fmt.Println(d.Greet())
}
```
## Pointers and references

People with C/C++ background will feel comfortable with the next topic so you can skim over it. Said topic is pointers, references and passing parameters by value or reference. In C# or Java, when you define a method that accepts an object (an instance of a class) as a parameter, you're implicitly passing a pointer to that object - a numeric value, an address that tells the method where it can find the memory where the object is being stored. If you then change that object within the scope of the method and try to access it from the caller's scope, you'll see that the changes made in the callee have affected the caller's scope. As opposed to this default behaviour in go, if you just pass a struct to a function, you'll be passing the structure's value - in effect a copy of the object's contents to the function. To receive behaviour that's corresponding to the one you expect in classic OOP-driven languages you'd need to explicitly pass a pointer to the struct by using an ampersand before the argument type declaration. Syntax-wise pointers in go are relatively simple. When you want to get a pointer to an object, you prefix it with an ampersand, like so `d := &dog{}`, when you want to indicate that you want something to be of a reference type, you prefix it with an asterisk like so `func greet(d *dog)`.
Here's a basic example and an [interactive link to it](https://play.golang.org/p/ZFQg7eoU9e5):

```go
package main

import (
	"fmt"
)

type dog struct {
	name string
}

func rename(d dog) {
	d.name = "Pesho"
	fmt.Printf("Renamed inside the value func: %#v\n", d)
}

func renameRef(d *dog) {
	d.name = "Vihren"
}

func main() {
	d := dog{name: "Stamat"}
	fmt.Printf("Original dog: %#v\n", d)

	rename(d)
	fmt.Printf("Attempted to rename passing in a value:   %#v\n", d)

	renameRef(&d)
	fmt.Printf("Attempted to rename passing in a pointer: %#v\n", d)
}
```

So why do we care about all this, doesn't it just complicate our code? This can be a workshop in and of itself, but here are three important things to note for the cases where you do want to pass a reference - one we might actually care about the exact memory there - a pool of connections to the DB, an open TCP socket and so on. Two - if the structs are large, copying them often can make your stack too thick and copying an entire struct is slower than just copying the pointer number. And three, we might want the function to alter the state of the object that was passed in (this one is a rare case we try to aviod). But then, why don't we always pass objects by reference, the way C#, Java and Python do? Again, several use cases, but most often, you don't want the objects you pass to a function to be modifiable in there, because that leads to code that's *extremely* difficult to debug and maintain. There's also a bit more advanced issues here, where accessing memory through a pointer is slower than having a copy passed directly, but stack and heap are a topic beyond this workshop.

## Tasks
### Persistent calculator
Your task is to implement a calculator that can execute the following operations - `Plus(int a)`, `Minus(int a)`, `Multiply(int a)`, `Divide(int a)` and `Equals() int`. The calculator should remember the running total and when `Equals()` is called, should output the result of all calculations and reset its state.

