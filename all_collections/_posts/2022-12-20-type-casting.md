---
layout: post
title: Приведение типов (Type Casting)
date: 2022-12-20
categories: ["Swift", "SPL"]
---

# Приведение типов (Type Casting)

Приведение типов используется для взаимодействия с экземпляром определенного класса, как с частью другого подкласса или суперкласса в той же иерархии классов.

### Что такое тип?

Swift использует типы, такие как классы и структуры, для представления различных типов данных в коде вашего приложения.

- `Int` используется для целочисленных значений;
- `Double` используется для десятичных значений, например 3.1415;
- `String` используется для текста, например «Привет»;
- `UIButton` используется для элемента пользовательского интерфейса в виде кнопки.

Язык программирования Swift является строго типизированным. Это означает, что каждая переменная нуждается в типе, и как только вы определите тип для переменной, ее нельзя будет изменить.

Это довольно жесткое ограничение, поэтому Swift имеет такие возможности как **приведение типов**, **протоколы**, **Any и AnyObject**, **непрозрачные типы**, **дженерики**, которые позволяют писать гибкий и многократно используемый код.

### Что из себя представляет приведение типов?

При приведении типа вы относитесь к объекту одного типа, как к объекту другого типа.

Для примера работы приведения типов опишем три разных класса. Первый класс `House` имеет одно свойство `windows` типа `Int`, а также инициализатор:

```swift
class House {
    var windows: Int = 0
 
    init(windows: Int) {
        self.windows = windows
    }
}
```

Следующий класс `Villa` является подклассом `House`, тем самым наследуя его свойство `windows`. Он также добавляет новое свойство `hasGarage`:

```swift
class Villa: House {
    var hasGarage: Bool = false
 
    init(windows: Int, hasGarage: Bool) {
        self.hasGarage = hasGarage
        super.init(windows: windows)
    }
}
```

Третий класс `Castle` также наследуется от `House`. Он добавляет свойство `towers`:

```swift
class Castle: House {
    var towers: Int = 0
 
    init(windows: Int, towers: Int) {
        self.towers = towers
        super.init(windows: windows)
    }
}
```

Оба класса `Villa` и `Castle` являются подклассами `House`. В результате у обоих из них есть свойство `windows` в дополнение к своим собственным свойствам `hasGarage` и `towers`.

С помощью приведения типов вы можете рассматривать экземпляр одного класса как экземпляр другого суперкласса или подкласса в иерархии классов.

- Мы можем выполнить восходящее преобразование и привести экземпляр типа `Villa` или `Castle` в `House`.
- Мы можем выполнить нисходящее преобразование и привести экземпляр типа `House` до `Villa` или `Castle`.

Посмотрим на пример:

```swift
let house: House = Castle(windows: 200, towers: 4)
```

В приведенном выше коде мы объявляем константу с именем `house` типа `House`. Далее мы инициализируем ее экземпляром `Castle` и определяем `200` окон и `4` башни.

Это называется **восходящим преобразованием** (Upcasting). Поскольку `House` и `Castle` находятся в одной иерархии классов, можно назначить экземпляр `Castle` переменной типа `House`.

Это все равно, что сказать: *замок — это дом*.

Приведение выполняется неявно, однако, также можно написать явно:

```swift
let house: House = Castle(windows: 200, towers: 4) as House
```

Можно сказать: "проинициализируй константу `house` с типом `House` как экземпляр дочернего класса `Castle`, но рассматривай её как тип `House`".

Можно не указывать тип данных у переменной `house` так как он указан с помощью оператора `as`. 

```swift
let house = Castle(windows: 200, towers: 4) as House
```

Теперь можно использовать функцию `type(of:)`, чтобы получить значение типа:

```swift
print(type(of: house))

// Output
// Castle
```

В выводе в консоль можно увидеть, что тип константы `house` выводится как `Castle`.

Как может `house` быть типа `Castle`, когда четко объявили его с типом `House`?

