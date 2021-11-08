---
layout: post
title: "[Swift] 프로토콜 지향 프로그래밍(Protocol-Oriented Programming)"
date: 2020-04-10
image: "/images/04.png"
author: leefilll
tags: [Swift, iOS, POP]
---

애플은 2015년 `WWDC(애플에서 매년 개최하는 개발자 컨퍼런스)`에서 Swift 언어가 `프로토콜 지향 언어(Protocol-Oriented Language)`라고 발표하였다. 흔히 말하는 `객체 지향 언어(Object-Oriented Language)`의 부족한 점을 보완한 것이 `프로토콜 지향 언어`이다. 오늘의 포스팅은 프로토콜을 활용한 Swift 프로토콜 중심 프로그래밍을 살펴보려고 한다.

# Swift의 프로토콜 🤙

기존의 `객체 지향 프로그래밍`에서는 상속을 통해 추상화를 실현하고, 공통된 로직을 쉽게 공유할 수 있도록 한다. 하지만, `JAVA`와 같이 Swift는 단일 상속만 가능하기 때문에 기능이 복잡해지고 많아지게 되면 관리가 어려워지게 된다. 그래서 실제로 Swift의 표준 라이브러리에 있는 대부분의 타입은 클래스가 아닌 구조체로 이루어져있다. 그런데 구조체는 상속이 불가능한데, 어떻게 그 많은 로직을 구조체로 구현하였을까? 답은 `extension`을 활용한 확장 프로토콜에 있다.

## 구조체와 프로토콜

우선, 밑의 코드를 살펴보자

```swift
struct Juice {
    func drink() {
        print("벌컥 벌컥")
    }
}

struct Coffee {
    func drink() {
        print("벌컥 벌컥")
    }
}
```

코드를 보자마자 중복된 코드가 우리를 불편하게 한다. 그리고 당연하게도, 밑에 처럼 프로토콜을 이용하여 리팩토링을 한다.

```swift
protocol Drinkable {
    func drink()
}


struct Juice: Drinkable {
    func drink() {
        print("벌컥 벌컥")
    }
}

struct Coffee: Drinkable {
    func drink() {
        print("벌컥 벌컥")
    }
}
```

하지만, 문제는 사라지지 않았다. 프로토콜의 특성상 선언만 가능하기에 결국 해당 프로토콜을 채택한 모든 구조체에서 구현을 해주어야 하기 때문에 결국 중복된 코드가 발생한다.
이를 위해서 프로토콜에 `extension`을 사용하여 구현을 할 수 있다.

```swift
protocol Drinkable {
}

extension Drinkable {
    func drink() {
        print("벌컥 벌컥")
    }
}

struct Juice: Drinkable {
}

struct Coffee: Drinkable{
}
```

한결 나아진 모습을 볼 수 있다. 이제 아무리 많은 새로운 구조체를 정의하고, `drink()` 메소드를 구현하고 싶다고 하더라도 단순하게 `Drinkable` 프로토콜을 채택해주기만 하면 된다. 또한 프로토콜의 채택을 통해서 해당 구조체가 어떤 형태인지 예측하기 쉽다는 장점 또한 생겨난다.

# 프로토콜을 적용해보자 🎯

사실, Swift를 공부하다보면 `프로토콜 지향 언어`라는 사실을 자주 듣게 되기는 하는데, 정작 Swift를 이용하여 애플리케이션을 만들다 보면 프로토콜을 어떻게 이용하는 것인지에 대한 감이 잘 잡히지 않는다. 결국 객체 지향적으로만 프로그래밍을 하게 되고, 기능이 복잡해질 수록 어설프게 사용한 프로토콜과 상속 등으로 인해서 내가 짠 코드임에도 불구하고 해석하기 어려워진다. 더군다나 혼자서 애플리케이션을 제작하는 경우는 토이 프로젝트인 경우가 많기 때문에, 어떠한 압박감도 없이 그냥 그 프로젝트를 접거나, 새로운 프로젝트를 시작하게 된다. (내가 그랬다... 물론 원인은 다양하다...하하)

쨌든, Swift에서 프로토콜을 이용하여 보기 좋고 사용하기 편한 코드로 리팩토링을 하는 방법을 알아보자. [실무에서 활용하는 프로토콜 중심프로그래밍][realm_porotocol]을 참고하였으며, 예시는 조금 더 간단한 예시로 바꾸었다. 위해 다음과 같은 상황을 가정하자. 먼저 색상이 입혀진 버튼을 여러번 사용하기 위해서 다음과 같이 `UIButton`을 서브클래싱하는 `ColoredButton` 클래스를 만들었다고 하자.

