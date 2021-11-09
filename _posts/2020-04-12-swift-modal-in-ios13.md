---
layout: post
title: "iOS 13에서 Modal의 ViewLifeCycle"
date: 2020-04-12
image: "/images/05.jpg"
author: leefilll
tags: [Swift, iOS]
---

2019년에 `WWDC`에서 발표된 iOS 13에서는 꽤 많은 것들이 바뀌었다. iPadOS가 따로 출시되었기도 하고, 다크 모드의 도입으로 모든 애플리케이션에 획기적인 UI의 변화를 이끌었기 때문이다. 이 외에도 다양한 것들이 바뀌긴 하였지만, iOS 개발을 공부하면서 가장 크게 느껴진 것이 있다. 바로 `모달(Modal)`방식이다.

# iOS 11과 iOS 13의 Modal 📱

기존의 모달 방식은 기본적으로 `full screen`을 덮는 방식이다. iOS 13 이전의 모달과 iOS 13의 모달 방식을 비교하기 위해서 **iPhone 8 - iOS 11** 버전과 **iPhone 11 - iOS 13** 을 비교하였다.

<img width="1009" alt="Screen Shots" src="https://user-images.githubusercontent.com/38246878/79061488-bd0a5e00-7ccb-11ea-9d50-e395f30d153e.png">

비교를 위해서 간단하게 프로젝트를 구성하였다. 우선, 홈 화면인 `ViewController`에서 `Present Modal` 버튼을 누르면 `ModalViewController`가 모달 방식으로 나온다. 그리고 `Dismiss` 버튼을 누르게 되면 모달이 사라진다. 두 개의 뷰 컨트롤러의 라이프 사이클 메소드에 각각 `print()`를 이용하여 출력 하도록 해서 어떤 메소드가 언제 실행되는 지 확인해 보려고 한다.

```swift
  override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    print(type(of: self), #function)
  }

   override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    print(type(of: self), #function)
   }

   override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
    print(type(of: self), #function)
   }

   override func viewDidDisappear(_ animated: Bool) {
    super.viewDidDisappear(animated)
    print(type(of: self), #function)
   }
```

> _여기서는 `modalPresentationStyle`은 지정하지 않은 기본값으로 진행하였다._

### iPhon 8 - iOS 11

우선 iOS 11 에서 모달을 띄우면 다음과 같이 출력된다.

```zsh
ViewController viewWillAppear(_:)
ViewController viewDidAppear(_:)
ViewController viewWillDisappear(_:)
ModalViewController viewWillAppear(_:)
ModalViewController viewDidAppear(_:)
ViewController viewDidDisappear(_:)
```

출력을 보면 알 수 있듯이, `ModalViewController`가 나타나기 전에 `ViewController`는 뷰 계층에서 제거될 준비를 하고. `ModalViewController`가 화면에 로드되면 `ViewController`는 아예 제거된다. 이 상태에서 다시 모달을 dismiss 해보자.

```zsh
ModalViewController viewWillDisappear(_:)
ViewController viewWillAppear(_:)
ViewController viewDidAppear(_:)
ModalViewController viewDidDisappear(_:)
```

예상했던 대로, 다시 `ViewController`를 뷰 계층으로 로드하는 것을 볼 수 있다.

> _물론, `modalPresentationStyle`을 `.overCurrentContext`나 `.overFullScreen`으로 하게 되면 `ViewController`를 뷰 계층에서 제거하지 않고 모달을 띄울 수는 있다._

### iPhone 11 - iOS 13

iOS 13에서 모달을 띄우면 마치 새로운 화면이 이전의 화면을 밀고 나오는 듯한 화면이 연출된다.

<img width="741" alt="Screen Shot 2020-04-12 at 15 23 21" src="https://user-images.githubusercontent.com/38246878/79062050-93eccc00-7cd1-11ea-8064-795f0c76b8ed.png">

그렇다면 ViewLifeCycle은 어떻게 변화했을 지 콘솔을 확인해보자.

```zsh
ViewController viewWillAppear(_:)
ViewController viewWillAppear(_:)
ViewController viewDidAppear(_:)
ViewController viewDidAppear(_:)
ModalViewController viewWillAppear(_:)
ModalViewController viewDidAppear(_:)
```

iOS 11과 다른 부분이 보인다. 바로 `ModalViewController`가 뷰 계층에 추가되어도, `ViewController`가 제거되지 않은 것이다. 별 것 아닌것 같아도 이 부분이 iOS 개발 공부를 하다보면 은근히 사람 머리 아프게 하는 부분이다. iOS 13 만을 대상으로 앱을 개발한다면 상관이 없겠지만, (개인적인 생각으로...)아직까지 그렇게 개발하기에는 너무 시기상조인 것 같다. 즉, iOS 버전에 따른 이러한 차이를 대응할 줄 알아야 한다는 말이 된다(하하).

