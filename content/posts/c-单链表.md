---
title: c-单链表
date: 2022-02-13
categories: ["数据结构"]
tags: []
---

```
//定义节点
typedef struct node {
    int data; //节点存放数据
    struct node *next;  //指针域
} node;

//定义头指针
typedef struct list {
    int size; //链表的长度
    struct node *next; //指针域
} list;


list *crete_list() {
       
    //用malloc开辟一块list大小的内存,返回一个list的指针
    list *l = malloc(sizeof(list));

    //判断申请内存是否成功
    if (l == NULL) {
        printf("mem error");
        return 0;
    }

    //初始化
    l->size = 0; //头节点的数据域,用来表示链表的长度
    l->next = NULL;

    return l;
}


node *crate_node() {
    node *n = malloc(sizeof(node));
    if (n == NULL) {
        printf("mem error");
        return 0;
    }
    n->data = 0;
    n->next = NULL;
    return n;

}

//头插

int head_add(list *list, int data) {
    //新建空节点
    node *new_node = crate_node();

    new_node->data = data;
    new_node->next = list->next;
    list->next = new_node;
    list->size++;

    return list;
}

//尾插
int tail_add(list *list, int data) {
    node *new_node = crate_node();
    new_node->data = data;
    node *last = list->next;

    //如果last为NULL就证明是尾节点了,直接插入
    if (!last) {
        list->next = new_node;
    } else {
        //当last的next值不是NULL,保存到last指针
        while (last->next) {
            last = last->next;
        }
        //新节点插入到last next
        last->next = new_node;
    }

    list->size++;

    return list;
}

//插入
//链表的增加结点操作主要分为查找到第i个位置，将该位置的next指针修改为指向我们新插入的结点，而新插入的结点next指针指向我们i+1个位置的结点。其操作方式可以设置一个前驱结点，利用循环找到第i个位置，再进行插入。

list *list_insert(list *list, int data, int pos) {
    node *curr = list;
    int i;
    for (i = 0; i < pos; i++) {
        curr = curr->next;                 //查找第i个位置的前驱结点
    }
    //新建节点
    node *new_node = crate_node();
    //赋值节点data
    new_node->data = data;

    //插入
    new_node->next = curr->next;
    curr->next = new_node;

    /* 链表长度+1 */
    list->size++;
    return list;


}

//删除节点

list *list_del(list *list, int pos) {
    int i;
    node *curr = list;
    //遍历链表找到要删除的节点的下一个指针

    for (i = 0; i < pos; i++) {
        curr = curr->next;
    }

    //临时记录被删除的节点

    node *temp = curr->next;
    //删除节点
    curr->next = curr->next->next;
    //释放掉被删除节点的内存
    free(temp);
    list->size--;
    return list;
}

//删除值

list *list_vul_del(list *list, int data) {
    node *curr;
    node *p = list->next;
    while (p->data != data) {
        curr = p;
        p = p->next;
    }
    curr->next = p->next;
    free(p);
    list->size--;
    return list;
}


void print_list(list *list) {
    //打印链表总长度
    printf("len: %d\n", list->size);

    int i = 0;

    //list->next值就是下一个节点的指针变量,每个节点都会保存着下一个节点的值
    node *p = list->next;
    while (p) {
        printf("第%d个元素的值为:%d\n", ++i, p->data);
        p = p->next;
    }
}


int main() {
    list *l = crete_list();
    head_add(l, 1);
    head_add(l, 2);
    head_add(l, 10);
    tail_add(l, 100);
    tail_add(l, 111);
    list_insert(l, 520, 1);
    list_del(l, 2);
    list_vul_del(l, 520);
    print_list(l);


}
```