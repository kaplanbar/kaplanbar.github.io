---
title: "[CF] 258E"
date: 2021-06-27
lastmod: 
author: Yilmaz Baris Kaplan

description: 
categories: [empty]
tags: [empty]


draft: false
enableDisqus : true
enableMathJax: true
disableToC: false
disableAutoCollapse: true
---

I will share a solution to the problem [CF 258E.](https://codeforces.com/contest/451/problem/E)

## Short statement
You are given \\(n\\) boxes where \\(i\\)-th box contains \\(f_i\\) flowers. All flowers in a box are same color (indistinguishable) and there are no two flowers in the different boxes that have the same color.

We want to select exactly \\(s\\) flowers from those boxes. Find out how many different ways to select in modulo \\(10^9 + 7\\).

\\(n \leq 20\\), \\(f_i \leq 10^{12}\\) and \\(s \leq 10^{14}\\).

## Solution

Let \\(x_i\\) be the number of flowers taken from the \\(i\\)-th box. Then, we will have the following equation:

$$
x_1 + x_2 + x_3 + \dots + x_n = s,
$$

where \\(0 \leq x_i \leq f_i\\).

Assume that we did not have the constraint on \\(f_i\\). Then we could calculate answer as \\(\binom{s + n - 1}{n - 1}\\).

Since we have constraint on the maximum number of flowers we can take from the \\(i\\)-th box, the equation above contains cases where we took more than we could from some boxes. We can reach the answer by subtracting such cases.

Assume that we took more flowers than we can for the boxes \\(b_i \in B \\). Then the number ways we have counted where we took more flowers than we can will be equal to \\(\binom{(s - \sum (f_{b_i} + 1)) + n - 1}{n - 1}\\). We can think this formula as we preserved \\( f_{b_i} + 1 \\) flowers for the \\(i\\)-th box so that it is guaranteed to exceed \\(f_i\\).

Using that formula, we can calculate the number of cases where we took more flowers than we could and subtract it from the total number of cases.

You can find the C++ code for the solution described above.

```c++

#include <bits/stdc++.h>
using namespace std;
using ll = long long;
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(0);

    const int mod = 1e9 + 7;

    int n;
    ll s;
    cin >> n >> s;

    vector<ll> v(n);
    for(int i = 0; i < n; i++) cin >> v[i];

    auto fast_pw = [&](ll x, ll y) -> ll {
        ll ret = 1;
        for(;y;y>>=1) {
            if(y&1) ret = (ret * x) % mod;
            x = (x * x) % mod;
        }
        return ret;
    };
    vector<int> inv(n + 1);
    for(int i = 1; i <= n; i++) {
        inv[i] = fast_pw(i, mod - 2);
    }

    auto comb = [&](ll x, int y) -> ll {
        assert(x >= y);
        ll ret = 1;
        for(int i = 0; i < y; i++) {
            ret = (ret * ((x - i) % mod)) % mod;
            ret = (ret * inv[i + 1]) % mod;
        }
        return ret;
    };

    ll ans = 0;

    for(int i = 0; i < (1 << n); i++) {
        ll cur = s;

        int c = 0;

        for(int j = 0; j < n; j++) {
            if(i >> j & 1) {
                cur -= v[j] + 1;
                c++;
            }
            if(cur < 0) break;
        }
        if(cur < 0) continue;

        if(c & 1) ans = (ans - comb(cur + n - 1, n - 1) + mod) % mod;
        else ans = (ans + comb(cur + n - 1, n - 1)) % mod;
    
    }

    cout << ans;


    return 0;
}

```

