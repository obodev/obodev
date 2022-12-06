---
layout: post
title: Протоколы ошибок (Protocol. Error)
date: 2022-12-05
categories: ["Swift", "SPL"]
---

## Протоколы ошибок

В Swift есть несколько протоколов по обработке ошибок.

Основной

- Error

Специализированные

- LocalizedError
- RecoverableError
- CustomNSError

## Протокол ошибок (Protocol Error)

Тип данных, представляющий значение ошибки, которое может быть выброшено.

Это встроенный протокол, который объявлен как:

```swift
protocol Error: Sendable
```

Любой тип, декларирующий соответствие протоколу `Error`, может использоваться для представления ошибки в системе обработки ошибок Swift. Поскольку у протокола `Error` нет собственных требований, то можно заявить о соответствии для любого пользовательского типа, который создаете.

### Использование перечислений в качестве ошибок

Перечисления Swift хорошо подходят для представления простых ошибок. 

Для этого необходимо создать перечисление, которое соответствует протоколу `Error`. В перечислении следует описать случаи для каждой возможной ошибки. Если есть дополнительные сведения об ошибке, которые могут быть полезны для решения проблемы с ошибкой, то следует использовать **связанные значения** (associated values), чтобы включить эту информацию.

В следующем примере показано перечисление `IntParsingError`, которое фиксирует два разных типа ошибок, которые могут возникнуть при синтаксическом анализе целого числа из строки:

- переполнение, когда значение, представленное строкой, слишком велико для целочисленного типа данных,
- недопустимый ввод, когда нечисловые символы находятся на входе.

```swift
enum IntParsingError: Error {
    case overflow
    case invalidInput(Character)
}
```

В этом примере случай `invalidInput` включает недопустимый символ в качестве *связанного значения*.

В следующем примере кода показано возможное расширение типа `Int`, которое анализирует целочисленное значение экземпляра `String` и выдает ошибку при возникновении проблемы во время анализа.

```swift
extension Int {
    init(validating input: String) throws {
        // ...
        let c = _nextCharacter(from: input)
        if !_isValid(c) {
            throw IntParsingError.invalidInput(c)
        }
        // ...
    }
}
```

При вызове новой инициализации `Int` в операторе `do` можно использовать сопоставление с шаблоном, чтобы сопоставить конкретные случаи пользовательского типа ошибки и получить доступ к их связанным значениям, как в примере ниже.

```swift
do {
    let price = try Int(validating: "$100")
} catch IntParsingError.invalidInput(let invalid) {
    print("Invalid character: '\(invalid)'")
} catch IntParsingError.overflow {
    print("Overflow error")
} catch {
    print("Other error")
}

// Output
// Invalid character: '$'
```

### Включение дополнительных данных в ошибки

Иногда необходимо, чтобы разные состояния ошибок включали одни и те же общие данные, такие как позиция в файле или некоторые состояния вашего приложения. После того как вы это сделаете, используйте структуру для представления ошибок.

В следующем примере используется структура для представления ошибки при синтаксическом анализе XML-документа, включая номера строк и столбцов, в которых произошла ошибка:

```swift
do {
    let price = try Int(validating: "$100")
} catch IntParsingError.invalidInput(let invalid) {
    print("Invalid character: '\(invalid)'")
} catch IntParsingError.overflow {
    print("Overflow error")
} catch {
    print("Other error")
}

// Output
// Invalid character: '$'
```

Еще раз используйте сопоставление с образцом для условного отлова ошибок. Вот как вы можете перехватывать любые `XMLParsingError` ошибки (синтаксического анализа XML), выдаваемые функцией `parse(_:)`:

```swift
do {
    let xmlDoc = try parse(myXMLData)
} catch let e as XMLParsingError {
    print("Parsing error: \(e.kind) [\(e.line):\(e.column)]")
} catch {
    print("Other error: \(error)")
}

// Output
// Parsing error: mismatchedTag [19:5]
```

В этом протоколе есть такое свойство `localizedDescription`, которое позволяет получить локализованное описание ошибки.

Это свойство вычисляемое и объявлено как

```swift
var localizedDescription: String { get }
```

## Специализированные протоколы ошибок

Есть несколько специализированных протоколов ошибок, которым перечисление с ошибками может соответствовать. И, вероятно, следует использовать их по сравнению с протоколом ошибок по умолчанию (Error), который предоставляется языком по умолчанию. 

