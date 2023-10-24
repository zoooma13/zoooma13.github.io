---
title: Irreducible Permutation
date: 2023-10-09 12:00:00 +0900
categories: [Authored Tasks, KAIST 13th ICPC Mock Competition 2023]
tags: [ad-hoc, kaist, icpc, '2023', solution]
math: true
---

You can find the full statement on [acmicpc.net](https://www.acmicpc.net/problem/30297), where you can also submit your code.

---

## Description

### Brief Statement

A permutation is called irreducible if none of its prefixes forms a permutation, except for the whole permutation itself. For example, $[2,3,1]$ and $[4,1,2,3]$ are irreducible while $[2,1,3]$ and $[1,3,2]$ are not.

You are given a permutation $P$ of length $n$. In one operation, you can choose any two adjacent indices and swap their values. 

Find the minimum number, and the corresponding sequence, of operations to transform $P$ into an irreducible permutation.

### Example

#### Example 1
When $n = 4$ and $P = [1, 2, 3, 4]$, all the prefixes form permutations. Thus, we can do the following $3$ operations to make $P$ irreducible: $[1, 2, 3, 4] \xrightarrow{\text{swap}(P_1, P_2)} [2, 1, 3, 4] \xrightarrow{\text{swap}(P_2, P_3)} [2, 3, 1, 4] \xrightarrow{\text{swap}(P_3, P_4)} [2, 3, 4, 1]$. 

#### Example 2
When $n = 5$ and $P = [3, 1, 2, 4, 5]$, the prefixes $[3, 1, 2]$ and $[3, 1, 2, 4]$ form permutations. Thus, we can do the following $2$ operations to make $P$ irreducible: $[3, 1, 2, 4, 5] \xrightarrow{\text{swap}(P_4, P_5)} [3, 1, 2, 5, 4] \xrightarrow{\text{swap}(P_3, P_4)} [3, 1, 5, 2, 4]$. 

### Constraints

* $1 \le n \le 10^5$
* $1 \le P_i \le n$

---

## Solution

Notice how, if $P[1, i]$ forms a permutation, swapping any elements $P_j$ and $P_{j+1}$ where $j \neq i$ will not change the set of elements in $P[1, i]$, and thus it will remain a permutation after any number of swaps. In contrast, if we swap $P_i$ and $P_{i+1}$ once, it breaks the presumed permutation $P[1, i]$.

As a result, it's necessary and sufficient to only swap $P_i$ and $P_{i+1}$ for all indices $i$ ($1 \le i \lt n$) such that $P[1, i]$ forms a permutation. To determine if $P[1, i]$ forms a permutation for all possible values of $i$, simply, check if $\max P[1, i]$ is equal to $i$. Since there are $i$ elements and they're all distinct, if they form a permutation, the maximum must be equal to $i$.

<details markdown="1"><summary>Code</summary>

```cpp
void solve()
{
    int n;
    scanf("%d",&n);
    vector <int> a(n);
    for(int&i : a)
        scanf("%d",&i);

    int mx = 0;
    vector <int> ans;
    for(int i = 0; i < n-1; i++){
        mx = max(mx ,a[i]);
        if(mx == i+1)
            ans.push_back(i+1);
    }
    
    printf("%d\n",ans.size());
    for(int&i : ans)
        printf("%d ",i);
    printf("\n");
}
```
</details>

---

## References

[https://www.acmicpc.net/problem/30297](https://www.acmicpc.net/problem/30297)