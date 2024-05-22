---
title: "Editorial: [CF] 1967B2"
date: 2024-05-22
lastmod: 
author: Yilmaz Baris Kaplan

description: 
categories: [Competitive Programming, Mathematics]
tags: [Codeforces, Editorial]


draft: false
enableDisqus : true
enableMathJax: true
disableToC: false
disableAutoCollapse: true
---

I will share a solution to the problem [CF 1967B2](https://codeforces.com/contest/1967/problem/B2)

I found it as a nice mathematical problem.

## Short statement
Problem statement itself is already quite short, so no summary is needed.

You are given two positive integers \\n, m\\.

Calculate the number of ordered pairs \\(a, b)\\ satisfying the following conditions:

- \\1 \leq a \leq n, 1 \leq b \leq m.
- \\b \times gcd(a, b)\\ is a multiple of \\a + b\\.

## Solution

Let \\x = a + b\\

Then, we have the equation:
$$
x \mid b \times gcd(a, b) 
$$

Note that \\gcd(a,b) = gcd(a + b, b) = gcd(x, b)\\

So we have the equation:
$$
x \mid b \times gcd(x, b)
$$

What does that mean? Actually, if we think about that \\b = k \times gcd(x, b)\\ for some \\k\\ given that \\gcd(x, k) = 1\\. So, we have the following:

$$
x \mid k \times gcd(x, b) \ times gcd(x, b)
x \mid k \times gcd(x, b)^2
x \mid gcd(x, b)^2
$$

This gives us a nice property about the relation between \\x\\ and \\b\\. If we think about that, only way to satisfy that equation is for \\b\\ to have all prime factors of \\x\\ with at least of half of their powers.

So we can use that to brute force on all possible \\x\\'s. Then, find prime factors of that \\x\\ in \\O(logN)\\ and calculate how many \\b\\ are possible.

To calculate how many of \\b\\ are possible, we just need to take into account that

1. \\b \leq m\\
2. \\b < x - n\\ so that \\1 \leq a \leq n\\ is satisfied.


You can find the C++ code for the solution described above.

```c++
#include <algorithm>
#include <iostream>
#include <cassert>
#include <climits>
#include <cstdlib>
#include <cstring>
#include <iomanip>
#include <numeric>
#include <cstdio>
#include <string>
#include <vector>
#include <cmath>
#include <ctime>
#include <queue>
#include <stack>
#include <map>
#include <set>
#include <unordered_set>
#include <unordered_map>
#include <bitset>

using namespace std;

using ll = long long;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    const int N = 4 * 1e6 + 10;
    vector<int> lsp(N, 1);
    for(int i = 2; i < N; i++) {
        if(lsp[i] == 1) {
            for(int j = i; j < N; j += i) {
                lsp[j] = i;
            }
        }
    }
    int t;
    cin >> t;
    while(t--) {
        int n, m;
        cin >> n >> m;

        ll ans = 0;

        auto fast_pw = [&](ll a, ll b) {
            ll res = 1;
            while(b) {
                if(b & 1) {
                    res = (res * a);
                }
                a = (a * a);
                b >>= 1;
            }
            return res;
        };

        auto calculate = [&](ll x, ll divisor) {
            ll all = min(x - 1, 1ll * m) / divisor;
            ll big_a = max(0ll, min(x - n - 1, min(x - 1, 1ll * m))) / divisor;
            return all - big_a;
        };

        auto find_prime_factors = [&](ll x) {
            ll orig = x;
            ll num = 1;
            while(x > 1) {
                int c = 0;
                int p = lsp[x];
                while(x % p == 0) {
                    x /= p;
                    c += 1;
                }
                num *= fast_pw(p, (c + 1) / 2);
            }
            return calculate(orig, num);
        };

        for(int i = 2; i <= n + m; i++) {
            ans += find_prime_factors(i);
        }

        cout << ans << '\n';

    }
    return 0;
}

```