> _당연히 그냥 `button.backgroundColor = UIColor.systemBlue` 식으로 써도 된다.
> 여기서는 그냥 쉬운 예시를 위해서 간단한 기능을 이용하였다._

```swift
import UIKit

class ColoredButton: UIButton {
    func fill(with color: UIColor) {
        self.backgroundColor = color
    }
}

let button = ColoredButton()
button.fill(with: UIColor.systemBlue)
```

개발을 계속 하다 보니, 똑같은 기능이 들어간 라벨도 필요해져서 `UILabel`을 서브클래싱하는 새로운 클래스를 하나 더 만들었다.

```swift

import UIKit

class ColoredButton: UIButton {
    func fill(with color: UIColor) {
        self.backgroundColor = color
    }
}

class ColoredLabel: UILabel {
    func fill(with color: UIColor) {
        self.backgroundColor = color
    }
}

let button = ColoredButton()
button.fill(with: UIColor.systemBlue)

let label = ColoredLabel()
label.fill(with: UIColor.systemBlue)
```

여기서 코드의 중복이 발생하고 리팩토링 과정에서 Swift를 다뤄본 우리는 당연하게도 다음과 같이 코드를 작성하게 된다.

```swift
import UIKit

extension UIView {
    func fill(with color: UIColor) {
        self.backgroundColor = color
    }
}

class ColoredButton: UIButton {
  // 구현부
}

class ColoredLabel: UILabel {
  // 구현부
}

let button = ColoredButton()
button.fill(with: UIColor.systemBlue)

let label = ColoredLabel()
label.fill(with: UIColor.systemBlue)
```

훨씬 간단해지긴 했지만, 몇 가지 문제가 발생한다. 일단, 우리가 만든 `ColoredButton`과 `ColoredLabel`의 구현부를 보아도, `fill(with:)` 함수의 존재를 알기 힘들다. 위의 예시는 같은 파일에 존재하고 간단한 코드라서 괜찮지만, 만약 수많은 소스 파일이 존재하고 복잡한 기능이라면 머리 아파진다. 또한 만약 비슷한 기능이 추가된다면, 더더욱 가독성은 낮아지게 된다.

이러한 상황에서 사용할 수 있는 것이 `확장 프로토콜`이다.

```swift
import UIKit

protocol Fillable { }

extension Fillable where Self: UIView {
    func fill(with color: UIColor) {
        self.backgroundColor = color
    }
}

class ColoredButton: UIButton, Fillable {
  // 구현부
}

class ColoredLabel: UILabel, Fillable {
  // 구현부
}

let button = ColoredButton()
button.fill(with: UIColor.systemBlue)

let label = ColoredLabel()
label.fill(with: UIColor.systemBlue)
```

커스텀 `UIView` 클래스가 `Fillable` 프토로콜을 채택한 것을 바로 알 수 있기에, 훨씬 명확해졌고 코드의 가독성이 향상되었다. 만약 뷰의 모서리를 둥글게 하는 기능을 추가하고 싶다면 `Roundable` 프로토콜을 채택하는 것으로 끝난다.

```swift
class ColoredButton: UIButton, Fillable, Roundable {
  // 구현부
}

class ColoredLabel: UILabel, Fillable, Roundable {
  // 구현부
}
```

기능을 삭제하고 싶다면 채택했던 프로토콜을 지워주면 그만이다. 기존의 방식보다 훨씬 간단하고 가독성이 높은 코드로 만들기 쉬워진 것이다.

> _더 자세한 내용은 레퍼런스에 있는 첫 번째 사이트에 가서 찾아보길 바란다. 훨씬 자세하고, 좋은 예시를 들어 설명해주고 있고, Swift의 프로토콜과 `제네릭`을 결합하여 리팩토링하는 것을 볼 수 있다._

# 마치며 💬

Swift는 배우면 배울수록 참 신기하고 재밌는 언어인 것 같다. 그리고 생각보다도 더 잘만들어진 언어인 것 같다. 하지만 그래서 아직도 많이 어려운 듯 하다. 열심히 공부해보자! 또 포스팅도 좀만 더 자주 올리도록 노력해야겠다... :)

---

# References

- [실무에서 활용하는 프로토콜 중심프로그래밍][realm_porotocol]
- [Swift–프로토콜 지향 프로그래밍][yagom_blog]

[realm_porotocol]: https://academy.realm.io/kr/posts/appbuilders-natasha-muraschev-practical-protocol-oriented-programming/
[yagom_blog]: https://blog.yagom.net/531/
