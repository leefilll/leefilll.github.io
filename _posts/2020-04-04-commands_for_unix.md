---
layout: post
title: "Unix 명령어 기초 지식 (Basic commands for Unix)"
author: "leefilll"
---

# Unix 📺

`유닉스`는 1970년대 초반, 벨 연구소의 직원들에 의하여 개발된 운영 체제이다. `macOS`를 사용한다면 `유닉스 계열`의 운영체제를 사용하는 것이기 때문에, `유닉스`의 기초적인 명령어를 기록으로 남겨놓고자 포스팅을 하게 되었다.

> _물론 리눅스 또한 유닉스에서 파생되었기 때문에, 같은 명령어를 사용할 수 있다._

<br/>

# 표준 스트림 🏷

`표준 스트림(standard streams)`은 컴퓨터 프로그램과 환경 사이에 연결된 입출력 통로를 말한다. 일반적으로 다음과 같은 세 개의 스트림을 지칭한다.

- 표준 입력 스트림(standard input, STDIN)
- 표준 출력 스트림(standard output, STDOUT)
- 표준 오류 출력 스트림(standard error output, STDERR)

대부분의 명령어는 입력 데이터를 받고, 가공하고, 출력하는 3가지 단계로 동작한다. 기본적으로 표준 입력은 키보드의 입력, 표준 출력과 표준 오류 출력은 콘솔 화면으로 출력한다. 이 입력과 출력을 파일에서의 입력, 파일에서의 출력으로 변경할 수 있는데, 이를 `리다이렉트(redirect)`라고 한다.

```zsh
# 표준 출력 리다이렉트
# 명령어의 실행 결과를 파일에 저장한다
# 명령어 > 경로 (표준 입력: 명령어 < 경로)

$ cat test
Leefillls blog
개발은 처음이라

$ cat test > test2
$ cat test2
Leefillls blog
개발은 처음이라
```

<br/>

# 파이프 ⛓

`파이프`를 사용하게 되면, 어떤 명령어의 표준 출력을 또 다른 명령어의 표준 입력으로 전달할 수 있다.

```zsh
# echo 명령어의 표준 출력을 cut 명령어의 표준 입력으로 전달

$ echo 'leefilll,개발은 처음이라,github blog' | cut -d , -f 2
개발은 처음이라
```

<br/>

# 기초 명령어 💻

## cat 명령어

`cat` 명령어는 인자로 전달된 파일을 표준 출력을 이용하여 표시한다.

```zsh
# text 텍스트 파일을 읽어옴
$ cat text.txt
leefilll blog
개발은 처음이라
공부할게 너무 많은 세상

# 각 행에 번호를 붙여서 출력
$ cat -b text.txt
     1	leefilll blog
     2	개발은 처음이라
     3	공부할게 너무 많은 세상
```

## head, tail 명령어

`head` 명령어는 파일의 앞 부분 10 행까지만 출력하고 `tail` 명령어는 파일의 마지막 10행을 출력한다.

```zsh
$ head text2.txt
leefilll blog
leefilll blog
leefilll blog
leefilll blog
leefilll blog
leefilll blog
leefilll blog
leefilll blog
leefilll blog
leefilll blog

# 3행까지만 출력
$ head -n 3 text2.txt
leefilll blog
leefilll blog
leefilll blog
```

## grep 명령어

`grep` 명령어는 인자로 지정한 문자열을 포함한 일부 행만 추출할 때 사용한다. 물론 인자로 `정규표현식`을 넣는 것도 가능하다.

```zsh
# text.txt 파일에서 '개발' 문자열을 포함한 행을 출력

$ cat -n text.txt | grep 개발
     2	개발은 처음이라
```

## cut 명령어

`cut` 명령어는 인자로 들어온 옵션에 따라 일부를 제거하거나 뽑아낼 때 사용한다.

