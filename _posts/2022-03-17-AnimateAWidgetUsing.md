---
layout: post
title: Animate a widget using a physics simulation
subtitle : 그냥 번역만 해봤습니다.
tags: [flutter, 번역]
author: Jinwoo Kang
comments : True
---

[원본 공식 문서](https://docs.flutter.dev/cookbook/animation/physics-simulation)

<h2>1. Set up an animation controller</h2>
<h2>2. Move the widget using gestures</h2>
<h2>3. Animate the widget</h2>
<h2>4. Calculate the velocity to simulate a springing motion</h2>

이 페이지를 번역하면서 공부하려고 했는 데, 실제로 코드를 작성해보고 실행이 안되었다. 워닝과 에러가 너무 많았다. `DraggableCard` 클래스의 `this.child` 에 어떠한 값도 입력이 되지 않았고, `_DraggableCardState` 클래스의 `_controller` 와 `_animation` 가 초기화되지 않았다. 그냥 번역하면서 공부한 것으로 만족해야겠다.

다음부터는 공부하기 전에 미리 full code를 실행해보고 동작하면 공부해봐야겠다.
<br>
<br>
<h1>1. Set up an animation controller</h1>

먼저, Stateful Widget인 DraggableCard을 작성하고 시작한다.

{% highlight dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(home: PhysicsCardDragDemo()));
}

class PhysicsCardDragDemo extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: DraggableCard(
        child: FlutterLogo(
          size: 128,
	        )
      ),
    );
  }
}

class DraggableCard extends StatefulWidget {
  final Widget child;
  DraggableCard({ this.child }); // <-- 오류난다.

  @override
  _DraggableCardState createState() => _DraggableCardState();
}

class _DraggableCardState extends State<DraggableCard> {
  @override
  void initState() {
    super.initState();
  }
  @override
  void dispose() {
    super.dispose();
  }
  @override
  Widget build(BuildContext context) {
    return Align(
      child: Card(
        child: widget.child,
      ),
    );
  }
}
{% endhighlight %}

큰 틀인 PhysicsCardDragDemo는 바뀌지 않아도 되기 때문에 StatelessWidget을 상속받는 것같아 보인다.