이제 `ModalViewController`를 dismiss 해보자

```zsh
ModalViewController viewWillDisappear(_:)
ModalViewController viewDidDisappear(_:)
```

그냥 `ModalViewController`만 뷰 계층에서 제거되고 `ViewController`의 어떠한 라이프 사이클 메소드도 호출되지 않았다(심지어 `viewWillAppear` 조차도...). 근데 사실 직관적으로 볼 때도 그렇고, 모달 방식의 목적 및 UI적 관점에서 볼 때는 이러한 방식이 더 잘 어울리고 이해하기도 쉬운 것 같긴 하다.

각각의 버젼 별로 대응해주어도 되고, 아니면 그냥 `modalPresentationStyle`을 `.fullScreen` 등으로 지정해주거나 커스텀으로 직접 만들어도 되겠다.

> _iOS 13에서의 기본 `modalPresentationStyle`은 Horizontal Size Class가 compact(아이폰 세로 모드)일 때, `.automatic`이 기본값이다. 이 값은 대부분의 경우에는 `.pageSheet`으로 모달을 띄우고, 뷰 컨트롤러에 따라서 다른 스타일을 적용하는 값이다. 참고로 iOS 11에서는 기본 `modalPresentationStyle`은 `.fullScreen`이다. 자세한 내용은 레퍼런스를 참고._

# Interactive Dismissal 👨‍💻

`ViewLifeCycle`에 대응하는 것은 그 사이클의 작동 방식만 알게 되면 큰 문제가 없다. 버전별로 자신의 입맛에 맞게 대응할 수 있으니까. 그런데 문제는 iOS 13의 기본 모달 방식에 있다. 이 방식에서는 애플에서 말하는 Interactive Dismiss, 즉 제스쳐(손가락으로 뷰를 쓸어 내리는 것)를 통해서 모달을 dismiss할 수 있는데, 이렇게 dismiss하게 될 때는 `UIAdaptivePresentationControllerDelegate` 프로토콜의 델리게이트 메소드를 이용해야 한다.

`isModalInPresentation`는 iOS 13부터 생겨난 `ViewController`의 프로퍼티다.
이 프로퍼티를 `true`로 설정하면 스와이프해서 모달을 dismiss할 수 없게 된다.
기본값은 `false`이며, 스와이프로 dismiss가 가능해진다.

```swift
if #available(iOS 13.0, *) {
  self.isModalInPresentation = true
}
```

# UIAdaptivePresentationControllerDelegate 📜

이 프로토콜에는 다음과 같이 네 개의 메소드가 있다.

- `presentationControllerDidAttemptToDismiss(\_:)`
- `presentationControllerShouldDismiss(\_:)`
- `presentationControllerWillDismiss(\_:)`
- `presentationControllerDidDismiss(\_:)`

우선 `isModalInPresentation`가 `true`일 경우, `presentationControllerDidAttemptToDismiss(\_:)`가 호출된다.
만약 `isModalInPresentation`가 `false`로 되어 있다면, 차례대로 `ShouldDismiss(\_:)` -> `WillDismiss(\_:)` -> `DidDismiss(\_:)`가 호출되게 된다.

이 델리게이트 메소드를 이용해서, 특정 조건을 만족할 때는 Interactive Dismissal을 가능하게 하고, 평소에는 안되게 한다던가 혹은 dismiss시에 이벤트나 데이터 전달을 할 수 있으니 꼭 알아두자.

> _애플에서 제공하는 튜토리얼은 [여기](https://developer.apple.com/documentation/uikit/view_controllers/disabling_pulling_down_a_sheet)서 참고할 수 있다_

# 마치며 💬

계속 iOS 앱 개발에 관해서 공부만 하다 보니까 포스팅도 Swift가 많다. 물론 나는 개인적으로 Swift를 좋아하기도 한다. 근데 문제는 계속 해서 SwiftUI나 이렇게 iOS가 업데이트 되면서 공부할게 너무나도 많다는 것이다... 천천히 하나씩 내가 궁금하거나 공부해야 겠다는 생각이 들면 포스팅 꾸준히 해야겠다...!

---

# References

- [iOS Lifecycle When Dismissing a Modal View With .pageSheet in iOS 13][medium_blog]
- [iOS 13 ) UIModalPresentationStyle - ZeddiOS님의 블로그][zedd_blog]

[medium_blog]: https://medium.com/better-programming/the-lifecycle-and-control-when-dismissing-a-modal-view-with-pagesheet-in-ios-13-4bbd1e3e1ec7
[zedd_blog]: https://zeddios.tistory.com/828
