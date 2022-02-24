---
layout: post
title: 4. 해쉬 테이블로 가는 길 2
subtitle : 해쉬 테이블 선언하기
tags: [HashTable]
author: Jinwoo Kang
comments : True
---

3에서 해쉬값을 제한하여 각 이름의 인덱스가 의도한 정도의 크기로 얻어낼 수 있었다. 지금까지는 printf에서 hash함수에 직접적으로 이름을 넣었다면, 이제는 전역 변수로 해쉬 테이블을 선언하여 이 해쉬 테이블에 각 해쉬값에 해당되는 인덱스에 이름을 넣을 것이다.

- 테이블을 선언하고 테이블을 초기화
- 해쉬값을 얻어 그 값에 해당되는 테이블 속 인덱스 요소 자리에 이름을 넣는다. 하지만, 인덱스에 이미 요소가 있다면 넣지 않고 무시한다.

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

person * hash_table[TABLE_SIZE];

unsigned int hash(char* name) {
    int length = strnlen(name, MAX_NAME);
    unsigned int hash_value = 0;
    for (int i = 0; i < length; i++) {
        hash_value += name[i];
        hash_value = (hash_value * name[i]) % TABLE_SIZE;
    }
    return hash_value;
}

void init_hash_table() {
    for (int i = 0; i < TABLE_SIZE; i++) {
        hash_table[i] = NULL;
    }
    // table is empty
}

void print_table() {
    for (int i = 0; i < TABLE_SIZE; i++) {
        if (hash_table[i] == NULL) {
            printf("\t%i\t---\n", i);
        } else {
            printf("\t%i\t%s\n", i, hash_table[i]->name);
        }
    }
}

bool hash_table_insert(person* p) {
    if (p == NULL) return false;
    int index = hash(p->name);
    if (hash_table[index] != NULL) {
        return false;
    }
    hash_table[index] = p;
    return true;
}

int main() {
    init_hash_table();

    person jacob = {.name="Jacob", .age=256};
    person kate = {.name="Kate", .age=27};
    person mpho = {.name="Mpho", .age=14};

    hash_table_insert(&jacob);
    hash_table_insert(&kate);
    hash_table_insert(&mpho);
    print_table();

    return 0;
}
{% endhighlight %}


**터미널**
{% highlight shell %}
        0       ---
        1       Kate
        2       Jacob
        3       ---
        4       ---
        5       Mpho
        6       ---
        7       ---
        8       ---
        9       ---
{% endhighlight %}