- DraggableCardState 클래스가 [SingleTickerProviderStateMixin](https://api.flutter.dev/flutter/widgets/SingleTickerProviderStateMixin-mixin.html)로부터 상속받도록 해라,  그러면 위젯을 껐다/켰다 가능하게하는 Ticker를 사용할 수 있게 된다.

    SingleTickerProviderStateMixin으로부터 상속받으려면 다음과 같이 State<T>를 사용하여 상속을 받으면된다. [TickerMode class](https://api.flutter.dev/flutter/widgets/TickerMode-class.html).

    참 신기하게 만들었다. State<T인자>에 넣으면 SingleTikcerProviderStateMixin으로부터 상속받게했다.

- _[AnimationController](https://api.flutter.dev/flutter/animation/AnimationController-class.html) 생성자를 다음과 같이 initState, vsync 설정을 해라.
    - initState로 AnimationController를 객체화
    - vsync는 애니메이션 최적화 및 언제 재생할 지 시간을 세어주는 등의 기능을 위해 필요하다. 여기서 this로 되어있는 것은 레퍼런스가 존재하는 객체가 vsync역할을 해준다는 의미이다.

    [여기가 정말 잘 설명되어있다](https://papabee.tistory.com/140)

    {% highlight dart %}
    @override
      void initState() {
        _controller = //<-- AnimationController의 instance
            AnimationController(vsync: this, duration: Duration(seconds: 1));
        super.initState();
      }
    {% endhighlight %}

_DraggableCardState 클래스에 다음을 추가한다.

- with SingleTickerProviderStateMixin

{% highlight dart %}
class _DraggableCardState extends State<DraggableCard>
    with SingleTickerProviderStateMixin {
  AnimationController _controller;

  @override
  void initState() {
    _controller =
        AnimationController(vsync: this, duration: Duration(seconds: 1));
    super.initState();
  }


  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
  //...
{% endhighlight %}

`_controller` 부분은 생성자인 것같고, `_controller.dispose();` 는 소멸자 부분인 것같다.
<br>
<br>
<h1>2. Move the widget using gestures</h1>

위젯을 드래그하면 움직이도록 만들어보자. 그리고 _DraggableCardState 클래스에 Alignment field를 적용해볼 것이다.

`Alignment _dragAlignment = Alignment.center;`

GestureDetector를 추가하여 `onPanDown`, `onPanUpdate` , `onPanEnd` 콜백을 조정하도록 할 것이다. 이 alignment를 조정하기 위해서는 화면의 사이즈를 가져오는 MediaQuery가 위젯의 사이즈를 얻을 수 있어야하고 다음 코드에 나와있는 것처럼 그 사이즈를 2로 나누어야한다. 그러고나서, Align 위젯의 alignment를 _dragAlignment로 세팅한다.

{% highlight dart %}
@override
Widget build(BuildContext context) {
  var size = MediaQuery.of(context).size;
  return GestureDetector(
    onPanDown: (details) {},
    onPanUpdate: (details) {
      setState(() {
        _dragAlignment += Alignment(
          details.delta.dx / (size.width / 2),
          details.delta.dy / (size.height / 2),
        );
      });
    },
    onPanEnd: (details) {},
    child: Align(
      child: Card(
        child: widget.child,
      ),
    ),
  );
}
{% endhighlight %}
<br>
<br>
<h1>3. Animate the widget</h1>

위젯이 만들어졌다면 가운데로 정렬이 되어야한다.

Animation<Alignment>와 _runAnimation 메서드를 추가한다. 이 메서드는 위젯이 드래그된 지점과 가운데 지점 사이를 보간하는 Tween(선형 보간법)을 정의합니다. → 말이 좀 어렵다.

왜 가운데 지점과 드래그 지점의 보간을 하는 지 잘모르겠다.

{% highlight dart %}
Animation<Alignment> _animation;

  void _runAnimation() {
    _animation = _controller.drive(
      AlignmentTween(
        begin: _dragAlignment,
        end: Alignment.center,
      ),
    );
   _controller.reset();
   _controller.forward();
  }
{% endhighlight %}

다음은, AnimationController가 value를 생산할 때 _dragAlignment를 업데이트한다.

{% highlight dart %}
@override
void initState() {
  super.initState();
  _controller = AnimationController(vsync: this, duration: Duration(seconds: 1));
  _controller.addListener(() {
    setState(() {
      _dragAlignment = _animation.value;
    });
  });
}
{% endhighlight %}

다음은, Align 위젯이 _dragAlignment를 사용하도록 하는 것이다.

{% highlight dart %}
child: Align(
  alignment: _dragAlignment,
  child: Card(
    child: widget.child,
  ),
),
{% endhighlight %}

마지막으로 GestureDetector를 업데이트하여 animation controller를 관리할 수 있도록 한다.

{% highlight dart %}
onPanDown: (details) {
 _controller.stop();
},
onPanUpdate: (details) {
 setState(() {
   _dragAlignment += Alignment(
     details.delta.dx / (size.width / 2),
     details.delta.dy / (size.height / 2),
   );
 });
},
onPanEnd: (details) {
 _runAnimation();
},
{% endhighlight %}
<br>
<br>
<h1>4. Calculate the velocity to simulate a springing motion</h1>

마지막 단계는 약간의 수학을 사용해서 드래그된 위젯의 속도를 계산하여 현실감있게 움직이도록 하는 것이다.

첫째로 physics package를 import한다.

`import 'package:flutter/physics.dart;`

onPanEnd 콜백은 DragEndDetails라는 객체를 제공한다. 이 객체는 스크린에 입력이 멈추면 포인터의 속도를 계산하여 그 값을 넘겨준다. 이 속도는 pixel/second 로 계산되며 Align위젯은 픽셀을 사용하지 않는다. 이 좌표값은 [-1.0, -1.0]과 [1.0, 1.0]의 값이며, [0.0, 0.0]값은 가운데를 의미합니다. **STEP2**에서 계산된 size는 픽셀에서 좌표값으로 변환된다.

마지막으로 AnimationController는 SpringSimulation에 주어지는 animateWith() 메서드를 가지게 된다.

{% highlight dart %}
void _runAnimation(Offset pixelsPerSecond, Size size) {
  _animation = _controller.drive(
    AlignmentTween(
      begin: _dragAlignment,
      end: Alignment.center,
    ),
  );
  // Calculate the velocity relative to the unit interval, [0,1],
  // used by the animation controller.
  final unitsPerSecondX = pixelsPerSecond.dx / size.width;
  final unitsPerSecondY = pixelsPerSecond.dy / size.height;
  final unitsPerSecond = Offset(unitsPerSecondX, unitsPerSecondY);
  final unitVelocity = unitsPerSecond.distance;

  const spring = SpringDescription(
    mass: 30,
    stiffness: 1,
    damping: 1,
  );

  final simulation = SpringSimulation(spring, 0, 1, -unitVelocity);

  _controller.animateWith(simulation);
}
{% endhighlight %}

_runAnimation()을 속도와 사이즈와 함께 불러오는 것을 잊지마라.

{% highlight dart %}
onPanEnd: (details) {
  _runAnimation(details.velocity.pixelsPerSecond, size);
},
{% endhighlight %}
<br>
<br>
<h1>5. 전체 코드</h1>

{% highlight dart %}
import 'package:flutter/material.dart';
import 'package:flutter/physics.dart';

main() {
  runApp(MaterialApp(home: PhysicsCardDragDemo()));
}

class PhysicsCardDragDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: DraggableCard(
        child: FlutterLogo(
          size: 128,
        ),
      ),
    );
  }
}

/// A draggable card that moves back to [Alignment.center] when it's
/// released.
class DraggableCard extends StatefulWidget {
  final Widget child;
  DraggableCard({this.child});

  @override
  _DraggableCardState createState() => _DraggableCardState();
}

class _DraggableCardState extends State<DraggableCard>
    with SingleTickerProviderStateMixin {
  AnimationController _controller;

  /// The alignment of the card as it is dragged or being animated.
  ///
  /// While the card is being dragged, this value is set to the values computed
  /// in the GestureDetector onPanUpdate callback. If the animation is running,
  /// this value is set to the value of the [_animation].
  Alignment _dragAlignment = Alignment.center;

  Animation<Alignment> _animation;

  /// Calculates and runs a [SpringSimulation].
  void _runAnimation(Offset pixelsPerSecond, Size size) {
    _animation = _controller.drive(
      AlignmentTween(
        begin: _dragAlignment,
        end: Alignment.center,
      ),
    );
    // Calculate the velocity relative to the unit interval, [0,1],
    // used by the animation controller.
    final unitsPerSecondX = pixelsPerSecond.dx / size.width;
    final unitsPerSecondY = pixelsPerSecond.dy / size.height;
    final unitsPerSecond = Offset(unitsPerSecondX, unitsPerSecondY);
    final unitVelocity = unitsPerSecond.distance;

    const spring = SpringDescription(
      mass: 30,
      stiffness: 1,
      damping: 1,
    );

    final simulation = SpringSimulation(spring, 0, 1, -unitVelocity);

    _controller.animateWith(simulation);
  }

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(vsync: this);

    _controller.addListener(() {
      setState(() {
        _dragAlignment = _animation.value;
      });
    });
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    final size = MediaQuery.of(context).size;
    return GestureDetector(
      onPanDown: (details) {
        _controller.stop();
      },
      onPanUpdate: (details) {
        setState(() {
          _dragAlignment += Alignment(
            details.delta.dx / (size.width / 2),
            details.delta.dy / (size.height / 2),
          );
        });
      },
      onPanEnd: (details) {
        _runAnimation(details.velocity.pixelsPerSecond, size);
      },
      child: Align(
        alignment: _dragAlignment,
        child: Card(
          child: widget.child,
        ),
      ),
    );
  }
}
{% endhighlight %}
