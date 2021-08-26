---
title: "Difference between functions and methods in Golang"
date: 2021-03-18
draft: true
tags: 
  - golang
categories:
  - programming
cover: 
  image: "/difference-between-functions-methods-golang.jpeg"
---
*This post was originally published on my Medium profile : [Difference between functions and methods in Golang](https://penthaa.medium.com/difference-between-functions-and-methods-in-golang-54db90fb92c9)*

The words function and method are used almost interchangeably, but there are subtle differences in their implementation and usage when used in Golang. Let's see what the difference is and how its used.

### Function

Functions accept a set of input **parameters**, perform some operations on the input and produce an output with a specific **return type.** Functions are independent that is they are **not attached to any user defined type**. 

***Syntax:***
```go
func FunctionName(Parameters...) ReturnTypes...
```
There cannot exist two different functions with the same name in the same package.
```go
type Rectangle struct {
    Width float64
    Height float64
}

type Circle struct {
    Radius float64
}

func Area(r Rectangle) float64 {
    return 2 * r.Height * r.Width
}

func Area(c Circle) float64 {
    return math.Pi * c.Radius * c.Radius
}
```
[Run above code in The Go Playground](https://play.golang.org/p/LQNBLtOfhFi)

The above code throws an error :  `Area redeclared in this block`

### Method

 A method is effectively a function attached to a user defined type like a **struct**. This user defined type is called a **receiver**. 

***Syntax:***
```go
func (t ReceiverType) FunctionName(Parameters...) ReturnTypes...
```

There can exist different methods with the same name with a different receiver. 

 ```go
type Rectangle struct {
	Width  float64
	Height float64
}

func (r Rectangle) Area() float64 {
	return r.Width * r.Height
}

type Circle struct {
	Radius float64
}

func (c Circle) Area() float64 {
	return math.Pi * c.Radius * c.Radius
}

type Triangle struct {
	Base   float64
	Height float64
}

func (t Triangle) Area() float64 {
	return 0.5 * t.Base * t.Height
}
```

[Run above code in The Go Playground](https://play.golang.org/p/ASQP_J_RUSU)
