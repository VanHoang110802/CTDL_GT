## Sample
input
```
In the first line the number of vertices V is given. (1 ≤ V ≤ 55)
From the second line over V edges the connective relations between vertex number V and the vertex number 1-V are entered.
If the ith value in the vth line is 1 it means that the edge starts from V and heads to vertex i and when the value is 0 it means that edge does not exist.

4
0 1 0 1
1 0 0 0
0 0 0 1
0 1 0 0

```

output
```
From Number 1 to the vth vertex over a V number of edges, output the number of vertices connected to each vertex in a form like the following example.

1: 2 4
2: 1
3: 4
4: 2
```
