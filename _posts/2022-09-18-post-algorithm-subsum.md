---
title: "부분 합"
last_modified_at: 2022-09-18T22:38:00+09:00
categories:
  - Algorithm
tags:
  - Algorithm
  - Array
---

# 부분합

부분 합 알고리즘은 특정 수열에서 부분의 합이 특정조건을 만족하는 경우를 찾는다.

일반적으로 아래와 같은 방법들을 사용한다.

1. 0~i까지를 더한 정보를 저장한 배열 A를 만들고, A[i] - A[j]가 조건을 만족하는지 판단하여 해결한다.

### C++ Algorithm(BOJ1806)

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main()
{
    int N, S;
    cin >> N >> S;
    vector<int> datas(N);
    vector<long long> sum(N+1, 0);
    for(int i=0;i<N;i++)
    {
        cin >> datas[i];
        sum[i+1] = datas[i] + sum[i];
    }

    int minLength = 100000;
    int startIdx = 0;
    for(int i=1;i<N+1;i++)
    {
        for(;startIdx<=i;startIdx++)
        {
            if(sum[i]-sum[startIdx] < S)
            {
                break;
            }
            else if(sum[i]-sum[startIdx] >= S)
            {
                minLength = min(minLength, i-startIdx);
                continue;
            }
        }
    }
    if(minLength == 100000)
    {
        cout << 0;
    }
    else
    {
        cout << minLength;
    }
}
```