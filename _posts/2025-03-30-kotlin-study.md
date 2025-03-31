---
title: Kotlin Study
author: cosmo
date: 2025-03-30 18:35:00 +0900
categories: [Language, Kotlin]
tags: [Kotlin]
render_with_liquid: false
---

## Why study Kotlin?

Our team was using Java, but started to get into Kotlin. 

I'm writing this post for myself to understand and look back whenever I need.

## Kotlin - Basic syntax

### Where it starts?

```kotlin
fun main() {
  println("Hello, world!")
}
```
`main()` function starts the application. 


### Variables

- `val`: read-only variables
  - **highly recommended** for immutables!
- `var`: mutable variables

```kotlin
var test1 = 1
val constant = 2
```

### Basic Types

| Category               | Basic types                | Example Code                            |
| ---------------------- | -------------------------- | --------------------------------------- |
| Integers               | Byte, Short, Int, Long     | `val year: Int = 2020`                  |
| Unsigned integers      | UByte, UShort, UInt, ULong | `val year: UInt = 2020u`                |
| Floating-point numbers | Float, Double              | `val currentTemp: Float = 24.5f`        |
| Booleans               | Boolean                    | `val isEnabled: Boolean = true`         |
| Characters             | Char                       | `val separator: Char = ','`             |
| Strings                | String                     | `val message: String = "Hello, World!"` |

### String templates support

```kotlin
val PI = 3.141592
val message = "PI means ${PI + 1}." // "PI means 4.141592."
val message2 = "PI means $PI." // "PI means 3.141592."
val message3 = "PI means \${PI}." // "PI means \${PI}."
```

### Collections

#### Lists
```kotlin
// Read-Only List
val readOnlyAnimals = listOf("dog", "cat", "mouse")

// Mutable list
val animals: MutableList<String> = mutableListOf("dog", "cat", "tiger")

// Type-casting into read-only list
val readOnlyAnimals = animals

// access operator [], .first(), .last()
println(readOnlyAnimals[1]) // cat
println(readOnlyAnimals.first()) // dog
println(readOnlyAnimals.last()) // tiger

// operator 'in'
println("mouse" in animals) // false

// add, remove
animals.add("snake")
animals.remove("dog")
```

#### Sets
```kotlin
// Read-only set
val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")

// Mutable set with explicit type declaration
val fruit: MutableSet<String> = mutableSetOf("apple", "banana", "cherry", "cherry")

println(readOnlyFruit) // [apple, banana, cherry]
```

#### Maps
```kotlin
// Read-only map
val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
println(readOnlyJuiceMenu) // {apple=100, kiwi=190, orange=100}

// Mutable map with explicit type declaration
val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)

println(juiceMenu) // {apple=100, kiwi=190, orange=100}

// access operator []
println(juiceMenu["apple"]) // 100
juiceMenu["apple"] = 150

// .remove(), .containsKey(), .keys, .values
juiceMenu.remove("apple")
juiceMenu.containsKey("apple")
juiceMenu.keys
juiceMenu.values

// 'in' operator: checks both keys and values
"kiwi" in juiceMenu // true
100 in juiceMenu // true
```

### Control Flow

#### If
```kotlin
val d: Int
val check = true

if (check) {
    d = 1
} else {
    d = 2
}

val a = 1
val b = 2
println(if (a > b) a else b) // Returns a value: 2
```

#### When (Recommended!)
```kotlin
val obj = "Hello"    

val result = when (obj) {
    "1" -> "One"
    "Hello" -> "Greeting"
    else -> "Unknown"
}
println(result) // Greeting

val result = when (obj) {
    "1" -> {
      println("one")
      "One"
    }
    "Hello" -> {
      println("hi")
      "Greeting"
    }
    else -> "Unknown"
}
println(result) // Greeting
```

### Ranges
```kotlin
1..4 // 1,2,3,4
1..<4 // 1,2,3
4 downTo 1 // 4,3,2,1
1..5 step 2 // 1,3,5
'a'..'d' // 'a','b','c','d'
```

### Loops
```kotlin
for (number in 1..5) {
    print(number)
}

val cakes = listOf("carrot", "cheese", "chocolate")

for (cake in cakes) {
    println("Yummy, it's a $cake cake!")
}

var cakesEaten = 0
var cakesBaked = 0
while (cakesEaten < 3) {
    println("Eat a cake")
    cakesEaten++
}
do {
    println("Bake a cake")
    cakesBaked++
} while (cakesBaked < cakesEaten)
```

### Functions

Point is that you have to specify the type of parameters and return value.

```kotlin
fun sum(x: Int, y: Int): Int {
    return x + y
}

fun printMessageWithPrefix(message: String, prefix: String = "Info") {
    println("[$prefix] $message")
    // 'return Unit' or 'return' is optional
}

fun sum(x: Int, y: Int) = x + y

fun main() {
    println(sum(1, 2))
    println(sum(y = 1, x = 2)) // with parameter name, changing order is enabled
}
```

#### Lambda expressions

```kotlin
val upperCaseString = { text: String -> text.uppercase() }

val isNegative = { x -> x < 0 }
val negatives = listOf(1,2,-3,-4).filter(isNegative)

val doubled = listOf(1,2,-3,-4).map { x -> x * 2 }
```

```kotlin
(Type1, Type2) -> Type3 // function types are declined by this form
{ var1: Type1, var2: Type2 -> Type3 } // lambda function's definition
```

```kotlin
fun toSeconds(time: String): (Int) -> Int = when (time) {
    "hour" -> { value -> value * 60 * 60 }
    "minute" -> { value -> value * 60 }
    "second" -> { value -> value }
    else -> { value -> value }
}
```

### Classes

```kotlin
class Customer
```
