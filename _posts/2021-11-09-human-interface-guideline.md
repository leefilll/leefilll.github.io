---
layout: post
title: "Human Interface Guideline과 iOS 개발"
image: "/images/07.jpg"
author: leefilll
tags: [iOS, HIG]
---

Human Interface Guideline(이하 HIG)는 애플리케이션 개발자에게 권장되는 소프트웨어 개발 문서이다. 소프트웨어 인터페이스(UI)와 사용자 경험(UX) 향상에 목표를 두고 있다.

# iOS 애플리케이션과 HIG

Apple의 HIG는 iOS의 근간을 이루는 가이드라인 문서로, 강제 사항이라기 보다는 권장사항을 담았다. HIG는 현대의 UX 및 전체적인 디자인 산업에서 큰 영향을 미쳤다는 평가를 받는다. 그렇다고 해서 디자이너만 알아야 할 내용은 아니라고 생각한다. 오히려 iOS 개발자 및 다양항 산업군의 응용 프로그램 개발자라면 누구나 한 번쯤 읽어보길 권하고 있다.

![Frame 1devices](https://user-images.githubusercontent.com/38246878/140851354-42367750-8f80-49cf-ad87-378a30232d33.png)

[Apple Developer 사이트][hig]에 들어가보면 다양한 디바이스에 따른 모든 문서를 확인할 수 있다. 꽤나 방대하기도 하고, 영어로 되어있어서 아무래도 한국어 문서보다는 읽기 힘이 들수도 있지만, 잘 찾아보면 한국어로 번역된 문서도 많이 있다.

# Human Interface Guideline

HIG에서 주장하는 내용은 현재 iOS 애플리케이션의 기본 UI 및 UX에서 우리가 알게 모르게 사용하고 있던 것들이 많다..

- **Clarity: 기능이 명확하게 드러나도록**
- **Deference: 콘텐츠가 중심 적으로 부각될 수 있도록**
- **Depth: 레이어, 모션 등으로 표현된 계층 구조의 깊이 표현**

HIG의 가장 첫 도입 부분에 명시되어 있는 만큼, iOS의 특성을 아주 잘 나타내주는 세 가지라는 생각이 든다. 이외에도 간단하게 일반적인 버튼의 모양 부터, 특정 기능을 하는 버튼의 위치, 각 뷰의 상호 작용 및 제스쳐를 통한 사용자 경험 제공 등 iOS의 모든 내용을 담고 있다. 이전 회사에서 iOS의 개발을 진행하면서, 개인 프로젝트를 진행하면서 HIG를 고려하였던 경험과 스스로 찾았던 해결방법을 공유하려고 한다.

## Accessibility

애플리케이션 개발자는 그 어떤 것보다 최대한 많은 사용자들이 편하게 자신이 개발한 애플리케이션을 사용할 때 가장 큰 보람을 느끼는 것 같다. 이를 위해서는 그냥 고정된 사이즈의 Label을 사용하거나, 작은 사이즈의 iPhoneSE에서 레이아웃이 다 깨지는 등의 문제가 발생하면 마음이 너무 아프다... 많은 사람들이 애플리케이션을 편하게 사용하기 위해서, 다양한 접근성 기능을 고려해야 하지만, 이번에는 가장 기본적인 '더 큰 텍스트'에 대응할 수 있는 방법을 확인해보자.

### 더 큰 텍스트 대응하기

![02](https://user-images.githubusercontent.com/38246878/140851623-c41b0959-77cf-46c6-826f-6805372e571e.png)

'더 큰 텍스트'는 사용자가 `기본 설정 앱 > 손쉬운 사용 > 디스플레이 및 텍스트 크기 > 더 큰 텍스트`에서 설정할 수 있다. 테스트를 위해 간단한 Label 두 개를 만들었다.

```swift
private let titleLabel: UILabel = {
    let label = UILabel()
    label.text = "title label"
    label.font = UIFont.boldSystemFont(ofSize: 25)
    return label
}()

private let bodyLabel: UILabel = {
    let label = UILabel()
    label.text = "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua"
    label.font = UIFont.systemFont(ofSize: 15)
    label.numberOfLines = 0
    return label
}()
```

정상적으로 출력되는 것을 확인해보고, 더 큰 텍스트를 적용한 후에 다시 앱을 실행시키면, 더 큰 텍스트가 적용이 되지 않은 것을 볼 수 있다. 이렇게 되면, 더 큰 텍스트를 평소에 사용하는 사용자에게는 개발자가 적용한 작은 텍스트가 그대로 출력되는 문제점이 발생한다. 이를 해결하기 위해서 UIFont 클래스 메서드인 `UIFont.preferredFont(forTextStyle style: UIFont.TextStyle) -> UIFont`를 이용하면 된다.

```swift
private let titleLabel: UILabel = {
    // 생략
    label.font = UIFont.preferredFont(forTextStyle: .title1)
}()

private let bodyLabel: UILabel = {
    // 생략
    label.font = UIFont.preferredFont(forTextStyle: .body)
}()
```

그리고 다시 실행 해보면, 제대로 적용된 모습을 볼 수 있다.

![03](https://user-images.githubusercontent.com/38246878/140851676-a301c8a3-410e-4840-aebc-fc163c626952.png)

## AutoLayout

iOS 애플리케이션은 기본적으로 UI를 그릴 때 두 가지를 이용할 수 있다. Frame을 계산해서 직접 그리는 방식과, AutoLayout을 이용하여 상대값을 이용하는 방식. Frame을 이용하는 것의 장점은 계산이 쉽고 빠르기 때문에 UI를 그리고 계산하는 속도가 매우 빠르다고 한다. 하지만, 디바이스가 다양해지고, iOS의 회전 등을 대비하기에는 많은 양의 코드와 개발자가 복잡한 계산을 수행해야 한다는 부담감이 존재하기 때문에 잘 쓰지는 않는 것 같다.

이를 쉽고 직관적으로 해결할 수 있는 방법으로는 AutoLayout을 이용하는 방식이 있다. StoryBoard나 Nib파일 등을 이용하여 바로 화면을 보면서 AutoLayout을 적용하는 방법은 개발자가 빠르게 확인하며 UI 개발을 할 수 있도록 돕는다. 하지만, iOS 개발을 하다보면 Merge Conflict가 빈번하게 발생하기도 하고, 다이나믹한 Constraint의 변경, 각 파일의 규모가 커지면 Xcode 상에서 약간의 버벅임이 발생하는 등의 문제를 다들 겪어봤을 것이다.

### SnapKit

![SnapKit](https://camo.githubusercontent.com/31e716ea5f9dcf84442b4a908c4751e39f45aa47/687474703a2f2f736e61706b69742e696f2f696d616765732f62616e6e65722e6a7067)

이번에는 코드를 통해서 손쉽게 AutoLayout을 그릴 수 있는 [SnapKit][snkip] 라이브러리를 보고자 한다.
위에서 이용했던 두 개의 Label의 AutoLayout을 코드로 작성하면 다음과 같다.

```swift
func setupLayouts() {
    view.addSubview(titleLabel)
    view.addSubview(bodyLabel)

    titleLabel.translatesAutoresizingMaskIntoConstraints = false
    bodyLabel.translatesAutoresizingMaskIntoConstraints = false

    NSLayoutConstraint.activate([
        titleLabel.centerYAnchor.constraint(equalTo: view.centerYAnchor,constant: -100),
        titleLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor),
        bodyLabel.topAnchor.constraint(equalTo: titleLabel.bottomAnchor, constant: 30),
        bodyLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor),
        bodyLabel.leadingAnchor.constraint(equalTo: view.layoutMarginsGuide.leadingAnchor),
        bodyLabel.trailingAnchor.constraint(equalTo: view.layoutMarginsGuide.trailingAnchor)
    ])
}
```

간단한 레이아웃을 그리는 것도 꽤나 많은 양의 코드가 필요하다. 또한 코드로 레이아웃을 그리다 보면 자주 발생하는 실수가 `translatesAutoresizingMaskIntoConstraints`를 `false`로 설정하는 일인데, 조금이라도 UI가 복잡해지면 코드의 양은 어마무시하게 늘어나게 되고, 유지보수 및 바뀌는 요구사항에 빠르게 대응하기 어려워지는 문제점이 있다.

```swift
func setupLayouts() {
    view.addSubview(titleLabel)
    view.addSubview(bodyLabel)

    titleLabel.snp.makeConstraints { make in
        make.centerX.equalToSuperview()
        make.centerY.equalToSuperview().offset(-100)
    }

    bodyLabel.snp.makeConstraints { make in
        make.centerX.equalToSuperview()
        make.leading.equalTo(view.layoutMarginsGuide.snp.leading)
        make.trailing.equalTo(view.layoutMarginsGuide.snp.trailing)
        make.top.equalTo(titleLabel.snp.bottom).offset(30)
    }
}
```

SnapKit은 `snp`라는 namespace를 이용하여 아주 멋진 방식으로 모든 뷰의 레이아웃을 분리하고, 여러 발생 가능한 문제를 피한다. 또한 Array가 아닌 클로저를 이용하기 때문에, 코드를 직접 작성해보면 아주 편하고 빠르게 같은 레이아웃을 구성할 수 있다는 것을 알 수 있다.

## LaunchScreen 및 로딩 화면

### LaunchScreen

![04](https://user-images.githubusercontent.com/38246878/140851697-d4429120-b6b2-4ee9-a4d2-098c603ac99f.png)

iOS에서는 스플래시 스크린은 LaunchScreen이라고 명명한다. 애플의 HIG에 따르면, 이 화면은 다음과 같은 원칙을 따라야 한다.

1. **앱이 실행되기 전에 보여지는 화면. 무언가 예술적인 작품을 보여주는 공간은 아니다.**
2. **디바이스에 대응하기 위해 LaunchScreen.storyboard와 AutoLayout을 이용하는 것이 좋다.**
3. **앱의 첫 화면이기 때문에, 앱의 정체성에 대해서 잘 설명할 수 있는 내용을 담는 것이 좋다.**
4. **다양한 언어를 대응하지 못하므로, 최대한 텍스트는 배제하는 것이 좋다.**
5. **LaunchScreen은 브랜딩을 하는 공간이 아니다. 광고에 사용하지 마라.**

즉, LaunchScreen은 최대한 간결하고 짧을 수록 좋다는 것으로 정리할 수 있다. 사용자는 LaunchScreen을 기대하며 앱을 실행하는 것이 아니라, 앱 내의 콘텐츠를 소비하고 싶어하기 때문에 가장 간단한 내용을 담는 것이 좋다는 의미.

### 로딩 화면 UI

또한 애플리케이션이 네트워크 통신, 데이터베이스에서 무언가 작업을 수행하는 등의 시간이 걸리는 작업을 할 때에는, 적극적으로 사용자에게 그것을 알려야 한다. iOS에서는 기본적으로 세 가지 방식을 이용한다.

![05](https://user-images.githubusercontent.com/38246878/140851752-c69ace22-a449-433e-8e63-8d82416cba16.png)

- Activity Indicators: 데이터 로드, 동기화 작업 등 수치화 불가능한 작업
- Progress Bars: 작업의 진행률을 보여주기에 적합. 기간이 명확한 작업에 사용
- Network Activity Indicators: 네트워킹 발생 시 회전, 연결이 되면 멈춤(iPhoneX 시리즈 제외)

### SkeletonView

하지만 추가적으로 라이브러리를 이용하여 더 세련된 방식을 이용할 수도 있다. [SkeletonView][skview]는 보다 쉬운 방식으로 이를 제공한다.

![header2](https://user-images.githubusercontent.com/38246878/140852010-4f3cc5e2-bd61-47b4-9b19-38f6503e8bdf.jpg)

다양한 애니메이션 또한 지원하기 때문에, 원하는 방식과 원하는 모양을 자유롭게 커스텀하여 사용할 수 있다. 물론 전체 화면에 적용할 수도 있으며 특정 셀의 이미지 부분에만 적용하는 등의 방식 또한 가능하다. iOS에서 기본적으로 제공하는 컴포넌트가 조금 지루하게 느껴진다면, 적극적으로 사용하기에 부담 없고, 기본 사용 방법 또한 아주 간단하기에 강력 추천.

![06](https://user-images.githubusercontent.com/38246878/140851766-c508a9f8-6de2-4ff2-b79c-3417ad0febc1.png)

> SkeletonView의 사용법은 [깃허브 저장소][skview]에 아주 자세하게 나와있습니다.

# 마치며

애플리케이션을 개발한다는 것은 플랫폼을 떠나서 정말 멋진 일인 것 같다. 비즈니스 로직을 구성하고, 성능을 최대화 하며 데이터를 보관하고 가공하는 등의 일도 정말 멋진 일이지만, 그 모든 과정은 사용자를 위한 일이고 사용자와 가장 맞닿아 있는 일이 클라이언트 개발이라고 생각한다. 물론 디자이너가 디자인을 그리고 개발자는 그것을 구현하는 것에 집중을 해야하지만, 그 과정에서 수많은 요인들을 고려하면서 더 나은 사용성과 안정성을 갖도록 하는 것도 클라이언트 개발자의 역할이지 않을까.

[hig]: https://developer.apple.com/design/human-interface-guidelines/
[snkip]: https://github.com/SnapKit/SnapKit
[skview]: https://github.com/Juanpe/SkeletonView