```zsh
$ cat text3.txt
leefilll blog 개발은 처음이라 github pages

# -c 옵션으로 글자 인덱스 지정, 콤마나 하이픈을 이용하여 범위를 지정할 수도 있다
# 이때 인덱스는 1부터 시작
$ cat text3.txt| cut -c 1
l
$ cat text3.txt| cut -c 4
f
$ cat text3.txt| cut -c 1-8 # 8번째까지
leefilll
$ cat text3.txt| cut -c 1,10 # 1번째, 10번째 문자 - 공백이 있으면 안됨
lb

# -d 옵션으로 구분자 지정하고 -f 옵션으로 열의 번호 지정
$ cat text3.txt | cut -d ' ' -f 4
처음이라
```

## sed 명령어

`sed` 명령어는 특정 조건으로 필터링 하거나 제거할 때 사용한다. 원본 파일은 변현하지 않고 출력되는 결과를 변화시켜서 보여주는 역할을 한다. 인자로 `정규표현식`을 사용할 수도 있다.

- `d(delete)` 옵션은 행을 삭제하여 출력할 때 사용한다.

```zsh
$ cat text4.txt
leefilll blog
개발은 처음이라
공부할게 너무 많은 세상

# d 옵션 - 행 삭제
$ sed '2d' text4.txt
leefilll blog
공부할게 너무 많은 세상

# 마지막 행은 $를 이용
$ sed '$d' text4.txt
leefilll blog
개발은 처음이라

$ sed '/개발/d' text4.txt
leefilll blog
공부할게 너무 많은 세상
```

- `p(print)` 옵션은 행을 출력할 때 사용한다.

```zsh
# -n 옵션은 기본 출력을 생략한다
# 이 옵션을 주지 않으면 기본 출력이 나오고 해당 행이 다시 한번 출력 된다.
$ sed -n '3p' text4.txt
공부할게 너무 많은 세상

# 2번째 행부터 3번째 행까지 출력
$ sed -n '2,3p' text4.txt
개발은 처음이라
공부할게 너무 많은 세상
```

- `s(substitution)` 옵션은 문자열을 치환할 때 사용한다. `'s/{치환하고 싶은 문자 또는 정규표현식}/{치환할 문자}/{추가 옵션}'` 형태로 전달하여 사용한다. 추가 옵션으로 `g`를 넣게 되면 한 줄에 해당 정규표현식이 여러번 나올 경우 모두 치환한다.

```zsh
# 개발은 -> 블로그는 으로 치환
$ sed 's/개발은/블로그는/' text4.txt
leefilll blog
블로그는 처음이라
공부할게 너무 많은 세상
```

<br/>

# 마치며 💬

`cat`과 `echo`는 자주 사용했는데, 나머지 명령어들은 이번 포스팅을 통해 공부를 많이 했다. 코드 예시를 넣기 위해서 직접 해보기도 하고, 예시 파일도 만들면서 조금씩 적응이 되어가고 있는 듯 하다. 다음 포스팅으로는 `정규표현식`에 대하여 글을 써볼 예정이다. 자주 사용하지 않았어서 많이 낯설기도 하고 자세하게 공부하고 싶기도 하다.

아직까지는 글 하나를 쓸 때마다 생각보다 많은 시간을 잡아 먹는다. 조금씩 나아지겠지...?

<br/>

---

# References

- [유닉스][unix_wiki]
- [유닉스 계열][unix_like_wiki]
- [리눅스 sed 명령어 - 밍곰님의 블로그][ming_blog]

[unix_wiki]: https://ko.wikipedia.org/wiki/%EC%9C%A0%EB%8B%89%EC%8A%A4
[unix_like_wiki]: https://ko.wikipedia.org/wiki/%EC%9C%A0%EB%8B%89%EC%8A%A4_%EA%B3%84%EC%97%B4
[ming_blog]: https://m.blog.naver.com/PostView.nhn?blogId=minki0127&logNo=220677180665&proxyReferer=https%3A%2F%2Fwww.google.com%2F
