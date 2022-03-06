---
layout: post
title: 30% 이해한 Priority Queue
subtitle : Max Heap으로 Priority Queue 구현
tags: [Heap, Queue]
author: Jinwoo Kang
comments : True
---

[Cplusplus.com](https://www.cplusplus.com/reference/queue/)에 들어가면 **queue**헤더에 queue, priority_queue가 함께 들어있다. [소스코드](https://github.com/llvm/llvm-project/blob/2e2f3158c604adb8401a2a44a03f58d4b6f1c7f9/libcxx/include/queue)로 들어가도 queue파일 안에 두 자료구조가 들어간 것을 알 수 있다.

Priority Queue는 queue에서 3가지 특징이 추가된 것이다.

<h6>1. 모든 item은 priority가 있다.</h6>
<h6>2. 높은 priority의 요소는 낮은 priority의 요소보다 먼저 pop된다.</h6>
<h6>3. 만약 2개의 요소들이 동등한 priority를 갖게 된다면, 그 2개의 요소는 먼저 들어온 순서가 우선순위가 된다.</h6>

[GeeksForGeeks](https://www.geeksforgeeks.org/priority-queue-using-binary-heap/)가 가장 이해가 잘되서 여기 나와있는 코드로 공부했다.

이 코드를 30%정도 이해한 것같다. 각 코드의 의미는 알겠지만, 그 코드를 이렇게 작성할 수 밖에 없었던 이유를 말하라고 하면 정말 모르겠다. 만약에 이 코드를 응용해서 어떤 코드를 작성하라고 하면 꽤 고생할 것같다.

여기서 이해안되는 건 `shiftDown`함수이다.

GeeksForGeeks에 나와 있는 전체 코드는 이렇다. 내가 이해안되는 것은 밑의 코드 블럭에 따로 적었고 계속 이해하려고 노력 중이다.

<br>
**전체 코드**
{% highlight cpp %}
// https://www.geeksforgeeks.org/priority-queue-using-binary-heap/
#include <iostream>
#include <utility>
using namespace std;

int H[50];
int _size = -1;

int parent(int i)
{
    return (i - 1) / 2;
}

int leftchild(int i)
{
    return ((2 * i) + 1);
}

int rightchild(int i)
{
    return ((2 * i) + 2);
}

void shiftUp(int i)
{
    while (i > 0 && H[parent(i)] < H[i])
    {
        swap(H[parent(i)], H[i]);
        i = parent(i);
    }
}

// pop되었을 때 사용된다.
// Pop되었을 경우 leaf노드의 값을 가진 인덱스 0이 들어간다.
void shiftDown(int i)
{
    int maxIndex = i;

    // Left child
    int l = leftchild(i); // 1

    if (l <= _size && H[l] > H[maxIndex]) {
        maxIndex = l; // 1로 교체
    }

    // Right child
    int r = rightchild(i); // 2

    if (r <= _size && H[r] > H[maxIndex]) {
        maxIndex = r; // 2
    }

    if (i != maxIndex) {
        swap(H[i], H[maxIndex]); // 0, 2
        shiftDown(maxIndex); // 2
    }
}

void insert(int p)
{
    _size = _size + 1;
    H[_size] = p;
    shiftUp(_size);
}

int extractMax()
{
    // pop
    int result = H[0];
    _size--;

    // 빈 root자리를 채우기 위한 shift down작업
    // leaf노드부터 시작한다. leaf노드를 root로 올린 다음 밑으로 내리는 식으로 작동
    H[0] = H[_size];
    shiftDown(0);
    return result;
}

void changePriority(int i, int p)
{
    int oldp = H[i];
    H[i] = p;

    if (p > oldp)
    {
        shiftUp(i);
    }
    else {
        shiftDown(i);
    }
}

int getMax()
{
    return H[0];
}

void remove(int i)
{
    H[i] = getMax() + 1;
    shiftUp(i);
    extractMax();
}

void representQueue()
{
    int i = 0;
    while (i <= _size) {
        cout << H[i] << " ";
        i++;
    }
    cout << "\n";
}

int main()
{
    insert(45);
    insert(20);
    insert(14);
    insert(12);
    insert(31);
    insert(7);
    insert(11);
    insert(13);
    insert(7);


    cout << "Priority Queue: ";
    representQueue();

		// 최상위 root pop
    cout << "Node with maximum priority: " << extractMax() << "\n";

    changePriority(2, 49);
    cout << "Priority queue after " << "priority change: ";
    representQueue();

    remove(3);
    cout << "Priority queue qfter " << "removing the element: ";
    representQueue();

    return (0);
}
{% endhighlight %}

<br>
**이해안되는 shiftDown 코드**

{% highlight cpp %}
void shiftDown(int i)
{
    int maxIndex = i;

    // Left child
    int l = leftchild(i);

    if (l <= _size && H[l] > H[maxIndex]) {
        maxIndex = l;
    }

    // Right child
    int r = rightchild(i);

    if (r <= _size && H[r] > H[maxIndex]) {
        maxIndex = r;
    }

    if (i != maxIndex) {
        swap(H[i], H[maxIndex]);
        shiftDown(maxIndex);
    }
}
{% endhighlight %}

이 부분의 코드가 정말 이해 안된다. 예시를 들면 어떻게 작동하는 지는 알겠지만, 왜 이렇게 코드를 작했는 지는 이해가 안된다. `extractMax()` 를 했을 때의 예시를 들어보자.

밑의 코드에 주석이 달아놨는 데, 우선 가장 큰 H[0]노드가 pop되어 나가고, 가장 작은 값을 가진 leaf노드를 root노드에 올려놓는다. 그리고나서 shiftDown을 수행한다. shiftDown는 가장 큰 값이 있어야할 0번 인덱스에 가장 작은 값이 들어간 상황을 해결해줘야한다.

<br>
**extractMax 코드**
{% highlight cpp %}
int extractMax()
{
    // pop
    int result = H[0];
    _size--;

    // 빈 root자리를 채우기 위한 shift down작업
    // leaf노드부터 시작한다. leaf노드를 root로 올린 다음 밑으로 내리는 식으로 작동
    H[0] = H[_size];
    shiftDown(0);
    return result;
}
{% endhighlight %}

자 이제 상황이 이해되면 **shiftDown코드**를 볼 때가 됐다.
아시다시피 `shiftDown(0)` 이 수행된다.

<br>
**shiftDown 코드**(트리가 충분히 컸을 때를 가정한 시나리오)
{% highlight cpp %}
void shiftDown(int i)
{
    int maxIndex = i; // maxIndex는 0

    // Left child
    int l = leftchild(i); // l은 1

    if (l <= _size && H[l] > H[maxIndex]) // 1은 size보다 작을 것이다.
		{                                     // H[1]은 H[0]보다 클 것이다.
        maxIndex = l;                     // 이제 maxIndex는 1이 된다.
    }

    // Right child
    int r = rightchild(i); // r은 2

    if (r <= _size && H[r] > H[maxIndex]) // 2은 size보다 작을 것이다.
		{                                     // H[2]은 H[0]보다 클 것이다.
        maxIndex = r;                     // 이제 maxIndex는 2이 된다.
    }

    if (i != maxIndex) // 0이 2와 다르다면
		{
        swap(H[i], H[maxIndex]); // H[0]과 H[2]를 스왑한다.
        shiftDown(maxIndex); // 새로운 값인 2를 shiftDown한다.
    }
}
{% endhighlight %}

이제 H[0]의 자리는 루트 노드의 오른쪽 노드의 값이 차지하게 되고 H[0]에 담겼던 값은 H[2]로 스왑된다. 이제 루트노드에 있었던 값은 오른쪽 노드로 내려온 뒤 다시 shiftDown을 통해 다시 내려간다.

이렇게 재귀함수가 충분히 돌고 나면 `if (i != maxIndex)` 조건에 걸리게 되어 재귀가 멈춘다. i와 maxIndex가 같은 값일 때는 왼쪽, 오른쪽 자식이 모두 없을 때의 조건일 것이다. 즉 leaf node가 다시 leaf node로 간다.

지금까지 내가 이해한 바를 정리했다. 그런데, 한참 모자르다.

내가 이해한 것이 한참 모자른 이유

1. 재귀가 아닌 [다른 것으로 구현한 로직](https://suyeon96.tistory.com/31)을 이해하지 못한다.
2. 만약 이해했다면 코드를 읽다보면 앞으로 어떻게 코드가 전개될지 눈에 보여야되는 데, 그렇지 못하다.
3. extractMax함수로 최상위 노드가 pop된 후에 leaf노드가 곧바로 최상위로 올라가는 것은 확실한 방법이지만 비효율적이다. 그런데, 최적화할 묘수가 생각이 안난다.

아직도 너무 실력이 모자라서 한숨이 나온다. 다른 사람들은 직접 구현한 것을 블로그 포스팅하는 데, 나는 아직까지도 트리 구현을 바로바로 못한다.