Все эти протоколы сами соответствуют протоколу `Error`. 

Ниже будут рассмотрено несколько протоколов, которые можно использовать при работе с ошибками в Swift.

## LocalizedError

Протокол **LocalizedError** предоставляет четыре свойства для отображения пользователю информации об ошибках на его родном языке.

Ниже перечислены четыре свойства, и все они являются строками. Они необходимы, но они обеспечивают реализацию по умолчанию (Required. Default implementation provided.).

- var errorDescription: String?
- var failureReason: String?
- var helpAnchor: String?
- var recoverySuggestion: String?

Примером реализации может быть:

```swift
enum NetworkError: LocalizedError {
	case noNetwork
	case unexpectedResponse

	var errorDescription: String {
		switch self {
			case .noNetwork:
				NSLocalizedString("No network connection found", comment: "")
			case .unexpectedResponse:
				NSLocalizedString("The server returned an unexpected response", comment: "")
		}
	}

	var failureReason: String? {
		switch self {
			case .noNetwork:
				NSLocalizedString("Could not connect to the internet", comment: "")
			case .unexpectedResponse:
				NSLocalizedString("The server is not working properly", comment: "")
		}
	}

	var recoverySuggestion: String? {
		switch self {
			case .noNetwork:
				NSLocalizedString("Check your internet connection and try again", comment: "")
			case .unexpectedResponse:
				NSLocalizedString("Contact support", comment: "")
		}
	}
}
```

Есть еще одна интересная деталь об этом типе ошибок, а именно то, что при соединении с `Objective-C` (или преобразовании в `NSError` - casted as `NSError`) все свойства протокола получают ключами `userInfo` словаря `NSError`:

- `NSLocalizedDescriptionKey` для `errorDescription`
- `NSLocalizedFailureReasonErrorKey` для `failureReason`
- `NSLocalizedRecoverySuggestionErrorKey` для `recoverySuggestion`
- `NSHelpAnchorErrorKey` для `helpAnchor`

Так что, если необходимо, чтобы ваши ошибки были связаны с Objective-C, это одна из специализаций, которую следует учитывать.

## RecoverableError

Протокол **RecoverableError** предоставляет средства, которые помогут вашим пользователям попытаться восстановиться после ошибок. Эта специализация предоставляет одно свойство и два метода:

- var recoveryOptions: [String]: это массив строк, которые можно показать своему пользователю при попытке восстановления после ошибок. Это свойство является обязательным и не предоставляется реализация по умолчанию (Required);
- func attemptRecovery(optionIndex: Int) -> Bool: используйте это, чтобы попытаться восстановиться после ошибки, а затем верните логическое значение, указывающее, была ли операция успешной или нет. `optionIndex` соответствует индексу опции в массиве `recoveryOptions`. Этот метод обязательный и не предоставляется реализация по умолчанию (Required);
- func attemptRecovery(optionIndex: Int, resultHandler: (Bool) -> Void): как и в предыдущем методе, используйте этот метод, чтобы попытаться исправить ошибку. Разница в том, что используется замыкание для передачи результата восстановления, поэтому вы можете использовать его, когда необходимо попытаться восстановить используя асинхронные операции. Этот метод обязательный и предоставляется реализация по умолчанию (Required. Default implementation provided).

Быстрый пример реализации:

```swift
enum NetworkError: RecoverableError {
	case noNetwork
	case unexpectedResponse

	var recoveryOptions: [String] {
		switch self {
			case .noNetwork:
				return [
					NSLocalizedString("Retry", comment: ""),
					NSLocalizedString("Open Settings to Change Network", comment: "")
				]

			case .unexpectedResponse:
				return [
					NSLocalizedString("E-Mail support", comment: ""),
					NSLocalizedString("Change server", comment: "")
				]
		}
	}

	func attemptRecovery(optionIndex recoveryOptionIndex: Int) -> Bool {
		switch self {
			case .unexpectedResponse:
				if recoveryOptionIndex == 0 {
					// Mail support
				} else if recoveryOptionIndex == 1 {
					// Change server
				}
				return true
			default:
				return false
		}
	}

	// ...
}
```

Опять же, к свойствам можно получить доступ через `userInfo` `NSError`, используя `NSLocalizedRecoveryOptionsErrorKey` для `recoveryOptions` и ключ `NSRecoveryAttempterErrorKey` для доступа к параметрам восстановления.

