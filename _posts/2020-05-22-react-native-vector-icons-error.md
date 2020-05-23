---
layout: post
title: "[React Native] react-native-vector-icons 오류 해결 방법"
author: "leefilll"
---

<p align="center">
  <img width="400" alt="center" src="https://cloud.githubusercontent.com/assets/378279/12009887/33f4ae1c-ac8d-11e5-8666-7a87458753ee.png">
</p>

한동안 개인 프로젝트를 Xcode와 Swift로 개발하다가, 문득 다른 프로젝트는 React Native를 이용해봐야 겠다는 생각이 들었다. 구글 플레이스토어에도 출시 해보고 싶기도 하였고, React Native를 처음 이용해볼 때, 그 빠른 핫 리로드(Hot reload)와 라이브 리로드(Live reload)를 사용하며 느꼈던 쾌감을 다시 한번 느껴보고 싶기도 하였다. 😂

그렇게 호기롭게 다 까먹었던 React Native를 다시 한번 시작하게 되었다. 그러나 얼마 못가서 당시에 마주했던 끝없는 에러의 연속에 빠져버렸는데, 그 중 가장 나를 괴롭혔던 `react-native-vector-icons` 관련 오류에 대하여 해결한 방법을 기록해 놓고자 한다.
<!-- <br/> -->

## [react-native-vector-icons](https://github.com/oblador/react-native-vector-icons) 📱

react-native-vector-icons는 정말 빠르고 유용하게 임포트하여 사용할 수 있는 매우 많은 아이콘과 폰트를 제공한다.

```zsh
npm install react-native-vector-icons
cd ios && pod install && cd ..
```

npm을 이용하여 패키지를 설치하고 ios 폴더로 가서 `pod install` 명령어를 실행한다. 원래는 이렇게 하면 되어야 하는데...
폰트가 없다고 에러가 떴다. 도큐멘트를 참고했는데도, 역시 실패하였다. 꽤 많은 시간을 투자한 결과, ios 프로젝트 내부의 info.plist를 수정해서 해결되었다.

```xml
<!-- /ios/{project}/info.plist -->

<key>UIAppFonts</key>
    <array>
        <string>AntDesign.ttf</string>
        <string>Entypo.ttf</string>
        <string>EvilIcons.ttf</string>
        <string>Feather.ttf</string>
        <string>FontAwesome.ttf</string>
        <string>FontAwesome5_Brands.ttf</string>
        <string>FontAwesome5_Regular.ttf</string>
        <string>FontAwesome5_Solid.ttf</string>
        <string>Foundation.ttf</string>
        <string>Ionicons.ttf</string>
        <string>MaterialCommunityIcons.ttf</string>
        <string>MaterialIcons.ttf</string>
        <string>Octicons.ttf</string>
        <string>SimpleLineIcons.ttf</string>
        <string>Zocial.ttf</string>
    </array>
```

공식 문서에 따르면 **몇몇(?)** iOS 개발자들이 오류를 겪는 다고 한다.
작년에 React Native 프로젝트를 진행하면서도, 그대로 겪었던 문제였는데 기록을 해놓지 않아서 또 꽤 많은 시간을 허비하였다.
앞으로는 조금 더 공식 문서를 꼼꼼하게 읽으면서 에러 처리에 대해서 Notion이나 여기에 기록을 해놓아야 겠다.

## 참고

혹시나 오류가 발생했을 때, 특별한 무언가를 건드렸거나, 새로운 패키지를 받은 적이 없다면 다음 명령어를 실행해보면 해결이 되는 경우가 많다.

우선, 클라이언트를 실행 중인 서버와 터미널을 종료 시키고, 시뮬레이터에서 번들을 삭제한다.
그리고 밑의 명령어 처럼 다시 초기화를 시킨다.

```zsh
rm -rf node_modules package-lock.json ios/build
watchman whatch-del-all
npm install && cd ios && pod install && cd .. && react-native run-ios
```

그 외의 오류 사항들은 계속해서 이 포스팅에 이어붙일 생각이다.

---

# References

- [react-native-vector-icons](https://github.com/oblador/react-native-vector-icons)
- 수 많은 stackoverflow...
