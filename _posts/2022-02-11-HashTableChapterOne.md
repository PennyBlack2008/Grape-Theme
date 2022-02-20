---
layout: post
title: 1. 간단히 구조만 잡기
subtitle : 아주 간단히 프로그램 구조만
tags: [HashTable]
author: Jinwoo Kang
comments : True
---

여기서는 아주 간단히 프로그램 구조만 잡을 것이다. 복잡한 로직은 나중에 구현할 것이기 때문에 간단히 어떤 식으로 구현할 것인지 코드만 작성한다. 그래서 hash값을 일정하게 5로 두고 hash값을 출력하는 코드를 작성했다.

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
    return 5;
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
Jacob => 5
Bobby => 5
Natali => 5
Rone => 5
Railie => 5
Tyler => 5
{% endhighlight %}