Вы можете использовать функцию `type(of:)` для поиска динамического типа значения, особенно когда динамический тип отличается от статического типа. Статический тип значения — это известный тип значения во время компиляции. Динамический тип значения — это фактический тип значения во время выполнения, который может быть подтипом его конкретного типа.

Больше про типизацию можно прочитать в отдельной статье. См. ссылку внизу.

Пока рассмотрим более простое объяснение.

```swift
print(house.towers)
// error: value of type 'House' has no member 'towers'
```

Объект `house` имеет данные, представленные классом `Castle`. У него есть окна (`windows`) и башни (`towers`). Но описываем его с помощью класса `House`, и в результате - нельзя получить доступ к свойству `towers`.

Объект `house` имеет тип `House`, но **ссылается на экземпляр класса `Castle`** и, соответственно, хранит его свойства и методы.

Приведение типов дает нам возможность рассматривать экземпляр одного класса как экземпляр другого класса в пределах одной иерархии классов.

Вернемся к неявному приведению:

```swift
let house: House = Castle(windows: 200, towers: 4)
```

Несмотря на то, что константа `house` ссылается на экземпляр класса `Castle`, она описывается как тип `House` и имеет этот тип данных, поэтому **нельзя добраться до свойства `house.towers`**.

Если не указать тип данных с помощью явного указания при объявлении, либо с помощью оператора `as`, то тогда переменная `house` была бы с типом `Castle` и доступными всеми свойствами и методами класса `Castle`, как при обычной инициализации.

Используем нисходящее преобразования для приведения `house` к типу `Castle`:

```swift
let castle: Castle = house as! Castle
```

Здесь используется ключевое слово - оператор `as!` - для нисходящего преобразования.

Можно сказать: "проинициализируй константу `castle ` с типом `Castle ` и присвой значение константы `house`, которую рассмотри, как экземпляр дочернего класса `Castle`.

Так как константу `house` рассматриваем как `Castle`, то свойство `towers` у неё доступно. А значит можно константе `castle` передать все свойства и методы из класса `Castle`.

Теперь константа `castle` ссылается на тот же экземпляр класса `Castle`, что и константа `house`.

Теперь можно получить свойство `towers`:

```swift
print(castle.towers)

// Output
// 4
```

Экземпляры `house` и `castle` относятся к одному и тому же объекту:

```swift
print(house === castle)
// true

```

## Как использовать is, as, as! и as?

Можно использовать 4 различных типа операторов:

- `is` — для проверки типа.
- `as` - для восходящего преобразования.
- `as!` - для принудительного нисходящего преобразования.
- `as?` - для опционального нисходящего преобразования.

### Проверка типа с помощью is

Оператор `is` используется, чтобы проверить тип экземпляра. Выражение возвращает значение типа `Bool`, поэтому его можно использовать в условном выражении:

```swift
let tintagel: Castle = Castle(windows: 300, towers: 1)
 
if tintagel is Castle {
    print("Это замок!")
} else if tintagel is Villa {
    print("Это вилла!")
}

// Output
// Это замок!
```

Приведенное выше выражение возвращает `true`, поэтому выполняется первое условие.

Когда используется `is` для проверки типа суперкласса, он также возвращает `true`:

```swift
let house: House = Castle(windows: 123, towers: 3)
 
print(house is House)

// Output
// true

print(house is Castle)

// Output
// true

print(house is Villa)

// Output
// false
```

### Восходящее преобразование с помощью as (Upcasting)

Восходящее преобразование происходит неявно, поэтому можно не использовать ключевое `as`. Как объяснялось в предыдущей части статьи, восходящее преобразование возможно для подкласса к суперклассу. Например, `Villa` является `House`.

### Принудительное преобразование с помощью as! (Downcasting)

Принудительное преобразование дает вам возможность преобразовать суперкласс в подкласс.

