---
layout: post
title: Простая проверка паттернов
date: 2022-10-10
categories: ["Swift", "Swift"]
---

# Простая проверка паттернов

Представим такую ситуацию:

```swift

let expr1: Bool
let expr2: Bool
let expr3: Bool

if expr1 && !expr3 {
  functionA()
} else if !expr2 && expr3 {
  functionB()
} else if expr1 && !expr2 && expr3 {
  functionC()
}

```

Получаем много кода, в котором легко запутаться.

Попробуем попользовать pattern matching, чтобы сделать всё понятнее:

```swift

let expr1: Bool
let expr2: Bool
let expr3: Bool

switch (expr1, expr2, expr3) {
  case (true, _, false):
    functionA()
  case (_, false, true):
    functionB()
  case (true, false, true):
    functionC()
  default:
    break
}
  
```

Читать стало намного приятнее.