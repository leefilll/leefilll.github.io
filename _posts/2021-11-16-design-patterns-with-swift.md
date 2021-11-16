---
layout: post
title: "Swift와 디자인 패턴(Design Pattern)"
image: "/images/08.jpg"
author: leefilll
tags: [Swift, Design Patterns]
---

디자인 패턴은 1980년대 중반, 4인방(Gang of Four, GoF)이라 불리우는 Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides의 <Design Patterns: Elements of Reusable Object-Oriented Software> 책을 출간하면서 인기를 얻기 시작했다. 이 책에서는 고전적인 23가지의 소프트웨어 디자인 패턴에 관해 설명한다고 한다. (물론 아직 읽어보지는 못했다...)

디자인 패턴은 소프트웨어 개발을 진행하면서 겪을 수 있는 다양한 문제에 대해서 효과적인 해결책을 제시하면서 여러 해에 걸쳐 입증이 되었기 때문에 개발 속도를 높일 수 있다.

또한 유지하기 쉬운 코드를 일관되게 가져갈 수 있다는 점이 디자인 패턴의 장점이다. 정해진 일련의 규칙을 따르기 때문에, 몇 년 후에 코드를 보더라도 다른 팀원이 보더라도 해당 패턴을 인지하고 코드를 해석하기 쉽기 때문이다.

> 디자인 패턴의 주된 철학은 코드의 재사용(Reusable)과 유연성(Flexibility)이다.

디자인 패턴은 주로 생성, 구조, 행위라는 세 가지의 범주로 구분이 된다.

- 생성 패턴(Creational patterns): 객체의 생성에 관하여 추상화를 제공한다.
- 구조 패턴(Structural patterns): 타입의 상속과 합성을 통해서 통일된 추상화를 제공한다.
- 행위 패턴(Behavioral patterns): 타입 간의 소통을 지원한다.

# 생성 패턴, Creational patterns

생성 패턴에는 주요한 두 가지 개념이 있다. 어떤 타입이 생성되어야 하는 지를 캡슐화(Encapsulation)하는 것과 어떻게 인스턴스가 생성되는 지를 숨기는 것이다.

Swift를 이용해서 생성패턴 중, 빌더 패턴(Builder pattern), 추상 팩토리 패턴(Abstract Factory pattern), 싱글톤 패턴(Singleton pattern)을 살펴보려고 한다.

### 빌더 패턴, Builder pattern

빌더 패턴은 다수의 멤버 변수나 상태 값들에 대해서 처리해야 하는 문제를 해결하면서 객체의 생성을 돕는다. 복잡한 타입으로부터 생성 로직을 분리하며, 다른 타입을 추가한다.

```swift
struct Sandwich {
  var name: String
  var bread: Bread
  var egg: Bool
  var cheese: Bool
  var lettuce: Bool
  /* ... more ... */
}
```

만약 위와 같은 복잡한 형태의 모델이 있다고 했을 때, 단순하게 생성자를 이용하면 다음과 같이 생성해야만 한다.

```swift
var eggSandwich = Sandwich(name: "Egg Sandwich", bread: Bread.plain, egg: true, cheese: false, lettuce: true /* ... */ )
var cheeseSandwich = Sandwich(name: "Cheese Sandwich", bread: Bread.plain, egg: false, cheese: true, lettuce: true /* ... */)
```

각각의 인스턴스를 생성하는데, 너무 많은 양의 코드가 필요해진다. 또한 Swift의 경우 전달인자 레이블(Argument Label)을 설정할 수 있고, 많은 IDE에서 매개변수(Parameter)에 대한 힌트를 제공해주기는 하지만, 각 매개변수의 순서, 타입 등에 대한 관리가 어려워진다. 또한 경우에 따라서 `nil`값을 하나하나 넣어주어야 할 때도 있을 수 있다. 빌더 패턴을 이용하여 이를 리팩토링 해보자.

```swift
struct SandwichBuilder {
  private var name: String = "Sandwich"
  private var bread: Bread = .plain
  private var egg: Bool = false
  private var cheese: Bool = false
  private var lettuce: Bool = true

  mutating func addEgg() { self.egg = true }
  mutating func addCheese() { self.cheese = true }

  public func build(name: String) -> Sandwich {
    return Sandwich(name: name, bread: bread, egg: egg, cheese: cheese, lettuce: lettuce)
  }
}
```

빌더 패턴을 이용하면 이렇게 인스턴스의 생성을 캡슐화하고, 클라이언트에서 인스턴스를 생성할 때 훨씬 깔끔하고 오류가 발생할 확률을 줄여줄 수 있다.

