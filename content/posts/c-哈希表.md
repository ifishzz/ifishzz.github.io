---
title: c-哈希表
date: 2022-02-13
categories: ["数据结构"]
tags: []
---

```
#include <stdlib.h>
#include <string.h>
#include <stdio.h>
typedef struct hash_node {
    void *key;
    void *val;
    struct hash_node *next;
} hash_node;
typedef struct hash_table {
    hash_node **table;
    int size;
    // hashmask https://its301.com/article/qing_gee/120260024
    int sizemask;
} hash_table;

unsigned int hash_33(char *str) {
    unsigned int hash = 5381;
    while (*str) {
        hash += (hash << 5) + (*str++);
    }
    return (hash & 0x7FFFFFFF);
}

hash_table *hash_table_create() {
    hash_table *hashTable = (hash_table *) malloc(sizeof(hash_table));
    if (hashTable == NULL) return NULL;
    hashTable->size = 1024;
    hashTable->sizemask = hashTable->size - 1;
    // 申请1024个节点内存,可以看作是数组
    hashTable->table = (hash_node **) malloc(sizeof(hash_node *) * (hashTable->size));
    if (hashTable->table == NULL) return NULL;
    //数组元素置零
    memset(hashTable->table, 0, sizeof(hash_node *) * (hashTable->size));
    return hashTable;
}

//这个节点相当于是单链表
hash_node *hash_node_create(void *key, void *val) {
    hash_node *hashNode = (hash_node *) malloc(sizeof(hash_node));
    if (hashNode == NULL) return NULL;
    hashNode->next = NULL;
    hashNode->val = NULL;
    hashNode->key = NULL;
    return hashNode;

}

hash_table *hash_table_insert(hash_table *hashTable, void *key, void *val) {
    unsigned int hash = hash_33(key);
    int pos = hash & hashTable->sizemask;
    hash_node *hashNode = hash_node_create(key, val);
    hashNode->next = hashTable->table[pos];
    hashTable->table[pos] = hashNode;
    return hashTable;

}

void *get_val(hash_table *hashTable, void *key) {
    unsigned int hash = hash_33(key);
    int pos = hash & hashTable->sizemask;
    if (hashTable->table[pos] == 0) return NULL;
    hash_node *current = hashTable->table[pos];
    while (current) {
        if (hash_33(current->key) == hash_33(key)) {
            return current->val;
        } else {
            current = current->next;
        }

    }
    return NULL;
}

int main() {
    hash_table *hashTable = hash_table_create();
    hash_table_insert(hashTable, "test1", "dsafasdfads");
    puts(get_val(hashTable, "test1"));
    return 0;
}
```