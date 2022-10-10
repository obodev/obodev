---
layout: post
title: UIButton + closure
date: 2022-10-10
categories: ["Swift", "iOS"]
---

# Заменяем селекторы на замыкания

Кнопки - это часто используемый компонент. Обычно мы используем селекторы для подключения действия, но можно это немного упростить. 

Обычный способ использования кнопки:

```swift

let button = UIButton()
button.setTitle(" My Button", for: .normal)
button.addTarget(self, action: #selector(didTapButton), for: .touchUpInside)

```

Для начала сделаем расширение для кнопки и добавим **Action**:

```swift

extension UIButton {
  final class Action {
    private let actionClosure: () -> ()
    
    init(action: @escaping () -> ()) {
      actionClosure = action
    }
  
    func perform() {
      actionClosure()
    }
  }
}
    
```

Теперь можно сделать всё остальное расширение:

```swift

extension UIButton {
  private enum AssociatedKeys {
    static var touchUpInsideAction = "touchUpInsideAction"
  }
  
  @objc fileprivate func performTouchUpInsideAction() {
    let objcAssociatedObject = objc_getAssociatedObject(self, &AssociatedKeys.touchUpInsideAction)
    let action = objcAssociatedObject as? Action
    action?.perform()
  }
  
  private func setTouchUpInsideAction(_ newAction: Action) {
    objc_setAssociatedObject(
      self,
      &AssociatedKeys.touchUpInsideAction,
      newAction,
      objc_AssociationPolicy.OBJC_ASSOCIATION_RETAIN_NONATOMIC
    )
  }
}

extension UIButton {
  func setOnTapAction(_ closure: @escaping () -> Void) {
    let action = Action(action: closure)
    setTouchUpInsideAction(action)
    addTarget(self, action: #selector(performTouchUpInsideAction), for: .touchUpInside)
  }
}
    
```

Использовать довольно просто:

```swift

let button = UIButton()
button.setTitle("My Button", for: .normal)
button.setOnTapAction {
  debugPrint("TapAction")
}

```