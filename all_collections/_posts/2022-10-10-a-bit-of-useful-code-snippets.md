---
layout: post
title: Полезные сниппеты
date: 2022-10-10
categories: ["Swift", "Полезно"]
---

# Коллекция полезных сниппетов

## Достаём что-то из массива безопасно

```swift

public extension Collection {
    public subscript(safe index: Index) -> Element? {
        return startIndex <= index && index < endIndex ? self[index] : nil
    }
}

let array = [0, 1, 2]
if let item = array[safe: 2] {
    print(item)
}

```

## Добавляем сразу несколько subView

```swift

extension UIView {
    func addSubviews(_ subviews: UIView...) {
        subviews.forEach(addSubview)
    }
}

// Add multiple subviews in a single line
view.addSubviews(imageView, slider, view, label)

```

## Cоздаём фабрику UILabel

```swift

extension UILabel {
    // MARK: - Static Factory Method for Title
    static func initForTitle() -> UILabel {
        let label = UILabel()
        label.font = .boldSystemFont(ofSize: 18)
        label.textColor = .darkGray
        label.numberOfLines = 1
        label.adjustsFontSizeToFitWidth = true
        label.minimumScaleFactor = 0.75
        return label
    }

    // MARK: - Static Factory Method for Description
    static func initForDescription() -> UILabel {
        let label = UILabel()
        label.font = .systemFont(ofSize: 14)
        label.textColor = .black
        label.numberOfLines = 0
        return label
    }
}

class ContentViewController: UIViewController {
    lazy var titleLabel = UILabel.initForTitle()
    lazy var textLabel = UILabel.initForDescription()
}

```

## Удобно создаём цвета

```swift

extension UIColor {
    // UIColor(r: 95, g: 199, b: 220)
    convenience init(r: Int, g: Int, b: Int) {
        self.init(red: CGFloat(r) / 255.0, green: CGFloat(g) / 255.0, blue: CGFloat(b) / 255.0, alpha: 1.0)
    }
    // UIColor(hex: 0x5fc7dc)
    convenience init(hex:Int) {
        self.init(r:(hex >> 16) & 0xff, g:(hex >> 8) & 0xff, b:hex & 0xff)
    }
}

```

## Удобно создаём цвета

```swift

extension UIColor {
    // UIColor(r: 95, g: 199, b: 220)
    convenience init(r: Int, g: Int, b: Int) {
        self.init(red: CGFloat(r) / 255.0, green: CGFloat(g) / 255.0, blue: CGFloat(b) / 255.0, alpha: 1.0)
    }
    // UIColor(hex: 0x5fc7dc)
    convenience init(hex:Int) {
        self.init(r:(hex >> 16) & 0xff, g:(hex >> 8) & 0xff, b:hex & 0xff)
    }
}

```

```swift

// Passing operator as closures
let array = [3, 9, 1, 4, 6, 2]
let sorted = array.sorted(by: >)
// [9, 6, 4, 3, 2, 1]

```

## Передаём операторы как замыкания

```swift

// Passing operator as closures
let array = [3, 9, 1, 4, 6, 2]
let sorted = array.sorted(by: >)
// [9, 6, 4, 3, 2, 1]

```

## Используем вариадические параметры

```swift

func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// returns 3.0, which is the arithmetic mean of these five numbers
arithmeticMean(3, 8.25, 18.75)
// returns 10.0, which is the arithmetic mean of these three numbers

```