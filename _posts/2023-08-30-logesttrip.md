---
title: Longest Trip
date: 2023-08-30 17:00:00 +0900
categories: [Authored Tasks, International Olympiad in Informatics 2023]
tags: [constructive, graph, interactive, ioi, '2023', solution]
math: true
---

The full statement in various languages can be found on the [IOI 2023 website](https://ioi2023.hu/tasks/#:~:text=longesttrip%20%E2%80%93-,Longest%20Trip,-Author%3A%20Hazem%20Issa). Additionally, you can test your solution by submitting your code to [dmoj.ca](https://dmoj.ca/problem/ioi23p2), [oj.uz](https://oj.uz/problem/view/IOI23_longesttrip), or [codeforces.com](https://ioi.contest.codeforces.com/group/32KGsXgiKA/contest/104548/problem/B).

---

## Description

### Brief Statement

There's a hidden graph with $n$ vertices. The graph has a special property that any $3$ distinct vertices have at least $d$ $(1 \le d \le 3)$ edges among them.

You can give the interactor two disjoint sets of vertices, $A$ and $B$, and the interactor will return `true` if there's at least one edge connecting a vertex from $A$ to a vertex from $B$, and `false` otherwise.

Your task is to find a simple path of maximum length while making as few interactions as possible.

### Example

Consider a scenario in which $n = 5$, $d = 1$, and the hidden graph is shown below.

![example](/longesttrip_example.png)

You can interact with the interactor by giving the following sets of $A$ and $B$, and, in return, receive the following responses.

| $A$          | $B$             | Response | Connected pairs         |
|:------------:|:---------------:|:--------:|:-----------------------:|
| $\\{0\\}$    | $\\{1,2,3,4\\}$ | `true`   | $(0,\,1)$ and $(0,\,2)$ |
| $\\{2\\}$    | $\\{3\\}$       | `true`   | $(2,\,3)$               |
| $\\{0,1\\}$  | $\\{3,4\\}$     | `false`  | none                    |
| $\\{0\\}$    | $\\{2\\}$       | `true`   | $(0,\,2)$               |

At this point, it may be concluded that $1 \rightarrow 0 \rightarrow 2 \rightarrow 3 \rightarrow 4$ is a valid simple path of maximum length.

### Constraints

* $3 \le n \le 256$
* $1 \le d \le 3$

### Subtasks

1. (5 points): $d = 3$
2. (10 points): $d = 2$
3. (25 points): Let the longest simple path be of length $l^\star$. You get the score for this subtask if you find a simple path of length at least $\lceil \frac{l^\star}{2} \rceil$.
4. (60 points): No additional constraints

In subtask 4, let $q$ be the number of interactions you make, your score is then calculated as follows:

| Condition                  | Points |
|:--------------------------:|:------:|
| $2\,750 \lt q \le 32\,640$ | $20$   |
| $550 \lt q \le 2\,750$     | $30$   |
| $400 \lt q \le 550$        | $45$   |
| $q \le 400$                | $60$   |

---

## Solution

### Subtask #1 ($d = 3$)

There are $3$ edges among any $3$ vertices which implies that the graph is a complete graph, meaning that every vertex is adjacent to every other vertex. So, any permutation of vertices is a valid answer.

Query analysis: $0$ queries.

<details markdown="1"><summary>Code</summary>

```cpp
vector<int> longest_trip(int n, int /*D*/)
{
    vector <int> p(n);
    iota(p.begin() ,p.end() ,0);
    random_shuffle(p.begin() ,p.end());
    return p;
}
```
</details>

### Subtask #2 ($d = 2$)

In this type of graph, any vertex can be nonadjacent to at most one vertex. It can be shown that the graph has a hamiltonian path which can be constructed as follows: let $P$ be a simple path consisting of less than $n$ nodes. Then, we can always append any node $u$, not in the path, to one of the ends of $P$ since it must be adjacent to at least one of them. Thus, we can find a hamiltonian path in $n$ queries.

Query analysis: we make $1$ query for each vertex. So, in total, we make $n$ queries.

![cases when d = 2](/longesttrip_solution_d_equal_two.png)

<details markdown="1"><summary>Code</summary>

```cpp
vector<int> longest_trip(int n, int /*D*/)
{
    deque <int> p;
    if(are_connected({0} ,{1}))
        p = {0 ,1};
    else
        p = {0 ,2 ,1};
    for(int u = p.size(); u < n; u++){
        if(are_connected({p.back()} ,{u}))
            p.push_back(u);
        else
            p.push_front(u);
    }
    return vector <int> {p.begin() ,p.end()};
}
```
</details>

### Subtask #3 (at least half as long as the longest path)

Now that $d$ can be equal to $1$, extending the path from both ends in the same manner as the previous subtask results in a new scenario in which neither end is adjacent to the new vertex.

![new case when d = 1](/longesttrip_solution_d_equal_one_new_case.png)

However, we can still greedily extend $P$ from one end by making queries with vertices not in $P$. If an adjacent vertex is found, we extend the path by adding that vertex to the end.

![path extension](/longesttrip_solution_path_extension.png)

More importantly, we gain valuable information when we query multiple vertices that are not adjacent to the end of $P$: all the nonadjacent vertices must form a clique. Take the example above for reference, $u$, $v$, $w$, and $x$ are all nonadjacent to vertex $m$. Consequently, if we pick any two of those vertices, there must exist an edge between them. For example, the edges $mu$ and $mv$ doesn't exist then $uv$ must exist. If not, we would have found $0$ edges among $3$ vertices which contradicts that $d = 1$.

![induced clique](/longesttrip_solution_induced_clique.png)

How is this helpful? this implies that at the moment we cannot extend $P$ any further, all the vertices not in $P$ form a clique. So we end up with a path and a clique. From subtask 1, any permutation of a clique's vertices form a path. As a result, we have $2$ disjoint paths covering all the vertices in the graph. We just take the longer one, and we have a solution for this subtask.

Query analysis: we make $n$ queries for each vertex in the worst case. So, in total, we make $O(n^2)$ queries.


<details markdown="1"><summary>Code</summary>

```cpp
vector<int> longest_trip(int n, int /*D*/)
{
    vector <int> p{0} ,q(n-1);
    iota(q.begin() ,q.end() ,1);
    while(true){
        bool ok = false;
        for(int u : q){
            if(are_connected({p.back()} ,{u})){
                p.push_back(u);
                q.erase(find(q.begin() ,q.end() ,u));
                ok = true;
                break;
            }
        }
        if(!ok)
            break;
    }
    return p.size() > q.size()? p : q;
}
```
</details>

### Subtask #4 (score based on number of interactions)

#### Solution using $n^2$ queries ($2\,750 \le q \le 32\,640$)

Building on subtask 3, we shall try merging the two paths we got:
* First, check that there exists an edge connecting the two sets of vertices ($1$ query). If none exists, the graph is disconnected, and the answer is the longer of the two paths we have. 
* If not, try to connect the endpoints of the paths exhausting all the $2 \times 2$ combinations. If a combination works, we have a hamiltonian path, and we are done. 
* If not, the two paths we have are, in fact, cycles, not only paths as shown below.

![path to cycle](/longesttrip_solution_two_paths_to_cycles.png)

* As a result, it suffices to find any edge that connects a vertex from the first cycle to a vertex in the second cycle. Then, we cut both cycles to form a hamiltonian path as shown below.

![two cycles to hamiltonian path](/longesttrip_solution_two_cycle_cut.png)

To find the connecting edge, we query every vertex from the first cycle with every vertex from the second cycle which takes $O(n^2)$ queries. 

<details markdown="1"><summary>Code</summary>

```cpp
vector <int> rev(vector <int> a){
    return {a.rbegin() ,a.rend()};
}
vector <int> operator+(vector <int> a ,vector <int> b){
    a.insert(a.end() ,b.begin() ,b.end());
    return a;
}

vector<int> longest_trip(int n, int /*D*/)
{
    vector <int> p{0} ,q(n-1);
    iota(q.begin() ,q.end() ,1);
    //makes ~n*n/2 queries
    while(true){
        bool ok = false;
        for(int u : q){
            if(are_connected({p.back()} ,{u})){
                p.push_back(u);
                q.erase(find(q.begin() ,q.end() ,u));
                ok = true;
                break;
            }
        }
        if(!ok)
            break;
    }

    if(q.empty() || !are_connected(p ,q))
        return p.size() > q.size()? p : q;
    if(are_connected({p.front()} ,{q.front()})) return rev(p) + q;
    if(are_connected({p.back()}  ,{q.front()})) return p + q;
    if(are_connected({p.front()} ,{q.back()}))  return q + p;
    if(are_connected({p.back()}  ,{q.back()}))  return p + rev(q);

    //makes ~n*n/2 queries
    for(int i = 0; i < p.size(); i++){
        p.push_back(p.front());
        p.erase(p.begin());
        for(int j = 0; j < q.size(); j++){
            if(are_connected({p.back()} ,{q[j]})){
                swap(q[0] ,q[j]); //exploiting the fact that q is a clique
                return p + q;
            }
        }
    }
}
```
</details>

#### Solution using $n\log n + 2n$ queries ($550 \le q \le 2\,750$)

Instead of extending $P$ by making a query with each vertex not in $P$, which takes $n$ queries, we can binary search for a vertex that is adjacent to the end of $P$ in $\log n$ queries. Then, we merge the paths using the same approach as above. 

However, to find the connecting edge, we will first query a single vertex from $P$ with all the vertices from $Q$. If the result is positive, only then do we search for a vertex in $Q$. As a result, we make at most $n$ queries to find a vertex in $P$ adjacent to one from $Q$, then another $n$ queries to find the vertex in $Q$ adjacent to that in $P$.

Query analysis: we make at most $n \log n$ queries extending $P$, then we make $2n$ queries to find the connecting edge between $P$ and $Q$. So, in total, we make $n \log n + 2n$ ($\approx 2560$) queries.  

<details markdown="1"><summary>Code</summary>

```cpp
vector <int> rev(vector <int> a){
    return {a.rbegin() ,a.rend()};
}
vector <int> operator+(vector <int> a ,vector <int> b){
    a.insert(a.end() ,b.begin() ,b.end());
    return a;
}

vector<int> longest_trip(int n, int /*D*/)
{
    vector <int> p{0} ,q(n-1);
    iota(q.begin() ,q.end() ,1);
    //makes ~nlogn queries
    while(true){
        int l = 1 ,r = q.size() ,m;
        while(l <= r){
            m = (l + r) / 2;
            if(are_connected({p.back()} ,{q.begin()+l-1 ,q.begin() + m}))
                r = m - 1;
            else
                l = m + 1;
        }
        if(r == q.size())
            break;
        p.push_back(q[r]);
        q.erase(q.begin() + r);
    }

    if(q.empty() || !are_connected(p ,q))
        return p.size() > q.size()? p : q;
    if(are_connected({p.front()} ,{q.front()})) return rev(p) + q;
    if(are_connected({p.back()}  ,{q.front()})) return p + q;
    if(are_connected({p.front()} ,{q.back()}))  return q + p;
    if(are_connected({p.back()}  ,{q.back()}))  return p + rev(q);

    //makes ~2n queries
    for(int i = 0; i < p.size(); i++){
        p.push_back(p.front());
        p.erase(p.begin());
        if(!are_connected({p.back()} ,q))
            continue;

        for(int j = 0; j < q.size(); j++){
            if(are_connected({p.back()}, {q[j]})){
                swap(q[0] ,q[j]); //exploiting the fact that q is a clique
                return p + q;
            }
        }
    }
}
```
</details>

#### Solution using $2n + 2\log n$ queries ($400 \le q \le 550$)

Instead of extending one path, we will build two paths, $P$ and $Q$, simultaneously. Let $u$ be a vertex currently not in $P$ nor $Q$. It can be shown that at least one of the following is true:
  1. $end(Q)$ is adjacent to $u$, so we can append $u$ to the end of $Q$.
  2. $end(P)$ is adjacent to $u$, so we can append $u$ to the end of $P$.
  3. $end(P)$ and $end(Q)$ are adjacent, so we can append $Q$ reversed to the end of $P$, and change $Q$ to be a path consisting of a single vertex, $u$.
  
![two path extension](/longesttrip_solution_two_path_extension.png)

In the end, all the vertices will either be in $P$ or $Q$. Similar to the previous solutions, we need to merge those two paths. To find the connecting edge effeciently, we will do two binary searches: the first to find a vertex, $v$, from $P$ connected to some vertex from $Q$; and the second to find a vertex from $Q$ connected to $v$. This way, we find the connecting edge in $2 \log n$ queries.

Query analysis: for each vertex, we make $2$ queries to check for cases $1$ and $2$. Then, we merge the two paths in $2 \log n$ queries. So, in total, we make $2n + 2 \log n$ ($\approx 530$) queries.

<details markdown="1"><summary>Code</summary>

```cpp
vector <int> rev(vector <int> a){
    return {a.rbegin() ,a.rend()};
}
vector <int> operator+(vector <int> a ,vector <int> b){
    a.insert(a.end() ,b.begin() ,b.end());
    return a;
}

vector<int> longest_trip(int n, int /*D*/)
{
    vector <int> p{0} ,q{1};
    for(int u = 2; u < n; u++){
        if(are_connected({q.back()} ,{u}))
            q.push_back(u);
        else if(are_connected({p.back()} ,{u}))
            p.push_back(u);
        else{
            p = p + rev(q);
            q = {u};
        }
    }

    if(q.empty() || !are_connected(p ,q))
        return p.size() > q.size()? p : q;
    if(are_connected({p.front()} ,{q.front()})) return rev(p) + q;
    if(are_connected({p.back()}  ,{q.front()})) return p + q;
    if(are_connected({p.front()} ,{q.back()}))  return q + p;
    if(are_connected({p.back()}  ,{q.back()}))  return p + rev(q);

    //rotate cycle `s` so that the new first vertex of `s` is connected to a vertex from `t`
    auto ord = [](vector <int> s ,vector <int> t){
        int l = 1 ,r = s.size() ,m;
        while(l <= r){
            m = (l + r) / 2;
            if(are_connected({s.begin() ,s.begin() + m} ,t))
                r = m - 1;
            else
                l = m + 1;
        }
        rotate(s.begin() ,s.begin() + r ,s.end());
        return s;
    };

    p = ord(p ,q);
    q = ord(q ,{p.front()});
    return rev(p) + q;
}
```
</details>



#### Solution using $1.5n + 2\log n$ queries ($q \le 400$)

We will introduce a new optimization to the previous solution. Let's define a **relation** between two vertices, $u$ and $v$, to exist if the edge $uv$ doesn't exist. As a result, all the vertices must be connected to at least one of $u$ or $v$.

The idea is to exploit the **relation** between $end(P)$ and $end(Q)$. Previously, if case 1 is true, we make $1$ query, otherwise, we make $2$ queries. Now, if a **relation** exists, we make only $1$ query: case 1 is false implies that case 2 is true.

![relation optimization](/longesttrip_solution_two_path_relation.png)

Notice how, by assuming that either case 1 or case 2 is always true, we limit our queries to a maximum of $1.5n$ queries since we would never need to check case 2 for two consecutive times.

However, when both case 1 and case 2 are false, we make more than $1.5n$ queries. Fortunately, we can turn this case into our advantage by introducing two more vertices, $v$ and $w$. If we query each of $v$ and $w$ with $u$, we can guarantee a way to divide them into two paths as shown below.

![introduction of two new nodes](/longesttrip_solution_three_new_nodes.png )

As a result, we add $3$ new vertices with $4$ queries. Since we can split the queries into disjoint phases, and each has a query-to-vertex ratio no greater than $3 : 2$, overall the two-path extension part would make $1.5n$ queries in the worst case. Finally, we connect both cycles by doing two binary searches like the previous solution which takes $2 \log n$ queries. 

Query analysis: $1.5n + 2 \log n$ ($\approx 400$) queries.

<details markdown="1"><summary>Code</summary>

```cpp
vector <int> rev(vector <int> a){
    return {a.rbegin() ,a.rend()};
}
vector <int> operator+(vector <int> a ,vector <int> b){
    a.insert(a.end() ,b.begin() ,b.end());
    return a;
}

vector<int> longest_trip(int n, int /*D*/)
{
    bool relation = false;
    vector <int> p{0} ,q{1};
    for(int u = 2; u < n; u++){
        if(are_connected({q.back()} ,{u})){
            relation = false;
            q.push_back(u);
        }
        else if(relation || are_connected({p.back()} ,{u})){
            relation = true;
            p.push_back(u);
        }
        else if(u + 2 < n){
            relation = false;
            int v = u + 1;
            int w = u + 2;
            using vi = vector <int>;
            switch(are_connected({u} ,{v})<<1 | are_connected({u} ,{w})){
                case 0b11: p = p + rev(q);            q = {v ,u ,w}; break;
                case 0b10: p = p + vi{w} + rev(q);    q = {u ,v};    break;
                case 0b01: p = p + vi{v} + rev(q);    q = {u ,w};    break;
                case 0b00: p = p + vi{v ,w} + rev(q); q = {u};       break;
            }
            u += 2;
        }
        else{
            relation = false;
            p = p + rev(q);
            q = {u};
        }
    }

    if(q.empty() || !are_connected(p ,q))
        return p.size() > q.size()? p : q;
    if(are_connected({p.front()} ,{q.front()})) return rev(p) + q;
    if(are_connected({p.back()}  ,{q.front()})) return p + q;
    if(are_connected({p.front()} ,{q.back()}))  return q + p;
    if(are_connected({p.back()}  ,{q.back()}))  return p + rev(q);

    //rotate cycle `s` so that the new first vertex of `s` is connected to a vertex from `t`
    auto ord = [](vector <int> s ,vector <int> t){
        int l = 1 ,r = s.size() ,m;
        while(l <= r){
            m = (l + r) / 2;
            if(are_connected({s.begin() ,s.begin() + m} ,t))
                r = m - 1;
            else
                l = m + 1;
        }
        rotate(s.begin() ,s.begin() + r ,s.end());
        return s;
    };

    p = ord(p ,q);
    q = ord(q ,{p.front()});
    return rev(p) + q;
}
```
</details>


---

## References

[https://ioi2023.hu/tasks](https://ioi2023.hu/tasks/#:~:text=longesttrip%20%E2%80%93-,Longest%20Trip,-Author%3A%20Hazem%20Issa)

[https://dmoj.ca/problem/ioi23p2](https://dmoj.ca/problem/ioi23p2)

[https://oj.uz/problem/view/IOI23_longesttrip](https://oj.uz/problem/view/IOI23_longesttrip)

[https://ioi.contest.codeforces.com/group/32KGsXgiKA/contest/104548/problem/B](https://ioi.contest.codeforces.com/group/32KGsXgiKA/contest/104548/problem/B).
