---
title: "위상 정렬"
last_modified_at: 2022-09-20T19:45:00+09:00
categories:
  - Algorithm
tags:
  - Algorithm
  - Graph
---

# 위상 정렬

위상 정렬이란 실행 순서를 결정해주기 위한 알고리즘. 어떤 작업 이전에 선행되어야 하는 작업들이 있는 경우, 작업들의 순서를 정하고 종료까지의 시간을 구하는 데 사용 됨.

그래프로 구성된 데이터에 대해 사용 된다.

예시 :

![Untitled](/assets/images/Topological_sort.png)

사용 :

- 작업 순서를 정하거나, 동시 작업 시 걸리는 최대 시간을 구할 때 사용 됨.
- 방향 그래프로 구성된 데이터에 대해 사용 됨.

알고리즘 순서 :

1. 각 정점으로 진입하는 간선들의 갯수를 센다.
2. 진입 간선이 없는 정점들을 큐에 삽입한다.
3. 큐에서 하나씩 꺼내어 해당 정점의 진출 간선들을 제거하고, 꺼내진 정점은 별도의 큐에 저장한다.
    1. 시간 계산이 필요할 경우, 간선제거 시 각 정점까지의 최소/최대 시간을 계산할 수 있다.
4. 2~3 내용을 큐에 정점이 없을 때까지 반복한다.
5. 별도의 큐에 적재된 순서대로 작업 실행할 수 있다.

### C++ Algorithm(BOJ 1005)

```cpp
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

void nodeOrdering(const vector<int>& buildingBuildTime, const vector<vector<int>>& graph, vector<int> inboundEdgeCnts, vector<int>& buildingMaxTime)
{
    int currentTime = 0;
    queue<int> q;

    // 진입 edge가 없는 경우 queue에 삽입
    int listSize = inboundEdgeCnts.size();
    for( int i=0;i<listSize;i++)
    {
        if(inboundEdgeCnts[i] == 0)
        {
            buildingMaxTime[i] = buildingBuildTime[i];
            q.push(i);
        }
    }

    // queue가 빌 때까지 반복작업
    while(false == q.empty())
    {
        int node = q.front();
        q.pop();

        const vector<int>& nextNodes = graph[node];
        for(int nextNode : nextNodes)
        {
            buildingMaxTime[nextNode] = max(buildingBuildTime[nextNode] + buildingMaxTime[node], buildingMaxTime[nextNode]);
            inboundEdgeCnts[nextNode] -= 1;
            if(inboundEdgeCnts[nextNode]==0)
            {
                q.push(nextNode);
            }
        }
    }
}

int PlayGame()
{
    int buildingCnt, ruleCnt;
    cin >> buildingCnt >> ruleCnt;
    vector<int> buildingBuildTime(buildingCnt);
    vector<vector<int>> graph(buildingCnt);
    vector<int> inboundEdgeCnt(buildingCnt);
    for(int i=0;i<buildingCnt;i++)
    {
        int time;
        cin >> time;
        buildingBuildTime[i] = time;
    }

    // 그래프를 구성하고, 노드로 진입하는 edge수를 센다.
    for(int i=0;i<ruleCnt;i++)
    {
        int buildOrderFrom, buildOrderTo;
        cin >> buildOrderFrom >> buildOrderTo;
        graph[buildOrderFrom-1].push_back(buildOrderTo-1);
        inboundEdgeCnt[buildOrderTo-1]+=1;
    }
    int goal;
    cin >> goal;
    vector<int> buildingMaxTime(buildingCnt);
    // node들에 대한 반복 작업
    nodeOrdering(buildingBuildTime, graph, inboundEdgeCnt, buildingMaxTime);
    
    // 목표의 최대 시간 반환
    return buildingMaxTime[goal-1];
}

int main()
{
    int testCnt;
    cin >> testCnt;
    for( int i=0;i<testCnt;i++)
    {
        printf("%d\n",PlayGame());
    }
}
```