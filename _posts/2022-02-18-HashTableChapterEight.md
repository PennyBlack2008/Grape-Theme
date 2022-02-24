---
layout: post
title: 8. 해쉬 테이블로 가는 길 6(마무리)
subtitle : 해쉬값이 중복되어도 잘 자리를 잡는 마법의 코드
tags: [HashTable]
author: Jinwoo Kang
comments : True
---

- `tryIndex = (i + index) % TABLE_SIZE`를 다음과 같이 tryIndex 코드로 삽입, 출력, 삭제 시 요소에 접근하기 위해 사용한다.
    - index를 i에 미리 더하고 TABLE_SIZE를 나누는 이유는 예를 들어, 해쉬값이 9인 요소가 들어올 때 9번, 10번 인덱스에 모두 요소가 차있으면 0번 인덱스도 고려할 수 있도록 계산한 것이다.

**코드**
{% highlight c %}
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <stdint.h>
#include <stdbool.h>

#define MAX_NAME 256
#define TABLE_SIZE 10
#define DELETED_NODE (person*)(0xFFFFFFFFFFFUL)

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
        hash_value = (hash_value * name[i]) % TABLE_SIZE; // hash value를 더 복잡하게 하려면
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
    printf("Start\n");
    for (int i = 0; i < TABLE_SIZE; i++) {
        if (hash_table[i] == NULL) {
            printf("\t%i\t---\n", i);
        } else if (hash_table[i] == DELETED_NODE) {
            printf("\t%i\t---<deleted>\n", i);
        } else {
            printf("\t%i\t%s\n", i, hash_table[i]->name);
        }
    }
    printf("End\n");
}

bool hash_table_insert(person* p) {
    if (p == NULL) return false;
    int index = hash(p->name);
    for (int i=0; i < TABLE_SIZE; i++) {
        int tryIndex = (i + index) % TABLE_SIZE;
        if (hash_table[tryIndex] == NULL ||
            hash_table[tryIndex] == DELETED_NODE) {
            hash_table[tryIndex] = p;
            return true;
        }
    }
    return false;
}

/** hash table에 삽입하는 방법이 바뀌었으니 탐색하는 방법도 바뀌어야한다. **/
person* hash_table_lookup(char* name) {
    int index = hash(name);
    for (int i=0; i < TABLE_SIZE; i++) {
        int tryIndex = (index + i) % TABLE_SIZE;
        if (hash_table[tryIndex] == NULL) {
            return NULL;
        }
        if (hash_table[tryIndex] == DELETED_NODE) continue ;
        if (hash_table[tryIndex] != NULL &&
            strncmp(hash_table[tryIndex]->name, name, TABLE_SIZE)==0) {
                return hash_table[tryIndex];
            }
    }
    return NULL;
}

person* hash_table_delete(char* name) {
    int index = hash(name);
    for (int i=0; i<TABLE_SIZE; i++) {
        int tryIndex = (index + i) % TABLE_SIZE;
        if (hash_table[tryIndex] == NULL) return NULL;
        if (hash_table[tryIndex] == DELETED_NODE) continue ;
        if (strncmp(hash_table[tryIndex]->name, name, TABLE_SIZE) == 0) {
                person* tmp = hash_table[tryIndex];
                hash_table[tryIndex] = DELETED_NODE;
                return tmp;
        }
    }
}

int main() {
    init_hash_table();

    person jacob = {.name="Jacob", .age=256};
    person kate = {.name="Kate", .age=27};
    person mpho = {.name="Mpho", .age=14};
    person sarah = {.name="Sarah", .age=54};
    person eliza = {.name="Eliza", .age=21};
    person jenny = {.name="Jenny", .age=23};
    person tylor = {.name="Tylor", .age=33};
    person hera = {.name="Hera", .age=53};
    person dongy = {.name="Dongy", .age=31};
    person eiden = {.name="Eiden", .age=45};

    hash_table_insert(&jacob);
    hash_table_insert(&kate);
    hash_table_insert(&mpho);
    hash_table_insert(&sarah);
    hash_table_insert(&eliza);
    hash_table_insert(&jenny);
    hash_table_insert(&tylor);
    hash_table_insert(&hera);
    hash_table_insert(&dongy);
    hash_table_insert(&eiden);
    print_table();

    person* tmp = hash_table_lookup("Mpho");
    if (tmp == NULL) {
        printf("Not found!\n");
    } else {
        printf("Found %s.\n", tmp->name);
    }

    tmp = hash_table_lookup("George");
    if (tmp == NULL) {
        printf("Not found!\n");
    } else {
        printf("Found %s.\n", tmp->name);
    }

    /**
     * 모두 테이블에 들어가있는 것을 알 수 있다. 또한,
     * Mpho를 삭제하는 코드를 주석 해제하면 삭제 표시가 뜨는 것을 볼 수 있다.
     * */
    return 0;
}
{% endhighlight %}


**터미널**
{% highlight shell %}
Start
        0       Tylor
        1       Kate
        2       Jacob
        3       Jenny
        4       Sarah
        5       Mpho
        6       Hera
        7       Eliza
        8       Dongy
        9       Eiden
End
Found Mpho.
Not found!
{% endhighlight %}