```swift
var builder = SandwichBuilder()
builder.addEgg()
let eggSandwich2 = builder.build(name: "Egg Sandwich")
```

### 추상 팩토리 패턴, Abstract Factory pattern

팩토리 패턴은 생성할 정확한 타입을 직접 명시하지 않으면서 객체의 생성을 돕는다. 즉, 생성할 타입을 런타임에 선택하게 되는 방식이다.

```swift
protocol Vehicle {
  func go()
}

class Car: Vehicle {
  func go() {
    print("Car Start")
  }
}

class Bike: Vehicle {
  func go() {
    print("Bike Start")
  }
}
```

프로토콜을 따르는 두 가지의 클래스를 선언하였다. 편의상 멤버 변수는 생략하였다. 단순 팩토리 패턴의 경우, 팩토리 클래스 내부에서 일반적으로 `if-else`구문이나 `switch`구문을 이용하여 각각의 서브 클래스를 생성하여 리턴한다. 추상 팩토리 패턴은 인자로 구체적인 팩토리 클래스를 받아서 분기 구문을 걷어내는 방식이라 생각하면 쉽다.

```swift
protocol VehicleAbstractFactory {
  func createVehicle() -> Vehicle
}

class CarFactory: VehicleAbstractFactory {
  func createVehicle() -> Vehicle {
    return Car()
  }
}

class BikeFactory: VehicleAbstractFactory {
  func createVehicle() -> Vehicle {
    return Bike()
  }
}

class VehicleFactory {
  static func createVehicle(factory: VehicleAbstractFactory) -> Vehicle {
    return factory.createVehicle()
  }
}
```

`Vehicle`프로토콜에 별다른 멤버 변수가 없기에 단순히 생성자만 호출해주면 된다는 점을 기억하고 보자. 구체적으로 `Car`와 `Bike` 인스턴스를 생성해주는 팩토리 클래스를 선언해주고, 각각 알맞은 인스턴스를 생성하여 리턴하고 있다. 그리고 클라이언트와 접점이 되는 `VehicleFactory`에서 최종적으로 알맞은 `Vehicle`을 리턴해준다.

```swift
let car = VehicleFactory.createVehicle(factory: CarFactory())
let bike = VehicleFactory.createVehicle(factory: BikeFactory())
```

### 싱글톤 패턴, Singleton pattern

싱글톤 패턴은 디자인 패턴 중에서 가장 남용이 많이 되는 패턴이라고 한다. 이용하기 편하고 애플리케이션 내에 어느 지점에서나 객체를 가져올 수 있기 때문에 나도 무심결에 여기저기 많이 사용했던 기억이 있다.

싱글톤 패턴은 애플리케이션 생애 동안 클래스 인스턴스를 단일 인스턴스로 제한한다. 따라서 애플리케이션이 실행되는 내내 싱글톤 인스턴스는 계속 살아 있게 된다.

> 싱글톤 패턴은 참조 타입에서만 가능하기 때문에 `class`를 이용해야 한다.

```swift
class Singleton {
  static let shared = Singleton()
  private init() { }
}
```

여기서 주목할 점은 생성자의 접근 수준을 `private`으로 설정했다는 점이다. 외부의 접근을 차단시켜서 `Singleton` 클래스의 `shared` 인스턴스로만 접근 가능하도록 제한하는 것이 싱글톤 패턴이다.

```swift
let instance = Singleton.shared
```

애플리케이션 어느 지점에서나 인스턴스에 접근하고 싶다면 위와 같이 접근 할 수 있다.

