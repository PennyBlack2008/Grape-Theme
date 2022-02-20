---
layout: post
title: 6. 해쉬 테이블로 가는 길 4
subtitle : hash_table_delete 구현하기
tags: [HashTable]
author: Jinwoo Kang
comments : True
---

- 이름을 받아 해쉬테이블에서 대응 되는 이름을 제거하는 함수 작성
- 하지만, 해쉬 테이블에 중복된 해쉬 값은 테이블에 들어가지 못하고 여전히 튕겨나온다.

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
    printf("Start\n");
    for (int i = 0; i < TABLE_SIZE; i++) {
        if (hash_table[i] == NULL) {
            printf("\t%i\t---\n", i);
        } else {
            printf("\t%i\t%s\n", i, hash_table[i]->name);
        }
    }
    printf("End\n");
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

// find a person in the table by their name
person* hash_table_lookup(char* name) {
    int index = hash(name);
    if (hash_table[index] != NULL &&
        strncmp(hash_table[index]->name, name, TABLE_SIZE) == 0) {
            return hash_table[index];
        } else {
            return NULL;
        }
}

person* hash_table_delete(char* name) {
    int index = hash(name);
    if (hash_table[index] != NULL &&
        strncmp(hash_table[index]->name, name, TABLE_SIZE) == 0) {
            person* tmp = hash_table[index];
            hash_table[index] = NULL;
            return tmp;
        }
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

    hash_table_delete("Mpho");

    print_table();

    return 0;
}
{% endhighlight %}


**터미널**
{% highlight shell %}
Start
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
End
Found Mpho.
Not found!
Start
        0       ---
        1       Kate
        2       Jacob
        3       ---
        4       ---
        5       ---
        6       ---
        7       ---
        8       ---
        9       ---
End
{% endhighlight %}