Выражение, которое использует `as!`, вернет принудительно извлеченное значение. Когда понижающее преобразование не удается, код можете вылететь с фатальной ошибкой. Поэтому необходимо использовать принудительное преобразование с помощью `as!`, если только полностью уверены, что оно будет успешным.

Посмотрим на пример:

```swift
let house: House = Castle(windows: 200, towers: 4)
print(house.towers)

// Output - Это не работает. House не имеет свойств towers.
 
let castle: Castle = house as! Castle
print(castle.towers)

// Output - Теперь house имеет свойства towers.
// 4
```

### Опциональное преобразование с помощью as? (Downcasting)

Часто бывает удобнее использовать опциональное преобразование с ключевым словом `as?`. Тогда в случае сбоя возвращается `nil`.

```swift
let house: House = Castle(windows: 200, towers: 4)
print(house.windows)

// Output
// 200
 
let villa: Villa? = house as? Villa
print(villa?.hasGarage)

// Output
// nil
```

Здесь пытаемся преобразовать `house` к `Villa` и присвоить результат константе `villa`. Данное преобразование терпит неудачу, потому что значение `house` имеет тип `Castle`, а `Castle` и `Villa` не могут быть преобразованы друг к другу.

Давайте посмотрим на еще один пример:

```swift
var houses = [
    Castle(windows: 100, towers: 3),
    Villa(windows: 20, hasGarage: false),
    Castle(windows: 999, towers: 12),
    House(windows: 3),
    Castle(windows: 93, towers: 8),
    Villa(windows: 42, hasGarage: true)
]
```

В приведенном выше примере был создан массив с экземплярами `Castle`, `House` и `Villa`. Благодаря полиморфизму, тип массива `houses` будет выведен в качестве `[House]` (приведение к общему типу данных для всех указанных экземпляров). Однако в этом массиве есть экземпляры 3 разных классов.

Можно написать цикл `for-in`:

```swift
for house in houses {
    if let castle = house as? Castle {
        print("Замок имеет \(castle.windows) окна и \(castle.towers) башни")
    }
    else if let villa = house as? Villa {
        print("Вилла имеет \(villa.windows) окна и \(villa.hasGarage ? "имеет" : "не имеет") гараж")
    }
    else {
        print("Дом с \(house.windows) окнами")
    }
}
```

В цикле используется опциональное приведение и опциональное связывание для проверки типа `house`, а затем вызываем одно из трех условных выражений.

---

## Еще один пример приведения типа

Создадим классы, которые опишут сотрудников компании:

```swift
class Person {
    var name: String
    var age: Int
             
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    func display() {
        print("Имя: \(name)  Возраст: \(age)")
    }
}
         
class Employee : Person {
    var company: String
    init(name: String, age: Int, company: String) {
        self.company = company
        super.init(name:name, age: age)
    }

    override func display() {
        print("Имя: \(name)  Возраст: \(age)  Сотрудник компании: \(company)")
    }

    func work() {
        print("\(self.name) работает")
    }
}
```

Поскольку класс `Employee` наследуется от класса `Person`, то везде, где требуется объект `Person`, мы можем использовать объект `Employee`:

```swift
func getInfo(p: Person) {
    p.display()
}
         
let tom: Employee = Employee(name:"Tom", age: 23, company: "Google")
let bob: Person = Employee(name: "Bob", age: 28, company: "Apple")
 
getInfo(p: tom)

// Output
// Имя: Tom  Возраст: 23  Сотрудник компании: Google

getInfo(p: bob)

// Output
// Имя: Bob  Возраст: 28  Сотрудник компании: Apple
```

И в данном случае никаких ошибок не возникнет. И компилятор автоматически преобразует объекты `Employee` к типу `Person`.

Для объекта `bob` было выполнено неявное восходящее преобразование: несмотря на то, что `bob` ссылается на экземпляр класса `Employee`, `bob` рассматривается, как экземпляр класса `Person`.

Но теперь рассмотрим другую ситуацию:

