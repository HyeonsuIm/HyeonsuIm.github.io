---
layout: default
parent: algorithm
nav_order: 3
title: "그리디"
last_modified_at: 2022-10-07T07:05:00+09:00
categories:
  - Algorithm
tags:
  - Algorithm
  - Gridy
---

# 그리디 알고리즘

그리디 알고리즘은 탐욕 알고리즘으로, 탐욕적인 방법으로 현재 상황에서의 최고의 선택지를 계속해서 선택해 나감으로써 문제의 해를 구하는 방식이다.

이러한 방식은 일반적으로 최적의 해를 구하지 못하고, 최소한의 시간으로 최적에 가까운 해를 구하게 된다. 하지만 현재의 최고의 선택지가 최고의 해가 될 수 있는 방법이 있다면 결과적으로 그리디 알고리즘으로 최적의 해를 찾을 수 있다.

### C++ Algorithm(BOJ1202)

```cpp
#include <iostream>
#include <vector>
#include <functional>
#include <queue>
using namespace std;

int main()
{
    int N,K;
    cin >> N >> K;

    vector<pair<int,int>> jewelList(N);
    for(int i=0;i<N;i++)
    {
        cin >> jewelList[i].first >> jewelList[i].second;
    }

    sort(jewelList.begin(), jewelList.end());

    vector<int> begSizes(K);
    for(int i=0;i<K;i++)
    {
        cin >> begSizes[i];
    }
    sort(begSizes.begin(), begSizes.end());
    long long costs = 0;
    int j=0;
    int jEnd = jewelList.size();
    priority_queue<int, vector<int>> pq;
    for(int i=0;i<begSizes.size();i++)
    {
        while(begSizes[i] >= jewelList[j].first && j != jEnd)
        {
            pq.push(jewelList[j].second);
            j+=1;
        }
        if(!pq.empty())
        {
            costs += pq.top();
            pq.pop();
        }
    }
    cout << costs;
}
```