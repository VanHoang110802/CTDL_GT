## Sample
input:
```
The first line of the input gives the number of commands; N (1 ≤ N ≤ 1,000,000).
N lines follow. Each has the commands below.
enqueue x: insert x to a queue
dequeue: delete the factor entered first
size: print the size of queue
front: print the value of the first factor in a queue

8
size
enqueue 4
enqueue 3
front
size
dequeue
front
size
```

output
```
Output values based on the order of each command.

0
4
2
3
1
```

```c
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>
#include <stdbool.h>

struct queue_node
{
    int data;
    struct queue_node* next;
};

struct queue
{
    struct queue_node* front;
    struct queue_node* rear;
    int sz;
};
void queue_init(struct queue *q)
{
    q->front = q->rear = NULL;
    q->sz = 0;
}
int queue_size(struct queue *q)
{
    return q->sz;
}
bool queue_empty(struct queue *q)
{
    return queue_size(q) == 0;
}
void queue_enqueue(struct queue *q, int val)
{
    struct queue_node *newNode = (struct queue_node*)malloc(sizeof(struct queue_node));
    newNode->data = val;
    newNode->next = 0;
    q->sz++;
    if (q->rear==NULL && q->front==NULL)
    {
        q->rear=q->front = newNode;
        return;
    }
    q->rear->next = newNode;
    q->rear = newNode;
}
void queue_dequeue(struct queue *q)
{
    assert(!queue_empty(q));
    struct queue_node *nextHead = q->front->next;
    free(q->front);
    if (q->front == q->rear) q->front = q->rear = 0;
    q->front = nextHead;
    q->sz--;
}
int queue_front(struct queue *q)
{
    assert(!queue_empty(q));
    return q->front->data;
}

int val, N;
char cmd[16];

int main()
{
    struct queue q;
    scanf("%d", &N);
    queue_init(&q);
    for (int i = 0; i < N; i++)
    {
        scanf(" %s", cmd);
        if (cmd[0] == 's')
        {
            printf("%d\n", queue_size(&q));
        }
        else if (cmd[0] == 'e')
        {
            scanf("%d", &val);
            queue_enqueue(&q, val);
        }
        else if (cmd[0] == 'd')
        {
            queue_dequeue(&q);
        }
        else if (cmd[0] == 'f')
        {
            printf("%d\n", queue_front(&q));
        }
    }

    return 0;
}
```
