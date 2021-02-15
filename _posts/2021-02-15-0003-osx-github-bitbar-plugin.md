---
title: "OS X에서 Github 1일 1커밋 도와주는 플러그인 설정하기"
permalink: /github/osx-github-bitbar-plugin
excerpt: "OS X에서 github의 1일1커밋 목표를 도와주는 플러그인 사용법."
last_modified_at: 2021-02-15
categories:
  - etc
  - osx
  - github
tags:
  - github
  - git
  - bitbar
author_profile: true
header:
  teaser: /assets/images/blog/2021/0003/teaser.jpg
---

![](/assets/images/blog/2021/0003/teaser.jpg)    


## :seedling: 1일1커밋
개발자라면 누구나 [`github`](https://github.com/godngu)에 잔디 심는 목표를 가져 봤을 겁니다.. ~~(물론 전 최근에 목표를 세웠습니다 ^^;;)~~
하지만 매일 브라우저를 통해 github에 들어가 잔디를 확인 하는건 여간 귀찮은 작업이 아닐 수 없습니다.
이 때 [`BitBar`](https://getbitbar.com/)를 사용하면 데스크탑에서 Github 잔디 상태를 손쉽게 확인 할 수 있습니다. :sparkles:  


## :hammer: BitBar 설치

- [`BitBar`](https://getbitbar.com/)
- [`BitBar Github`](https://github.com/matryer/bitbar#get-started)

### homebrew 설치  
> 만약 설치시 `Unknown command` 오류가 발생한다면 다음 명령어로 설치 해보세요.

```bash
$ brew cask install bitbar

Error: Unknown command: cask
%


# 오류 발생시 다음 명령어로 설치 하세요.
$ brew install --cask bitbar

==> Downloading https://github.com/matryer/bitbar/releases/download/v1.10.1/BitBar.app.zip
==> Downloading from https://github-releases.githubusercontent.com/14376285/31e70500-3ef7-11eb-8e62-7f1ddc233681?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Am
######################################################################## 100.0%
==> Installing Cask bitbar
==> Moving App 'BitBar.app' to '/Applications/BitBar.app'
🍺  bitbar was successfully installed!
%
```

## :file_folder: Github 플러그인 설치
### 플러그인 디렉터리 생성
```bash
$ mkdir ~/dev/workspace/private/blog/bitbar-plugins
```

### 플러그인 다운로드
아래 링크를 들어가면 수 많은 플러그인이 있습니다.
> https://github.com/matryer/bitbar-plugins

이 중 Github 플러그인을 다운로드 받아야 합니다.
> https://github.com/matryer/bitbar-plugins/blob/master/Dev/GitHub/github-contribution.10m.rb

![](/assets/images/blog/2021/0003/image03.png)

```bash
$ ~/dev/workspace/private/blog/bitbar-plugins/github-contribution.10m.rb
```

### Github 계정 세팅
```bash
# 설정파일 생성, 편집
$ touch ~/.bitbarrc
$ vim ~/.bitbarrc

# Github 계정정보 입력
[github_contribution]
username = <GITHUB_USERNAME>
max_contributions = 10
```


## :coffee: BitBar 설정
### 실행
- 마우스 우클릭 후 `열기`로 실행합니다.
![](/assets/images/blog/2021/0003/image01.png)

### 플러그인 디렉터리 변경
- 처음 설치시 :warning:표시로 나오는데 디렉터리를 변경하면 아이콘이 `BitBar`로 바뀝니다.

```bash
$ ~/dev/workspace/private/blog/bitbar-plugins
```
![](/assets/images/blog/2021/0003/image02.png){: width="400px"}{: .center}


## :smile: 완료
![](/assets/images/blog/2021/0003/image04.png){: width="400px"}{: .center}


## :fishing_pole_and_fish: 이모지 변경 팁
상태바에서 :poop: emoji를 :rage:로 변경 해보겠습니다.

> Link: [emoji 참고](https://www.webfx.com/tools/emoji-cheat-sheet/)

![](/assets/images/blog/2021/0003/image05.png){: width="500px"}{: .center}

```bash
$ vim github-contribution.10m.rb

...
def icon
  case count
  # emoji 변경
  # when 0    then ':poop:'
  when 0    then ':rage:'
  when 1..3 then ':seedling:'
  when 4..9 then ':herb:'
  else           ':deciduous_tree:'
  end
end
...
```


![](/assets/images/blog/2021/0003/image06.png){: width="400px"}{: .center}
