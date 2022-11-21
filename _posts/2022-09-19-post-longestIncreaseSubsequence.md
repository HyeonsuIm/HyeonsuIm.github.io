---
title: "최장 증가 수열"
last_modified_at: 2022-09-24T11:51:00+09:00
categories:
  - Algorithm
tags:
  - Algorithm
  - DP
  - Dynamic Programming
---

# 가장 긴 증가하는 수열

배열 안에서 수열을 찾아야 하는 알고리즘 문제로, 가장 긴 증가하는 수열을 찾아야 한다.

아래와 같은 수식으로 구할 수 있다.

$DP[X]=MAX(DP[J])+1 IF(ARR[J]<ARR[X]), 0<=J<X$

(이전에 있는 DP값들 중 , 자신보다 작은 DP값의 최댓값 + 1)

알고리즘 :

1. 이전 값들 중 현재 값보다 작은 값의 cnt를 구하고, 현재 위치에 +1한다.
2. 1을 리스트 갯수만큼 반복한다.

위와 같은 알고리즘으로 기본적인 문제는 풀 수 있으나, 많은 데이터를 처리해야 하는 심화 문제에서는 시간초과가 발생하게 된다.

이에 더 성능을 최적화하여, 비교 횟수를 줄인 알고리즘을 사용하여야 한다.

$DP[X] = 증가하는 수열 갯수가 X인것 중 가장 작은 값$

알고리즘:

1. DP배열에서 현재 값보다 큰 값중 가장 작은 값을 구한다.
    1. 만약 배열 최대값보다 크다면, 배열을 한칸 추가한다.
2. 해당 지점에 현재 값을 넣는다.
3. 1~2를 리스트 개수만큼 반복한다.

### C++ Algorithm(BOJ12015)

```cpp
#include <iostream>
#include <vector>

using namespace std;

int binary_search(vector<int>& maxVal4EachCnt, int val)
{
    if(maxVal4EachCnt.empty())
    {
        return -1;
    }
    else if(maxVal4EachCnt.back() < val)
    {
        return -1;
    }
    else
    {
        int position = 0x7fffffff;
        int start = 0;
        int end = maxVal4EachCnt.size()-1;
        int mid;
        while(start<=end)
        {
            mid = (start+end)/2;
            if(maxVal4EachCnt[mid]<val)
            {
                start=mid+1;
            }
            else
            {
                position = min(position,mid);
                end=mid-1;
            }
        }
        return position;
    }
}

int main()
{
    int N;
    cin >> N;
    vector<int> val(N);
    for(int i=0;i<N;i++)
    {
        cin >> val[i];
    }

    vector<int> maxVal4EachCnt;
    for(int i=0;i<N;i++)
    {
        int position = binary_search(maxVal4EachCnt, val[i]);
        if(position == -1)
        {
            maxVal4EachCnt.push_back(val[i]);
        }
        else
        {
            maxVal4EachCnt[position] = val[i];
        }
    }
    cout << maxVal4EachCnt.size();
}
```