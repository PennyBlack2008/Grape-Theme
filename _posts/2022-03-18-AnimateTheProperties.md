---
layout: post
title: Animate the properties of a container
subtitle : 플러터 에니메이션 기초 튜토리얼
tags: [flutter, 번역]
author: Jinwoo Kang
comments : True
---
[공식 문서](https://flutter-ko.dev/docs/cookbook/animation/animated-container)

<h2>1. 기본 디폴트 StatefulWidget 만들기</h2>
<h2>2. 약간의 프로퍼티 조정 후 AnimatedContainer 만들기</h2>
<h2>3. 새로운 프로퍼티로 리빌딩된 애니메이션을 만들기</h2>
<h2>4. 전체 코드</h2>

Container 클래스는 위젯을 만드는 데 편리한 방법들을 제공합니다: width, height, backgroundColor, padding, borders, 등등

작업을 진행하면서 애니메이션들의 프로퍼티를 여러번 변경하는 경우가 많습니다. 예를 들어, user가 어떤 위젯을 가리키거나 선택하면 해당 위젯의 backgroundColor를 회색에서 초록색으로 바뀌도록 구현해야한다고 가정해봅시다.

프로퍼티들이 바뀌기 위해서, Flutter는 AnimatedContainer 위젯을 제공합니다. Container 위젯과 동일하게, AnimatedContainer는 width, height, backgroundColor등등을 결정할 수 있게 도와줍니다. 하지만, AnimatedContainer가 새로운 프로퍼티를 다시 리빌딩하게 되면, 자동으로 구버전의 값이 새로운 값으로 바뀝니다. Flutter에서는 이러한 애니메이션의 타입들을 묵시적 애니메이션(implicit animations)이라고 합니다.

이번 글에서는 어떻게 하면 AnimatedContainer가 size, backgroundColor, border radius등을 사용하여 user가 버튼을 눌렀을 때의 애니메이션을 보여주는 지 알려줍니다. 이 과정은 다음의 STEP을 따릅니다.

<h4>1. 기본 디폴트 StatefulWidget 만들기</h4>
<h4>2. 약간의 프로퍼티 조정 후 AnimatedContainer 만들기</h4>
<h4>3. 새로운 프로퍼티로 리빌딩된 애니메이션을 만들기</h4>
<br>
<br>
<h1> 1. 기본 디폴트 StatefulWidget 만들기 </h1>

시작하기 앞서 StatefulWidget과 State 클래스를 만듭니다. 커스텀 State 클래스를 사용하여 프로퍼티들을 계속 바꿔봅시다. 이 예제에서 width, height, color, border radius 프로퍼티들을 활용해 볼 것입니다.

이 프로퍼티들은 custom State 클래스에 속합니다. user가 버튼을 눌렀을 때 State 클래스가 업데이트될 것입니다. 그 말은 즉, 그 안에 들어있는 width, height, color, border radius 프로퍼티들이 업데이트 된다는 뜻입니다.

{% highlight dart %}
class AnimatedContainerApp extends StatefulWidget {
  @override
  _AnimatedContainerAppState createState() => _AnimatedContainerAppState();
}

class _AnimatedContainerAppState extends State<AnimatedContainerApp> {
  // Define the various properties with default values. Update these properties
  // when the user taps a FloatingActionButton.
  double _width = 50;
  double _height = 50;
  Color _color = Colors.green;
  BorderRadiusGeometry _borderRadius = BorderRadius.circular(8);

  @override
  Widget build(BuildContext context) {
    // Fill this out in the next steps.
  }
}
{% endhighlight %}

<br>
<br>
<h1>2. 약간의 프로퍼티 조정 후 AnimatedContainer 만들기</h1>
다음은, 프로퍼티들을 사용하여 AnimatedContainer를 만들 것입니다. 또한, duration도 넣어서 애니메이션의 재생 길이도 정의해줄 것입니다.
{% highlight dart %}
AnimatedContainer(
  // Use the properties stored in the State class.
  width: _width,
  height: _height,
  decoration: BoxDecoration(
    color: _color,
    borderRadius: _borderRadius,
  ),
  // Define how long the animation should take.
  duration: Duration(seconds: 1),
  // Provide an optional curve to make the animation feel smoother.
  curve: Curves.fastOutSlowIn,
);
{% endhighlight %}

<br>
<br>
<h1>3. 새로운 프로퍼티로 리빌딩된 애니메이션을 만들기</h1>

여기까지 왔으면 새로운 프로퍼티로 바뀐 AnimatedContainer의 애니메이션을 시작해봅시다. 어떻게 바뀐 프로퍼티를 적용할 수 있을 까요? 그러려면 `setState()` 메서드가 필요합니다.

app에 button을 추가합니다. user가 버튼을 누르면 setState()코드 안에서 프로퍼티들이 바뀐값을 적용할 수 있도록 합니다.

이제 새로운 app의 프로퍼티가 가지고 있는 값이 바뀌게 될 것입니다. 예를 들어, 이 app은 user가 버튼을 눌렀을 때마다 새로운 값을 생성할 것입니다.

{% highlight dart %}
FloatingActionButton(
  child: Icon(Icons.play_arrow),
  // When the user taps the button
  onPressed: () {
    // Use setState to rebuild the widget with new values.
    setState(() {
      // Create a random number generator.
      final random = Random();

      // Generate a random width and height.
      _width = random.nextInt(300).toDouble();
      _height = random.nextInt(300).toDouble();

      // Generate a random color.
      _color = Color.fromRGBO(
        random.nextInt(256),
        random.nextInt(256),
        random.nextInt(256),
        1,
      );

      // Generate a random border radius.
      _borderRadius =
          BorderRadius.circular(random.nextInt(100).toDouble());
    });
  },
);
{% endhighlight %}

<br>
<br>
<h1>4. 전체 코드</h1>
{% highlight dart %}
import 'dart:math';

import 'package:flutter/material.dart';

void main() => runApp(AnimatedContainerApp());

class AnimatedContainerApp extends StatefulWidget {
  @override
  _AnimatedContainerAppState createState() => _AnimatedContainerAppState();
}

class _AnimatedContainerAppState extends State<AnimatedContainerApp> {
  // Define the various properties with default values. Update these properties
  // when the user taps a FloatingActionButton.
  double _width = 50;
  double _height = 50;
  Color _color = Colors.green;
  BorderRadiusGeometry _borderRadius = BorderRadius.circular(8);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('AnimatedContainer Demo'),
        ),
        body: Center(
          child: AnimatedContainer(
            // Use the properties stored in the State class.
            width: _width,
            height: _height,
            decoration: BoxDecoration(
              color: _color,
              borderRadius: _borderRadius,
            ),
            // Define how long the animation should take.
            duration: Duration(seconds: 1),
            // Provide an optional curve to make the animation feel smoother.
            curve: Curves.fastOutSlowIn,
          ),
        ),
        floatingActionButton: FloatingActionButton(
          child: Icon(Icons.play_arrow),
          // When the user taps the button
          onPressed: () {
            // Use setState to rebuild the widget with new values.
            setState(() {
              // Create a random number generator.
              final random = Random();

              // Generate a random width and height.
              _width = random.nextInt(300).toDouble();
              _height = random.nextInt(300).toDouble();

              // Generate a random color.
              _color = Color.fromRGBO(
                random.nextInt(256),
                random.nextInt(256),
                random.nextInt(256),
                1,
              );

              // Generate a random border radius.
              _borderRadius =
                  BorderRadius.circular(random.nextInt(100).toDouble());
            });
          },
        ),
      ),
    );
  }
}
{% endhighlight %}

![전체코드동작](https://flutter-ko.dev/images/cookbook/animated-container.gif)