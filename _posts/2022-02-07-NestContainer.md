---
layout: post
title: Docker로 Dependency Free한 Nest개발환경 구축하기
subtitle : Nest.js개발환경을 갖춘 Container 만들기
tags: [Docker]
author: Jinwoo Kang
comments : True
---

Local에서 여러 프로젝트를 하다보면 디펜던시가 꼬일 때가 있다. 이 꼬인 디펜던시를 해결하기위해 이것저것 삭제하고 재설치하다보면, 다른 작업도 망가질 가능성이 매우 높다.

이를 방지하기 위하여 내 local에 개발환경을 구축하는 것보다는 `Docker`를 사용하여 Container에 개발환경을 구축해봤습니다.

<br>

이 방법이 완벽한 Dependency Free는 아닌 것으로 알고 있습니다.

Dependency가 꼬이지 않기 위한 최소한의 예방이라고 생각합니다.

이 포스팅은 Nest.js를 위한 개발환경을 다뤘습니다.

<br>

<h2>1. Dockerhub에서 node이미지를 받는다.</h2>
컨테이너 이름은 nest-practice로 지었다.
{% highlight shell %}
docker run -it -d -p 127.0.0.1:4010:4010 --name nest-practice node
{% endhighlight %}
4010번대의 포트를 열었는 데, 포트를 열지 않으면 vscode로 접근이 불가능하다.

<br>

<h2>2. node 컨테이너에 접속한다.</h2>
{% highlight shell %}
docker exec -it nest-practice bash
{% endhighlight %}

<br>

<h2>3. node 컨테이너를 업데이트한다(필수)</h2>
업데이트를 하지 않으면 zsh를 다운 받지 못한다. 그 외 초기버전에서 나타나는 여러 오류가 생길 수 있다.
{% highlight shell %}
apt update
{% endhighlight %}

<br>

<h2>4. zsh 다운로드</h2>
{% highlight shell %}
apt-get install zsh
{% endhighlight %}

<br>

<h2>5. oh-my-zsh 다운로드</h2>
{% highlight shell %}
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
{% endhighlight %}

<br>

<h2>6. vim 다운로드</h2>
{% highlight shell %}
apt-get install vim
{% endhighlight %}

<br>

<h2>7. debian일 경우, 한글 다운로드</h2>
debian은 특별한 설정없으면 한글이 깨진다. alpine은 안깨졌었다.
<h6>STEP1: 지역 언어를 설치한다</h6>
{% highlight shell %}
docker run -it -d -p 127.0.0.1:4000:4000 --name nest-practice node
{% endhighlight %}

<h6>STEP2: ~/.bashrc에 language 환경변수 추가</h6>
{% highlight shell %}
docker run -it -d -p 127.0.0.1:4000:4000 --name nest-practice node
{% endhighlight %}

<h6>STEP3: locales 업데이트(글쓴 당시에는 297번)</h6>
{% highlight shell %}
locale-gen ko_KR ko_KR.UTF-8
update-locale LANG=ko_KR.UTF-8
dpkg-reconfigure locales
{% endhighlight %}
그 후, 디패키징을 하면서 한글을 선택한다. 탐색 방법은 enter이며, 한글 인덱스를 기억했다가 enter를 누르다보면 원하는 패키지 인덱스를 입력하라고 한다. 그 때 한글 인덱스를 입력하고 UTF-8(3번)을 선택한다.

<h6>STEP4: vim 업데이트, ~/.vimrc에 들어가서 다음을 추가한다.</h6>
{% highlight shell %}
set encoding=utf-8
set fileencodings=utf-8,cp949
{% endhighlight %}

<h6>STEP5: ~/.zshrc도 설정해준다.</h6>
{% highlight shell %}
# You may need to manually set your language environment
export LANG=ko_KR.UTF-8
{% endhighlight %}

<h6>STEP6: 터미널을 새로켜서 vim, bash, zsh에 한글이 잘 적용되는 지 확인한다.</h6>
한글이 깨져 보인다면 vimrc, bashrc, zshrc 설정에 오타가 있는 지 확인하면 좋을 것같습니다.

<br>

<h2>8. VScode에서 extension의 Remote Development 환경 설치한다</h2>
extension tab의 모니터 모양의 아이콘을 클릭해서 컨테이너에 접속한다. 접속 성공하면 nest를 설치해서 문제 없는 지 확인한다.
<br>