```swift
let bob: Person = Employee(name: "Bob", age: 28, company: "Apple")
print(bob.company) // Error: у типа Person нет свойства company
bob.work()         // Error: у типа Person нет метода work
```

В данном случае константа `bob` представляет тип `Person`, но хранит ссылку на объект `Employee`, у которого есть свойство `company` и метод `work()`. Однако у типа `Person` их нет. И в данном случае константа `bob` воспринимается именно объект `Person`, объект `Person` не обязательно должен представлять объект `Employee`.

Или другой пример:

```swift
func getInfo(p: Employee) {
    p.display()
}
         
let bob: Person = Employee(name: "Bob", age: 28, company: "Apple")
getInfo(p: bob) // Error: bob представляет объект Person, а не Employee
```

Функция `getInfo` принимает объект `Employee`, однако константа `bob` представляет именно объект `Person`, несмотря на то, что хранит ссылку на объект `Employee`, поэтому автоматически мы ее передать в этот метод не можем.

### Нисходящее преобразование

Что же делать, если переменная/константы представляет объект базового типа, однако необходимо его использовать как объект производного типа? В этом случае необходимо применить нисходящее преобразование типов (downcasting). Для этого применяется оператор `as!`:

```swift
func getInfo(p: Employee){
    p.display()
}
         
let bob: Person = Employee(name: "Bob", age: 28, company: "Apple")
         
print((bob as! Employee).company)

// Output
// Apple
         
(bob as! Employee).work()

// Output
// Bob работает
         
getInfo(p: (bob as! Employee))

// Output
// Имя: Bob  Возраст: 28  Сотрудник компании: Apple
 
let bobEmpl = bob as! Employee
bobEmpl.work()
```

Здесь принудительно указываем рассмотреть `bob` именно как экземпляр класса `Employee` - `bob as! Employee`. В этом случае, у экземпляра класса `Employee` доступны, входящие в этот класс, свойства и методы: `company`, `work()`. Можно передать конструкцию `bob as! Employee` в функцию `getInfo(p: Employee)`. И раз `bob` рассматривается, как именно экземпляр класса `Employee`, то можно эту ссылку передать в другую константу. В примере выше - в константу `bobEmpl`. Константа `bobEmpl` имеет тип `Employee` и ссылается на объект класса `Employee`. Поэтому, для доступа к свойствам и методам класса `Employee` (`company`, `work()`), в константе `bobEmpl` приводить тип уже не надо.

### Безопасное преобразование

Хотя оператор `as!` позволяет преобразовать объект одного типа в другой, тем не менее можно столкнуться с ошибкой:

```swift
let tom: Person = Person(name:"Tom", age: 23)
let tomEmpl: Employee = tom as! Employee
```

Здесь константа `tom` хранит ссылку на объект `Person`, а не `Employee`. Поэтому при попытке преобразования к типу `Employee` получим ошибку. Чтобы избежать подобных ошибок, следует проверять тип перед преобразованием типов с помощью оператора `is`:

```swift
let tom: Person = Person(name:"Tom", age: 23)
if tom is Employee {
    let tomEmpl: Employee = tom as! Employee
    tomEmpl.work()
}        
         
let bob: Person = Employee(name: "Bob", age: 28, company: "Apple")
if bob is Employee {
    let bobEmpl = bob as! Employee
    bobEmpl.work()
}

// Output
// Bob работает
```

- `tom` не является экземпляром `Employee`, поэтому `tom is Employee` вернёт `false`.
- `bob` является по типу `Person`, но имеет ссылку на экземпляр класса `Employee`, и поэтому можно привести к классу `Employee` (рассмотреть как объект класса `Employee`). Значит `bob is Employee` вернёт `true` и будут выполнятся инструкции в блоке `if`.

В качестве альтернативы можно преобразовывать объект в тип `Optional` с помощью оператора `as?`, а затем проверять на `nil`:

