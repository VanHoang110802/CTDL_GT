## Sample
input
```
In the first line the number of vertices V is given. (1 ≤ V ≤ 55)
From the second line over V edges the connective relations between vertex number V and the vertex number 1-V are entered.
If the ith value in the vth line is 1 it means that the edge starts from V and heads to vertex i and when the value is 0 it means that edge does
not exist.

4
0 1 0 1
1 0 0 0
0 0 0 1
0 1 0 0

```

output
```
From Number 1 to the vth vertex over a V number of edges, output the number of vertices connected to each vertex in a form like the following
example.

1: 2 4
2: 1
3: 4
4: 2
```

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX 55

typedef struct node
{
    struct node* next;
    int value;
} node;

void add(node** head, int value)
{
    node* new_node = (node *)malloc(sizeof(node));
    new_node->value = value;
    if (!(*head))
    {
        new_node->next = NULL;
        (*head) = new_node;
    }
    else
    {
        new_node->next = (*head);
        (*head) = new_node;
    }
    return;
}

int main()
{
    int adj_matrix[MAX][MAX], n;

    node* head[MAX];

    scanf("%d", &n);

    for (int i = 1; i <= n; i++)
    {
        head[i] = NULL;

        for (int j = 1; j <= n; j++)
        {
            scanf("%d", &adj_matrix[i][j]);

            if (adj_matrix[i][j])
                add(&head[i], j);
        }
    }

    for (int i = 1; i <= n; i++)
    {
        node* current_node = head[i];

        printf("%d : ", i);

        while (current_node)
        {
            printf("%d ", current_node->value);
            current_node = current_node->next;
        }
        printf("\n");
    }

    return 0;
}
```
