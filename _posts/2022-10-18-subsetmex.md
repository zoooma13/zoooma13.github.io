---
title: Subset Mex
date: 2022-10-18 00:00:00 +0900
categories: [Authored Tasks, European Girls' Olympiad in Informatics 2022]
tags: [ad-hoc, egoi, '2022', solution]
math: true
---

The full statement in various languages can be found on the [EGOI website](https://stats.egoi.org/tasks/subsetmex/). Additionally, you can test your solution by submitting your code to [dmoj.ca](https://dmoj.ca/problem/egoi22p1) or [codeforces.com](https://codeforces.com/gym/104229/problem/A).

---

## Description

### Brief Statement

You are given a multiset $S$ and an integer $n$. You may do the following three-step operation some number of times.

1. Choose a set $T$ such that $T \subseteq S$ ($T$ may be empty).
2. Erase the elements of $T$ from $S$.
3. Insert $\text{mex}(T)$ to $S$.

Find the minimum number of operations you need to perform to include $n$ in $S$ ($n \in S$).

Since the size of set $S$ may be large, you are given an array $f$ consisting of $n$ elements. The $i$-th ($0 \le i \le n-1$) element in $f$ denotes the frequency of the integer $i$ in $S$. 

### Example

When $n = 4$ and $f = [0, 3, 0, 3]$, we start with an initial set $S = \\{1, 1, 1, 3, 3, 3\\}$, and the goal is to add $4$ to $S$. This can be accomplished through the following steps:

|     | **Chosen $T$**     | **New $S$**                             | 
|-----|--------------------|-----------------------------------------|
| $0$ |                    | $\\{1, 1, 1, 3, 3, 3\\}$                |
| $1$ | $\\{\\}$           | $\\{\underline{0}, 1, 1, 1, 3, 3, 3\\}$ |
| $2$ | $\\{0, 1, 3\\}$    | $\\{1, 1, \underline{2}, 3, 3\\}$       |
| $3$ | $\\{1\\}$          | $\\{\underline{0}, 1, 2, 3, 3\\}$       |
| $4$ | $\\{0, 1, 2, 3\\}$ | $\\{3, \underline{4}\\}$                |

After the fourth operation, the value $4$ is included in $S$. It can be shown the $4$ operations is minimum.

### Constraints

* $1 \le n \le 50$
* $0 \le f_i \le 10^{16}$

### Subtasks

1. (5 points): $n \le 2$
2. (17 points): $n \le 20$
3. (7 points): $f_i = 0$
4. (9 points): $f_i \le 1$
5. (20 points): $f_i \le 2000$
6. (9 points): $f_0 \le 10^{16}$ and $f_j = 0$ (for all $j \neq 0$)
7. (10 points): There exists a value $i$ for which $f_i \le 10^{16}$ and $f_j = 0$ (for all $j \neq i$)
8. (23 points): No additional constraints

---

## Solution

### Subtask #1 ($n \le 2$)

There is a total of $6$ scenarios when $n \le 2$. We can handle all of them by a few simple if conditions.

<details markdown="1"><summary>Code</summary>

```cpp
void solve()
{
    int n;
    scanf("%d",&n);
    if(n == 1){
        long long a;
        scanf("%lld",&a);
        if(a == 0)
            printf("2\n");
        else
            printf("1\n");
    }
    else if(n == 2){
        long long a ,b;
        scanf("%lld%lld",&a,&b);
        if(b >= 1){
            if(a == 0)
                printf("2\n");
            else
                printf("1\n");
        }
        else{
            if(a == 0)
                printf("4\n");
            else if(a == 1)
                printf("3\n");
            else if(a >= 2)
                printf("2\n");
        }
    }
}
```
</details>

### Subtask #2 ($n \le 20$)

We can notice that it's optimal to choose $T$ of the form $\\{0, 1, \cdots, x-1\\}$ in order to insert integer $x$ to $S$. As a result, a simple greedy solution emerges: Keep inserting $\text{mex}(S)$ by choosing $T = \\{0, 1, \cdots, \text{mex}(S)-1\\}$. Eventually, $n$ will be inserted into $S$, and the answer is then equal to the number of insertions made. This simulation can be implemented in a time complexity of $O(n \cdot ans)$ which is $O(n \cdot 2^n)$ in the worst-case scenario when $S$ is initially empty.

<details markdown="1"><summary>Code</summary>

```cpp
void solve()
{
    int n;
    scanf("%d",&n);
    vector <long long> f(n);
    for(auto&i : f)
        scanf("%lld",&i);
    f.push_back(0);
    long long ans = 0;
    while(f[n] == 0){
        int i = 0;
        while(f[i] > 0)
            f[i++]--;
        f[i]++;
        ans++;
    }
    printf("%lld\n",ans);
}
```
</details>

### Subtask #3 ($S$ is initally empty)

When we apply the greedy solution starting with an empty set, we observe that after the $i$-th insertion, the elements in set $S$ correspond to the positions of the one-bits in the binary representation of $i$. Therefore, on the $2^n$-th step, the integer $n$ will be inserted for the first time. As a result, the answer is simply $2^n$.

<details markdown="1"><summary>Code</summary>

```cpp
void solve()
{
    int n;
    scanf("%d",&n);
    vector <long long> f(n);
    for(auto&i : f)
        scanf("%lld",&i);
    printf("%lld\n",1LL<<n);
}
```
</details>

### Subtask #4 ($S$ has at most $1$ occurrence of each element)

Recall that, in order to insert $n$ into $S$, each element from $0$ to $n-1$ must exist at least once. Since $S$ initially has at most one instance of each element, all the existing elements will be used to insert $n$. Therefore, we only need to insert the elements that are not already present in $S$ as if the set were initially empty (subtask 3). So, the answer is $2^n - \sum_{i=0}^{n-1} 2^i \cdot f_i$.

<details markdown="1"><summary>Code</summary>

```cpp
void solve()
{
    int n;
    scanf("%d",&n);
    vector <long long> f(n);
    for(auto&i : f)
        scanf("%lld",&i);
    long long ans = 1LL<<n;
    for(int i = 0; i < n; i++)
        ans -= f[i]<<i;
    printf("%lld\n",ans);
}
```
</details>

### Subtask #5 ($S$ has at most $2000$ occurrences of each element)

Let's do a clever optimizaton on the simulation in subtask 2. From subtask 3, we know that inserting an instance of value of $i$ takes $2^i$ operations if there are no elements with value $j$ where $j < i$ in $S$. We will exploit this fact to do much less work in the simulation. 

If $f$ consists of a prefix of $0$'s of length $p$, then we need $2^0 + 2^1 + \ldots + 2^p = 2^{p+1}-1$ operations to fill that prefix with $1$'s (or in other words, to insert the values $0, 1, \ldots, p$ to $S$). After that, we do an operation with the newly inserted instances to insert a value greater than or equal to $p$. As a result, the prefix of length $p$ becomes full of $0$'s again, and it may even grow in length since more values in $f$ decrease by each step. 

The idea is to keep track of $p$ and instead of simulating $2^{p+1}-1$ operations (where we insert $0, 1, \ldots, p$), we only account for their effect on $f$ and the answer. This simulation works in $O(n \cdot \sum_{i=0}^{n-1} f_i)$.

<details markdown="1"><summary>Code</summary>

```cpp
void solve()
{
    int n ,p = 0;
    scanf("%d",&n);
    vector <long long> f(n);
    for(auto&i : f)
        scanf("%lld",&i);
    f.push_back(0);
    long long ans = 0;
    while(f[n] == 0){
        while(f[p] == 0 && p != n)
            p++;
        int i = p;
        while(f[i] > 0)
            f[i++]--;
        f[i]++;
        ans += 1LL<<p;
    }
    printf("%lld\n",ans);
}
```
</details>

### Subtask #6 ($S$ consists of only zeros)

From [subtask #3](#subtask-3-s-is-initally-empty), it becomes clear that half of the insertions are needed to insert a zero entry. So, we can save up some operations by using the existing zeros. We can save exactly $\min(f_i, 2^{n-1})$ operations. As a result, the answer is $2^n - \min(f_i, 2^{n-1})$.

<details markdown="1"><summary>Code</summary>

```cpp
void solve()
{
    int n;
    scanf("%d",&n);
    vector <long long> f(n);
    for(auto&i : f)
        scanf("%lld",&i);
    f[0] = min(f[0] ,1LL<<n-1);
    printf("%lld\n",(1LL<<n) - f[0]);
}
```
</details>

### Subtask #7 ($S$ consists of one distinct element)

Similarly to subtask #6, we can save operations by utilizing the elements present in $S$. Let $x$ be the value of the element in $S$. Then, we can see that we need at most $2^{n-x-1}$ instances (check yourself when $x = 0, 1, \dots$) of $x$ to insert $n$ into $S$. As a result, the answer is $2^n - \min(f_i, 2^{n-x-1})$.

<details markdown="1"><summary>Code</summary>

```cpp
void solve()
{
    int n;
    scanf("%d",&n);
    vector <long long> f(n);
    for(auto&i : f)
        scanf("%lld",&i);
    int i = 0;
    while(i+1 < n && f[i] == 0)
        i++;
    f[i] = min(f[i] ,1LL<<n-i-1);
    printf("%lld\n",(1LL<<n) - (f[i]<<i));
}
```
</details>

### Subtask #8 (Full solution)

Recall that it's optimal to choose $T$ of the form $\\{0, 1, \cdots, x-1\\}$ in order to insert the value $x$ to $S$. Let $q_x$ denote the number of times we choose a subset that yields a $\text{mex}$ value equal to $x$. By definition, it follows that the answer is $\sum_{i=0}^{n} q_i$. Additionally, $q_n = 1$ since once $n$ is inserted, no further operations are needed. When $S$ is empty, we have $q_i = \sum_{j=i+1}^{n} q_j = 2^{n-i-1}$, because we need at least $q_j$ instances of $i$ to create $q_j$ instances of $j$ (for $j > i$). The same logic applies when $S$ is not empty. We know that we need at least $\sum_{j=i+1}^{n} q_j$ instances of $i$, but since we already have $f_i$ instances in $S$, we only need to create $q_i = \max(\sum_{j=i+1}^{n} q_j - f_i ,0)$ new instances of $i$.

<details markdown="1"><summary>Code $O(n^2)$</summary>

```cpp
void solve()
{
    int n;
    scanf("%d",&n);
    vector <long long> f(n) ,q(n+1);
    for(auto&i : f)
        scanf("%lld",&i);
    q[n] = 1;
    for(int i = n - 1; i >= 0; i--){
        q[i] = accumulate(q.begin() ,q.end() ,0LL);
        q[i] = max(q[i] - f[i] ,0LL);
    }
    printf("%lld\n",accumulate(q.begin() ,q.end() ,0LL));
}
```
</details>

<details markdown="1"><summary>Code $O(n)$</summary>

```cpp
void solve()
{
    int n;
    scanf("%d",&n);
    vector <long long> f(n);
    for(auto&i : f)
        scanf("%lld",&i);
    long long ans = 1;
    for(int i = n - 1; i >= 0; i--)
        ans += max(ans - f[i] ,0LL);
    printf("%lld\n",ans);
}
```
</details>

---

## Bonus

* Show that in [subtask #2](#subtask-2-n-le-20), the time complexity is $O(2^n)$ not just $O(n \cdot 2^n)$.
* Solve the problem for $n \le 10^5$ while calculating the answer modulo some number.
* Solve the problem for $n \le 10^{18}$ but instead of the input $f_i$ being given for **all** $0 \le i \le n-1$, you only recieve pairs $(i, f_i)$ for **some** $0 \le i \le n-1$, and the remaining elements have a frequency of zero.

---

## References

[https://stats.egoi.org/tasks/subsetmex/](https://stats.egoi.org/tasks/subsetmex/)

[https://dmoj.ca/problem/egoi22p1](https://dmoj.ca/problem/egoi22p1)

[https://codeforces.com/gym/104229/problem/A](https://codeforces.com/gym/104229/problem/A)