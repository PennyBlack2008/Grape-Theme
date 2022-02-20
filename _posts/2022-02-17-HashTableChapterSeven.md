---
layout: post
title: 7. 해쉬 테이블로 가는 길 5
subtitle : 겹치는 해쉬값을 어떻게 할까?
tags: [HashTable]
author: Jinwoo Kang
comments : True
---

- 해쉬 테이블에 중복된 해쉬 값은 테이블에 들어가지 못하고 여전히 튕겨나온다.
- 그래서 10개를 입력해도 겹치는 해쉬 값 때문에 6사람만 들어간다

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
    if (hash_table[index] != NULL) { /* 여기서 중복되는 hash값 들어오면 */
        return false;                /* overwrite되지 않도록 막음 */
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
     * 어떤 사람은 테이블에 안들어가있는 것을 알 수 있다.
     * 중복되는 hash값 때문이다. 그래서 이것을 다음챕터에서 해결할 것이다. */
    return 0;
}
{% endhighlight %}


**터미널**
{% highlight shell %}
Start
        0       Tylor
        1       Kate
        2       Jacob
        3       ---
        4       Sarah
        5       Mpho
        6       ---
        7       Eliza
        8       ---
        9       ---
End
Found Mpho.
{% endhighlight %}