실제로 iOS 개발을 하다보면 싱글톤 패턴을 심심치 않게 볼 수 있다. `UIApplication.shared` , `NotificationCenter.default`나 `UserDefaults.standard`가 대표적인 예가 될 수 있다. ([참고](https://developer.apple.com/documentation/swift/cocoa_design_patterns/managing_a_shared_resource_using_a_singleton)) 한 가지 재밌는 사실은 `UIApplication`의 경우, 접근 제어가 없는 Objective-C에서 작성되었기 때문에 생성자를 사용자가 임의로 호출할 수 있다는 사실이다. 컴파일러가 이를 잡아내지는 못하지만 런타임 시점에 Xcode가 이를 인지하고 오류를 뱉어낸다.

> 싱글톤 패턴을 구현하기 쉽다는 이유로 막 사용하게 되면 많은 문제를 낳을 수 있다. 대표적인 예로 멀티 스레딩 환경에서 여러 개의 인스턴스가 동시에 생성되는 경우나, 과도한 사용은 명확하지 않은 분리로 인해서 유지 보수 하기 어렵다는 점, 테스트 하기 어렵다는 점 등이 있다. 자세한 사항은 [이곳](https://matteomanferdini.com/swift-singleton/)을 참고하자

# 구조 패턴, Structural patterns

구조 디자인 패턴은 어떻게 타입들을 결합하여 더 큰 구조체로 만들 수 있는 가를 나타낸다. 이렇게 하는 이유는, 큰 구조체의 경우 개발하기 수월하고 개별적인 타입이 갖고 있는 수많은 복잡도를 감추기에 좋기 때문이다.

### 브릿지 패턴, Bridge pattern

브릿지 패턴은 추상화를 구현에서 분리하여 각각 독립적으로 변화할 수 있도록 하는 패턴이라고 정의되어 있다. 즉, 두 계층을 가진 추상화와 유사하다고 할 수 있다.

브릿지 패턴은 주로 4가지의 계층을 갖는다.

- **Abstraction 계층**
  기능 계층의 최상위 클래스. 추상 인터페이스를 정의하고, `Implementor`에 대한 레퍼런스를 갖는다.
- **Refined Abstraction 계층**
  `Abstraction`에 정의된 인터페이스를 확장한다.
- **Implementor 계층**
  구현 클래스를 위한 인터페이스를 정의한다.
- **Concrete Implementor 계층**
  `Implementor`인터페이스를 실제 기능으로 구현한다.

```swift
// MARK: - Implementor

protocol VehicleImplementor {
  func go()
}

// MARK: - Abstraction

class Vehicle {
  var implementor: VehicleImplementor

  init(implementor: VehicleImplementor) {
    self.implementor = implementor
  }

  func go() {
    implementor.go()
  }
}

// MARK: - Concrete Implementor

class Car: VehicleImplementor {
  func go() {
    print("Car starts")
  }
}
```

`Refined Abstraction`은 `Abstraction`에 기능을 추가하고 싶을 때 생성할 수 있는데, Swift에서는 Extension을 이용할 수도 있다. 즉, 구현부에 기능을 직접 추가하는 것이 아니라 `Abstraction`을 확장하게 되면, 추상화와 구현부가 분리되어서 구현부에서 필요한 기능을 호출하면 되기 때문에 Bridge Pattern이라고 한다.

### 퍼사드 패턴, Facade pattern

퍼사드 패턴은 크고 복잡한 코드에 간소화된 인터페이스를 제공한다. 즉, 복잡한 것들은 뒤로 숨겨서 API를 사용하기 쉽게 해주는 디자인 패턴이다. 주로 서로 함께 동작하도록 설계가 되어있는 많은 API를 더 사용하기 쉬운 하나의 API로 만들어주기 위해 사용한다.

```swift
struct Hotel {
  var name: String
}

struct HotelBooking {
  static func bookHotel(hotel: Hotel) { /*... Logic ...*/}
}

struct Flight {
  var name: String
}

struct FlightBooking {
  static func bookFlight(flight: Flight) { /*... Logic ...*/}
}
```

여행을 위한 `Hotel`과 `Flight` 구조체가 있고, 각각의 예약을 위한 API가 존재한다고 하자. 여기서는 최대한 간결하게 표현을 하였다. 초기에는 API를 개별적으로 호출하기에 부담이 없을 수 있다. 그러나, 요구 사항은 항상 변하기 마련이고, 다양한 기능이 추가되면 분산되어 있는 코드는 더 복잡해지고, 수정하기 어려워진다.

```swift
struct TripFacade {
  var hotels: [Hotel]
  var flights: [Flight]

  init(hotels: [Hotel], flights: [Flight]) {
    self.hotels = hotels
    self.flights = flights
  }

  func bookTrip(hotel: Hotel, flight: Flight) {
    HotelBooking.bookHotel(hotel: hotel)
    FlightBooking.bookFlight(flight: flight)
  }
}
```

이를 방지하기 위하여 `TripFacade` 구조체를 선언하였다. 이제 사용자는 각각의 API를 개별적으로 접근할 필요 없이 Facade 타입을 통해서 예약을 진행하면 된다. 즉, 개별적인 API를 통합하여 간단하게 사용할 수 있는 인터페이스를 만드는 것이라 생각하면 쉽다.

# 행위 패턴, Behavioral patterns

행위 디자인 패턴은 타입 간에 상호작용이 어떻게 이뤄지는 가에 대해서 설명한다. 즉, 서로 다른 타입의 인스턴스 간에 어떻게 소통하는 지를 설명한다고 보면 된다.

### 커맨드 패턴, Command pattern

커맨드 패턴은 커맨드 실행과 호출자를 서로 분리한다. 이렇게 해서, 가능한 여러 행동에서 어떤 것을 사용할 지 런타임 시점에 결정하도록 한다.

```swift
protocol Command {
  func execute(aOperand: Int, operand: Int) -> Int
}

struct Addition: Command {
  func execute(aOperand: Int, operand: Int) -> Int {
    return aOperand + operand
  }
}

struct Subtraction: Command {
  func execute(aOperand: Int, operand: Int) -> Int {
    return aOperand - operand
  }
}
```

계산을 하는 로직을 만들기 위해서 `Command`라는 프로토콜과 해당 프로토콜을 구현한 두 개의 구조체를 선언하였다.

```swift
struct Calculator {
  func calculate(num1: Int, num2: Int, command: Command) -> Int {
    return command.execute(aOperand: num1, operand: num2)
  }
}
```

그리고 `Invoker`의 역할을 수행하는 계산기 구조체를 선언하였다. 계산을 원할 때는 이 구조체를 통해서 두 개의 피연산자와 `Command` 프로토콜을 따르는 구조체를 넣어주면 된다.

커맨드 패턴의 장점은 작업을 수행하는 객체와 호출하는 객체를 나누어 단일 책임 원칙(Single Responsibility Principle)을 만족하고, 새로운 작업을 추가하고 싶다면(예를 들어, 나누기나 곱하기) `Command` 프로토콜을 따르는 새로운 구조체를 생성만 하면 된다.

### 옵저버 패턴, Observer pattern

옵저버 패턴은 아마 iOS 개발을 조금이라도 했었다면 다들 친숙할 것이라고 생각이 된다. 이벤트가 트리거 될 경우, 이를 전달 받을 옵저버를 등록하고 이벤트가 발생한 경우 알림을 받게 되는 형태이다.

```swift
extension NSNotification.Name {
  static let NewNotificationName = NSNotification.Name("NewNotificationName")
}
```

우선, NotificationCenter을 이용하는 방법이다. 이를 위하여 `NSNotification.Name` 타입을 지정해준다.

> 전달 받는 알림 타입의 이름을 직접 하드코딩할 수도 있지만, 이는 권고되지 않는 방법이다. 위와 같이 Extension을 활용하면 쉽게 확장할 수 있다.

```swift
class Post {
  private let notificationCenter = NotificationCenter.default

  func post() {
    notificationCenter.post(name: NSNotification.Name.NewNotificationName, object: nil)
  }
}

class Observer {
  private let notificationCenter = NotificationCenter.default

  init() {
    notificationCenter.addObserver(self,
                                   selector: #selector(receiveNotification(_:)),
                                   name: NSNotification.Name.NewNotificationName,
                                   object: nil)
  }

  @objc func receiveNotification(_ notification: Notification) {
    print("Notification Received")
  }
}
```

Notification Center에 알림을 전달하는 타입과 그 알림을 옵저빙하는 타입을 선언하였다. `Post` 타입의 인스턴스가 `post()` 메소드를 호출할 경우, `Observer` 타입의 인스턴스에 선언된 `receiveNotification(_:)` 메소드가 실행되게 된다.

이렇게 알림 센터를 이용하는 방식은 아주 쉽고 간단하다. 또한 객체의 의존성을 거의 갖지 않으면서 애플리케이션 생애 동안 어느 지점에서나 사용할 수 있다는 장점이 있다. 그리고 센터를 이용하기 때문에, 다수의 수신자에게 한번에 알릴 수 있다.

위처럼 알림 센터를 이용할 수도 있지만, 직접 지정된 단일 옵저버에게만 알림을 보낼 수도 있다. 델리게이트 패턴을 생각하면 쉽게 이해가 된다. 하나의 객체에 Observer 프로토콜을 참조하면서 이벤트가 발생하는 시점에 참조하는 observer의 적당한 행동을 실행시키면 된다.

# 마치며

디자인 패턴에 대해서 모르고 다른 사람들의 코드를 보거나, 해석하려면 어려운 경우가 종종 있었다. 나 스스로도 많이 느꼈고 개발을 진행하면서 발생하는 다양한 문제들에 대해서 깔끔하게 해결할 수 있는 방법을 제시해준다는 점에서 참으로 매력적인 방식인 것 같다. 그렇다고 해서 작은 문제를 해결하기 위해 과도한 디자인 패턴의 사용은 오히려 오버 엔지니어링이 될 수는 있다고 생각이 들었다. 많은 디자인 패턴을 접하고, 코드를 해석하고 더 유연하고 재사용성이 높은 프로그래밍을 할 수 있어야겠다.

# References

[https://matteomanferdini.com/swift-singleton/](https://matteomanferdini.com/swift-singleton/)

[스위프트 4: 프로토콜지향 프로그래밍](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791161752280)

---
