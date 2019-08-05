---
layout: post
title: (C) Priority Queue Implement on LinkedList
published: true
---

### TL;DR (Hint)
For a number of leetcodes that involve doing some kind of BFS on a tree or needing to enqueue and dequeue items, I have turned to Python because of the splicing interface that allows for simple ```list[0];list[1:]``` behavior which essentially translates to, give the first element of my life and then cut it out. 

I wanted to have an interface like this in C, or at least the queue interface, and threw together a simple one that uses LinkedLists as the underlying data structure to make the magic happen. Priority is given on a basis of 0 being the root node and should be reserved for it, and the closer you are to 0, the more priority you have in the queue. 

I have written a simple test that is not comprehensive and will probably have to update some of the code for safety since NULL could be thrown at me for whatever reason. 

### Runtimes:

* Insertion: O(N) -- checks potentially all the elements for their priority making it an O(N) operation
* Peek: O(1)
* Poll: O(1)
* Deletion: O(1) -- Would require you to Poll() the interface, FIFO*


### Code

#### queue.h
```
/* 
Queue (C)

Designed using a LinkedList underlying implementation.
@author cheenar

Insert: O(n) --> priority system
Pop: O(1)

 */

#include <stdlib.h>
#include <stdio.h>

typedef struct QueueNode {
    void *value;
    unsigned int priority;
    struct QueueNode *next;
} QueueNode;

typedef struct Queue {
    QueueNode *root;
    unsigned size;
} Queue;

void make_queue(Queue* queue);
void insert(Queue *queue, void *value, unsigned int priority); 
QueueNode* pop(Queue *queue);
QueueNode* peek(Queue *queue);
```

#### queue.c
```
#include "queue.h"
#include <assert.h>

void make_queue(Queue* queue) {
    queue->size = 0;
    queue->root = malloc(sizeof(QueueNode));
    queue->root->next = NULL;
    queue->root->priority = 0;
    queue->root->value = NULL;
}

void insert(Queue *queue, void *value, unsigned int priority) {
    if (queue == NULL) return;

    QueueNode *next = queue->root;
    while (next->next != NULL) {
        if (next->next->priority > priority) break;
        next = next->next;
    }

    QueueNode *node = malloc(sizeof(QueueNode));
    node->priority = priority;
    node->value = value;
    node->next = next->next;

    next->next = node;

    queue->size += 1;
}

QueueNode* pop(Queue *queue) {
    QueueNode *node = queue->root->next;
    if (node == NULL) return NULL;
    queue->root->next = node->next;
    queue->size -= 1;
    return node;
}

QueueNode* peek(Queue *queue) {
   return queue->root->next; 
}

void test_queue() {
    Queue x;
    make_queue(&x);

    insert(&x, (void*)"alec", 2);
    insert(&x, (void*)"brian", 2);
    insert(&x, (void*)"david", 2);
    insert(&x, (void*)"fiallo", 1);

    printf("Queue Size: %u\n", x.size);

    printf("%s\n", pop(&x)->value); // fiallo
    printf("%s\n", pop(&x)->value); // alec
    printf("%s\n", pop(&x)->value); // brian
    printf("%s\n", pop(&x)->value); // david

    printf("Queue Size: %u\n", x.size);
}

int main() {
    test_queue();
    return 0;
}

```