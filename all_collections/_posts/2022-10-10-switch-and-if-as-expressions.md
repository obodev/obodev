---
layout: post
title: Используем `switch` и `if` как выражения
date: 2022-10-10
categories: ["Swift", "SPL"]
---

# Удобный способ использовать `switch` и `if` как выражения

Операторы управления потоком `switch` и `if` используются, внезапно, как операторы. Что делать если очень хочется использовать их как выражение?

Можно сделать generic функцию:

```swift

func resultOf<T>(_ code: () -> T) -> T {
  return code()
}

```

Теперь можно обернуть операторы в эту функцию и делать вот такие вещи:

```swift

let randomInt = Int.random(in: 0...3)

let spelledOut: String = resultOf {
  switch randomInt {
    case 0:
      return "Zero"
    case 1:
      return "One"
    case 2:
      return "Two"
    case 3:
      return "Three"
    default:
      return "Out of range"
  }
}

print(spelledOut)

```