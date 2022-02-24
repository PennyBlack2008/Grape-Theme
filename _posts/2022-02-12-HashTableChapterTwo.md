---
layout: post
title: 2. 아주 간단한 해쉬함수
subtitle : 가장 간단한 해쉬 함수 구현
tags: [HashTable]
author: Jinwoo Kang
comments : True
---

1에서 만들었던 무조건 5를 반환하는 hash함수를 업그레이드 시킬 것이다. 이 hash함수는 이름을 받아 해쉬값을 반환한다. 문자열을 받아 가장 간단한 해쉬 로직을 만드는 방법은 argument로 들어온 문자열의 각 문자의 ascii값을 더해 값을 반환하는 것이다.

**코드**
{% highlight c %}
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
Jacob => 479
Bobby => 494
Natali => 601
Rone => 404
Railie => 598
Tyler => 528
{% endhighlight %}