```swift
let tom: Person = Person(name:"Tom", age: 23)
 
let tomEmpl: Employee? = tom as? Employee
if tomEmpl != nil {
    tomEmpl!.work()
}        
         
let bob: Person = Employee(name: "Bob", age: 28, company: "Apple")

let bobEmpl = bob as? Employee
if bobEmpl != nil {
    bobEmpl!.work()
}

// Output
// Bob работает
```

Также можно сократить код следующим образом, с помощью оператора `?.` - **Опциональная цепочка**:

```swift
let tom: Person = Person(name:"Tom", age: 23)
(tom as? Employee)?.work()
         
let bob: Person = Employee(name: "Bob", age: 28, company: "Apple")
(bob as? Employee)?.work()

// Output
// Bob работает
```

Конструкцию `(tom as? Employee)?.work()` можно прочитать так:

`tom as? Employee` - попробуй рассмотреть `tom` как `Employee`;
`(tom as? Employee)?` - преобразуй в опциональную цепочку;
`(tom as? Employee)?.work()` - если будет значение в опциональной цепочке, то выполни `work()`, если нет (`nil`) - не  выполняй `work()`.

Опциональная цепочка будет возвращать опциональные значения даже для неопциональных свойств:

```swift
print((bob as? Employee)?.company)

// Output
// Optional("Apple")
```

Несмотря на то, что свойство `company` не является опциональным, но опциональная цепочка возвращает его значение опциональным - `Optional("Apple")`.

Больше про опциональные типы данных можно прочитать в отдельной статье. См. ссылку внизу.

## А что будет, если классы не связаны наследованием?  

Создадим два несвязанных между собой класса людей и животных:

```swift
class Human {
    var name: String
    var age: Int

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    func display() {
        print("Имя: \(name)  Возраст: \(age)")
    }
	func greetings() {
		print("Hello")
	}
}
         
class Animal {
    var name: String
    var animalBreed: String
    var numberOfLeg: Int
    
    init(name: String, animalBreed: String, numberOfLeg: Int) {
        self.name = name
        self.animalBreed = animalBreed
        self.numberOfLeg = numberOfLeg
    }

    func display() {
        print("Имя: \(name)  Вид животного: \(animalBreed)  Количество ног: \(numberOfLeg)")
    }
}

let humanOne = Human(name: "Tom", age: 30)
let humanTwo = Human(name: "Bob", age: 28)
let animalOne = Animal(name: "Jack", animalBreed: "Dog", numberOfLeg: 4)
let animalTwo = Animal(name: "Popka", animalBreed: "Bird", numberOfLeg: 2)

humanOne.display()

// Output
// Имя: Tom  Возраст: 30
         
animalOne.display()

// Output
// Имя: Jack  Вид животного: Dog  Количество ног: 4
```

Было создано по два экземпляра каждого класса.

Теперь объединим экземпляры одного класса в массив:

```swift
var arrayHuman = [humanOne, humanTwo]
var arrayAnimal = [animalOne, animalTwo]

print(type(of: arrayHuman))

// Output
// Array<Human>

print(type(of: arrayAnimal))

// Output
// Array<Animal>
```

Как видно из примера, массив с людьми `arrayHuman` имеет тип данных `Array<Human>` , а животных `arrayAnimal` - `Array<Animal>`.

Теперь объединим все экземпляры в один массив:

```swift
var arrayUnite = [humanOne, humanTwo, animalOne, animalTwo] as [Any]

print(type(of: arrayUnite))

// Output
// Array<Any>
```

Общий массив `arrayUnite` имеет тип данных `Array<Any>`. Так же, при инициализации массива `arrayUnite` было выполнено восходящие преобразование к массиву с данными по типу данных `Any`.

Можно было записать и так:

```swift
var arrayUnite: [Any] = [humanOne, humanTwo, animalOne, animalTwo]

print(type(of: arrayUnite))

// Output
// Array<Any>
```

Если перебрать массив `arrayUnite` циклом и вывести тип каждого элемента, то выведутся соответствующие типы, на которые элементы ссылаются, хотя сами являются элементами с типом `Any`:

