---
layout: post
title: "[Swift] Xcode에서 Storyboard 없이 iOS 앱 개발하기"
date: 2020-04-10
image: "/images/03.jpg"
author: leefilll
tags: [Swift, iOS, Xcode]
---

# Storyboard 🖥

`Storyboard`는 Xcode를 이용해서 iOS 앱을 개발할 때 여러모로 유용하다. 쉽고 편하다기 보다는, 직관적이고 한눈에 앱의 플로우를 볼 수 있다는 장점이 있다. 하지만... 팀 단위로 `Storyboard`를 통해서 작업하고 코드를 공유하다 보면, 생각보다 자주 코드 병합(merge) 과정에서 오류가 난다. 또한 스토리보드가 방대해지면 꽤 느려진다...

이렇게 `Storyboard`가 많은 장점을 갖긴 하지만, 동시에 단점도 존재하기 때문에 필자는 얼마 전 부터, 오직 코드로만 iOS 앱 개발을 공부하고 있다. 생각보다 어렵지도 않고 (물론, 가끔은 머릿속에 UI가 그려지지가 않긴 한다 😂), **[SnapKit][snapkit_github]** 이나 다른 라이브러리들과 함께 개발하면 오히려 더 편한 점들도 많은 것 같다.

그런데 `Storyboard`없이 Xcode에서 개발을 하기 위해선 몇 가지 작업을 해주어야 한다. 생각보다 더 간단하다.

<br/>

# Xcode 설정 ⚙️

## 1. 프로젝트를 생성한다.

![image](https://user-images.githubusercontent.com/38246878/78570544-19025c00-7860-11ea-922b-c4b7bb0528e4.png)

따로 해줄 건 없고, 그냥 생성하자.

<br/>

## 2. 프로젝트 이름과 타겟을 확인하고 General 탭에서 Main Interface를 삭제한다.

![image2](https://user-images.githubusercontent.com/38246878/78572014-e35e7280-7861-11ea-9014-676ceebeaa10.png)

위의 스크린샷 3번에 해당하 `Main Interface` 항목에서 기본으로 설정되어 있는 `Main`을 삭제한다. 그냥 지우고 리턴키 누르면 된다. 물론, `Main.storyboard` 파일은 삭제해도 상관 없다.

<br/>

## 3. info.plist에서 Storyboard Name 키를 삭제한다.

![image3](https://user-images.githubusercontent.com/38246878/78572406-6253ab00-7862-11ea-8e6a-bb2df831c92d.png)

`Application Scene Manifest` 키 부분에 마우스를 갖다 대면 `+` 버튼이 나오는데, 계속 확장해서 위와 같이 펼친다. 그리고 `Storyboard Name` 키 부분을 삭제한다. `-` 버튼으로 삭제하면 된다.

<br/>

## 4. SceneDelegate.swift에서 다음과 같이 코드를 추가한다.

![image4](https://user-images.githubusercontent.com/38246878/78573907-3c2f0a80-7864-11ea-94c0-089bc9c10fee.png)

`SceneDelegate`는 Xcode 11부터 추가된 기본 iOS 템플릿이다. 이전의 `AppDelegate`의 역할 중, UI의 LifeCycle에 관한 부분을 `SceneDelegate`가 맡게 된 것이다. window 개념이 scene으로 대체되면서, 앱에서 여러 scene을 갖을 수 있게 된 것이다. 쨌든, `SceneDelegate` 클래스의 `scene(_:, willConnectTo:, options:)` 메서드에 다음과 같이 적어주면 된다.

```swift
// SceneDelegate.swift
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
  guard let windowScene = (scene as? UIWindowScene) else { return }
  window = UIWindow(windowScene: windowScene)
  window?.rootViewController = ViewController() // RootViewController를 연결
  window?.makeKeyAndVisible()
}
```

`window?.rootViewController`와 원하는 ViewController(TabBarController, NavigationController, ViewController, 등)를 연결시켜주면 된다.

<br/>

# 마치며 💬

이제 머지할 때마다 충돌할 걱정없이 코드로 작업을 시작하면 된다. 또한 `@IBOutlet` 혹은 `@IBAction` 을 이용하기 위해 창을 두 개씩 띄울 필요도 없다. 가끔은 `ViewLifeCycle` 때문에 몇 가지 문제가 발생하기는 하지만, 이것에 대해서는 다음 포스팅에...

<br/>

---

# References

- [Starting an iOS project without storyboard][medium_blog]
- [How To Create A Swift App Without Storyboard][dev2qa_blog]

[medium_blog]: https://medium.com/@how_noobs_think/starting-an-ios-project-without-storyboard-94c0e618a119
[dev2qa_blog]: https://www.dev2qa.com/how-to-create-a-swift-app-without-storyboard/
[snapkit_github]: https://github.com/SnapKit/SnapKit