## CustomNSError

Наконец, протокол `CustomNSError` предоставляет свойства для создания хорошо известного объекта `NSError` с доменом ошибки , кодом ошибки и информацией о пользователе. Все свойства являются обязательными, но предоставляется реализация по умолчанию для каждого (Required. Default implementation provided.):

- static var errorDomain: String: это домен ошибки в обратной нотации DNS.
- var errorCode: Int: код ошибки в виде `Int`.
- var errorUserInfo: [String : Any]: в виде словаря [String: Any].

Быстрый пример реализации:

```swift
enum NetworkError: CustomNSError {
	enum ErrorCode: Int {
		case noNetwork
		case unexpectedResponse
	}

	var errorDomain = "com.andyibanez.com.myApp.NetworkError"
	var appErrorCode: ErrorCode

	var errorCode: Int {
		return self.appErrorCode.rawValue
	}

	var errorUserInfo: [String : Any] {
		let dic = [
			"URL": //...,
		]
	}
}
```

Поскольку вы сами предоставляете `userInfo`, то вам не нужно беспокоиться о том, какие у него ключи. Он обладает большой гибкостью, но с ним сложнее работать.

## Смешивание несколько протоколов вместе

Помните, что это протоколы, и вам разрешено соответствовать более чем одному протоколу одновременно. Поэтому ничто не мешает вам, скажем, создать исправимую локализованную ошибку.

Пример:

```swift
enum NetworkError: LocalizedError, RecoverableError {
	case noNetwork
	case unexpectedResponse

	// MARK: - LocalizedError

	var localizedDescription: String {
		switch self {
			case .noNetwork:
				return NSLocalizedString("No network connection found", comment: "")
			case .unexpectedResponse:
				return NSLocalizedString("The server returned an unexpected response", comment: "")
		}
	}

	var failureReason: String? {
		switch self {
			case .noNetwork:
				return NSLocalizedString("Could not connect to the internet", comment: "")
			case .unexpectedResponse:
				return NSLocalizedString("The server is not working properly", comment: "")
		}
	}

	// MARK: - RecoverableError

	var recoverySuggestion: String? {
		switch self {
			case .noNetwork:
				return NSLocalizedString("Check your internet connection and try again", comment: "")
			case .unexpectedResponse:
				return NSLocalizedString("Contact support", comment: "")
		}
	}

	var recoveryOptions: [String] {
		switch self {
			case .noNetwork:
				return [
					NSLocalizedString("Retry", comment: ""),
					NSLocalizedString("Open Settings to Change Network", comment: "")
				]

			case .unexpectedResponse:
				return [
					NSLocalizedString("E-Mail support", comment: ""),
					NSLocalizedString("Change server", comment: "")
				]
		}
	}

	func attemptRecovery(optionIndex recoveryOptionIndex: Int) -> Bool {
		switch self {
			case .unexpectedResponse:
				if recoveryOptionIndex == 0 {
					// Mail support
				} else if recoveryOptionIndex == 1 {
					// Change server
				}
				return true
			default:
				return false
		}
	}
}
```

## Вывод

Обработка ошибок в Swift становится проще, когда использовать эти протоколы ошибок. Они могут сильно помочь в написании лучшего кода обработки ошибок, который работает во всей платформе *Foundation* и в остальных API.

---

## Еще полезные ссылки

- [Опционалы](https://robot.obo.dev/read/posts/optional-data-type/)
- [Управление потоком](https://robot.obo.dev/read/posts/flow-control/)
- [Функции](https://robot.obo.dev/read/posts/function/)
- [Перечисления](https://robot.obo.dev/read/posts/enum/)
- [Протоколы](https://robot.obo.dev/read/posts/protocol/)
- [Передача и обработка ошибок](https://robot.obo.dev/read/posts/error-handling/)

Также информацию по протоколам ошибок можно получить на странице официальной документации.

Ссылки на официальную документацию:

- [Apple Developer Documentation - Error](https://developer.apple.com/documentation/swift/error)
- [Apple Developer Documentation - LocalizedError](https://developer.apple.com/documentation/foundation/localizederror)
- [Apple Developer Documentation - RecoverableError](https://developer.apple.com/documentation/foundation/recoverableerror)
- [Apple Developer Documentation - CustomNSError](https://developer.apple.com/documentation/foundation/customnserror)