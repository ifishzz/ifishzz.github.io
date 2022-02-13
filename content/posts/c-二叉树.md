---
title: c-二叉树
date: 2022-02-13
categories: ["数据结构"]
tags: []
---

```
#include <stdlib.h>
#include <stdio.h>
/* 树的节点 */
typedef struct tree_node {
    /* 左孩子指针 */
    struct tree_node *left;
    /* 右孩子指针 */
    struct tree_node *right;
    /* 关键字 */
    char key;
}tree_node;
/* 创建一个节点 */
tree_node *tree_create_node(char key)
{
    tree_node *node = (struct tree_node*)malloc(sizeof(struct tree_node));
    if(node==NULL) return NULL;
    node->key = key;
    node->left = NULL;
    node->right = NULL;

    return node;
}
/* 创建一棵二叉树 */
tree_node *tree_create()
{
    char str;
    tree_node *current;
    scanf("%c", &str);
    // input ABD##E##CF##G##
    if('#' == str)
    {
        current = NULL;
    }
    else {
        current = tree_create_node(str);
        current->left = tree_create();
        current->right = tree_create();
    }
    return current;
}

/* 前序遍历 */
void preorder_traverse1(tree_node *node)
{
    if(node != NULL) {
        printf("%c\t", node->key);
        preorder_traverse1(node->left);
        preorder_traverse1(node->right);
    }
}

/* 中序遍历 */
void inorder_traverse1(tree_node *node)
{
    if(node != NULL) {
        inorder_traverse1(node->left);
        printf("%c\t", node->key);
        inorder_traverse1(node->right);
    }
}

/* 后序遍历 */
void postorder_traverse1(tree_node *node)
{
    if(node != NULL) {
        postorder_traverse1(node->left);
        postorder_traverse1(node->right);
        printf("%c\t", node->key);
    }
}

int main() {

    /* ABD##E##CF##G## */
    tree_node *root = tree_create();

    printf("\n前序遍历1:");
    preorder_traverse1(root);


    printf("\n\n中序遍历1:");
    inorder_traverse1(root);


    printf("\n\n后序遍历1:");
    postorder_traverse1(root);




    printf("\n");
    return 0;
}
```