---
title: "최장 공통 문자열"
last_modified_at: 2022-10-07T07:04:00+09:00
categories:
  - Algorithm
tags:
  - Algorithm
  - String
  - DP
---

# 최장공통문자열

최장공통문자열은 두 문자열을 비교하였을 때, 공통되는 문자열의 최대 길이를 구하는 문제.

연속해서 같은 문자열을 세는 문제와 연속하지 않더라도 같은 경우를 세는 문제가 있다.

둘 모두 DP문제로, 이전에 최장길이를 저장해두고 현재에 더 길어졌는지 확인하는 방식으로 풀 수 있다.

연속해서 같은 문자열을 세는 문제

DP[X][Y] := if STR[X] == STR[Y] then DP[X-1][Y-1]+1 else 0

연속하지 않더라도 같은 경우를 세는 문제

DP[X][Y] := if STR[X] == STR[Y] then DP[X-1][Y-1]+1 else MAX(DP[X-1][Y], DP[X][Y-1]

### C++ Algorithm(BOJ9252)

```cpp
#include <iostream>
#include <memory.h>

using namespace std;

int main()
{
    string A,B;
    cin >> A >> B;
    int** compareArr = new int*[B.length()+1];
    for(int i=0;i<B.length()+1;i++)
    {
        compareArr[i] = new int[A.length()+1];
        memset(compareArr[i], 0, sizeof(int)*(A.length()+1));
    }
    
    for(int i=1;i<B.length()+1;i++)
    {
        for(int j=1;j<A.length()+1;j++)
        {
            int maxVal = max(compareArr[i-1][j], compareArr[i][j-1]);
            if(B[i-1] == A[j-1])
            {
                compareArr[i][j] = max(maxVal, compareArr[i-1][j-1]+1);
            }
            else
            {
                compareArr[i][j] = maxVal;
            }
        }
    }

    int cnt = compareArr[B.length()][A.length()];
    cout << cnt << endl;
    int lastX = A.length();
    int lastY = B.length();
    string dataList;
    for(int i=0;i<cnt;i++)
    {
        for(;lastX>1;lastX--)
        {
            if(compareArr[lastY][lastX] != compareArr[lastY][lastX-1])
            {
                break;
            }
        }
        for(;lastY>1;lastY--)
        {
            if(compareArr[lastY][lastX] != compareArr[lastY-1][lastX])
            {
                break;
            }
        }
        dataList+=B[lastY-1];
        lastY -=1;
        lastX -=1;
    }

    for(int i=dataList.length()-1;i>=0;i--)
    {
        cout << dataList[i];
    }
    
    for(int i=0;i<B.length();i++)
    {
        delete[] compareArr[i];
    }
    delete [] compareArr;
}
```