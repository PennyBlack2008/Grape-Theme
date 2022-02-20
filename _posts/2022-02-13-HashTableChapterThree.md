---
layout: post
title: 3. 해쉬 테이블로 가는 길 1
subtitle : 해쉬값이 너무 커버리면 생기는 문제가 있다.
tags: [HashTable]
author: Jinwoo Kang
comments : True
---

2번에서 해쉬 함수를 만들어 백의 자리수인 해쉬 값을 받았다. 해쉬 함수는 만들었지만, 해쉬 테이블을 만드려면 자료가 10개 들어가는 해쉬 테이블에 비해 해쉬 함수의 반환값의 인덱스의 값이 너무 크다. 예를 들어, 이름이 정말 긴 데이터가 들어오면 테이블이 그 해쉬값에 대응하는 인덱스를 포함하느라 테이블의 크기가 어마무시하게 커질 것이다.

그래서 테이블의 크기를 조정하기 위해 TABLE_SIZE의 나머지수로 해쉬 값을 얻어 인덱스의 크기를 제한할 것이다.

**코드**
{% highlight shell %}
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <stdint.h>
#include <stdbool.h>

#define MAX_NAME 256
#define TABLE_SIZE 10

typedef struct {
    char name[MAX_NAME];
    int age;
} person;

unsigned int hash(char* name) {
    int length = strnlen(name, MAX_NAME);
    unsigned int hash_value = 0;
    for (int i = 0; i < length; i++) {
        hash_value += name[i];
        hash_value = (hash_value * name[i]) % TABLE_SIZE;
    }
    return hash_value;
}

int main() {
    printf("Jacob => %u\n", hash("Jacob"));
    printf("Bobby => %u\n", hash("Bobby"));
    printf("Natali => %u\n", hash("Natali"));
    printf("Rone => %u\n", hash("Rone"));
    printf("Railie => %u\n", hash("Railie"));
    printf("Tyler => %u\n", hash("Tyler"));
    return 0;
}
{% endhighlight %}


**터미널**
{% highlight shell %}
Jacob => 2
Bobby => 5
Natali => 5
Rone => 1
Railie => 6
Tyler => 0
{% endhighlight %}