```swift
for item in arrayUnite {
    print(type(of: item))
}

// Output
// Human
// Human
// Animal
// Animal
```

То, что элементы имеют тип `Any`, можно увидеть, если попытаться получить доступ к свойствам и методам этих элементов.

```swift
for item in arrayUnite {
//    item.display() // Error: Value of type 'Any' has no member 'display'
}
```

Не смотря на то, что у всех экземпляров вышеназванных классов `Human` и `Animal` присутствует метод `display()`, но элементы массива `arrayUnite` имеют тип `Any`, который не содержит в себе метода `display()`.

Чтоб получить доступ к методу `display()` следует выполнить приведение типа для элементов массива `arrayUnite`:
 
```swift
for item in arrayUnite {
    (item as? Human)?.display()
    (item as? Animal)?.display()
}

// Output
// Имя: Tom  Возраст: 30
// Имя: Bob  Возраст: 28
// Имя: Jack  Вид животного: Dog  Количество ног: 4
// Имя: Popka  Вид животного: Bird  Количество ног: 2
```

Если необходимо, чтоб выполнился только метод экземпляров только одного класса, то тоже необходимо выполнить приведение типа:

```swift
for item in arrayUnite {
    (item as? Human)?.greetings()
}

// Output
// Hello
// Hello
```

В этом примере был перебран массив `arrayUnite` с помощью цикла. И если элемент массива можно рассмотреть как экземпляр класса `Human` (привести к типу `Human`), то выполняется метод `greetings()` из этого класса.

---

## Приведение типов в практической разработке

Вот для чего мы можем использовать приведение типов:

- Работа с различными типами контроллеров представления в контроллере панели вкладок (tab bar controller), например, для выполнения действий с контроллером представления определенного типа.
- Преобразование типов между объектными моделями, например, когда вы получаете объект `PFObject` из `PFUser`.
- Приведение `Any` к определенному типу, например, при получении объектов через JSON.
- Преобразования между типами Swift и Objective-C, к примеру, чтобы использовать `String` в качестве `NSString`.
- Преобразование подклассов контроллеров представлений, например, в контроллер табличного представления с настраиваемым подклассом ячеек табличного представления.

Если необходимо использовать настраиваемую ячейку табличного представления в контроллере табличного представления, можно использовать подкласс `UITableViewCell`. В результате - наследуете свойства ячейки табличного представления и получаете возможность настраивать их отдельно.

```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: "customCell", for: indexPath) as? MyCustomTableViewCell
 
 
    return cell
}
```

Приведение выполняется успешно до тех пор, пока `dequeueReusableCell(…)` возвращает экземпляр пользовательского подкласса. Также нужно будет принять во внимание, что понижающее преобразование может завершиться ошибкой, поэтому необходимо использовать `as?` для корректной обработки ошибки, если она произойдет.

---

## Еще полезные ссылки

- [Переменные и константы](https://robot.obo.dev/read/posts/variable/)
- [Операторы](https://robot.obo.dev/read/posts/operator/)
- [Типы данных](https://robot.obo.dev/read/posts/data-type/)
- [Опционалы](https://robot.obo.dev/read/posts/optional-data-type/)
- [Кортежи](https://robot.obo.dev/read/posts/tuple/)
- [Типы Any и AnyObject](https://robot.obo.dev/read/posts/data-type-any-anyobject/)
- [Управление потоком](https://robot.obo.dev/read/posts/flow-control/)
- [Структуры](https://robot.obo.dev/read/posts/structure/)
- [Классы](https://robot.obo.dev/read/posts/class/)
- [Протоколы](https://robot.obo.dev/read/posts/protocol/)
- [Передача и обработка ошибок](https://robot.obo.dev/read/posts/error-handling/)

Также информацию по приведению типов можно получить на странице официальной документации.

Ссылки на официальную документацию:

- [Swift.org - Type Casting](https://docs.swift.org/swift-book/LanguageGuide/TypeCasting.html)