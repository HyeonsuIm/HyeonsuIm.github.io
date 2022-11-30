---
layout: default
parent: algorithm
nav_order: 3
title: "최소 신장 트리"
last_modified_at: 2022-09-18T10:33:00+09:00
categories:
  - Algorithm
tags:
  - Algorithm
  - Tree
  - Graph
---

# 최소 신장 트리

최소 신장 트리란 그래프에서 모든 정점을 연결하는 최소한의 간선만을 남긴 것을 말한다.

도로 건설 / 네트워크 연결 등을 최소한의 비용으로 모든 정점을 연결하기 위해서 사용하는 알고리즘으로, 이를 위해 일반적으로 최소 가중치 트리를 구성하기 위해 사용된다.

최소한의 간선만을 남기기 때문에 아래의 조건이 성립한다.

1. N개의 정점과 각 정점을 연결하는 N-1개의 간선이 남는다.
2. 순환이 발생하지 않는, 트리 형태로 구성된다.

알고리즘에는 Prim알고리즘과 크루스칼 알고리즘이 있으며, 두 방법은 유사하나 간선을 연결하는 순서에 차이가 있다.

**Prim 알고리즘 :**

BFS방식으로 트리를 구성하되, 순환을 제외시킨다.

1. 빈 트리를 생성한다.
2. 하나의 노드를 선택하여 간선들을 리스트에 삽입한다.
3. 간선 리스트에서 가중치가 가장 작은 간선을 트리에 추가하고, 다음 노드의 간선들을 리스트에 삽입한다.
    1. 가장 작은 간선 선택 시, 다음 간선이 이미 사용된 경우 순환이 발생하므로 제외한다.
4. 추가 간선이 없을때까지 3의 과정을 반복한다.

### C++ Algorithm(BOJ 1197)

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <functional>
#include <memory.h>
using namespace std;

long long GetMinimumSpanningWeight(
    vector<vector<pair<int,int>>>& graph)
{
    long long totalWeight = 0;
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
    bool* isUsedNode = new bool[graph.size()];
    memset(isUsedNode, 0, sizeof(bool)*graph.size());
    pq.push(make_pair(0, 0));

    while(pq.empty() == false)
    {
        int weight = pq.top().first;
        int position = pq.top().second;
        pq.pop();
        if( isUsedNode[position] == true )
        {
            continue;
        }
        totalWeight += weight;
        isUsedNode[position]=true;
        
        vector<pair<int,int>>& edges = graph[position];
        for(const pair<int,int>& edge : edges)
        {
            if( isUsedNode[edge.first] == false )
            {
                pq.push(make_pair(edge.second,edge.first));
            }
        }         
    }
    delete[] isUsedNode;
    return static_cast<int>(totalWeight);
}

int main()
{
    int V, E;
    cin >> V >> E;
    vector<vector<pair<int,int>>> graph(V);
    for(int i=0;i<E;i++)
    {
        int from,to,weight;
        cin >> from >> to >> weight;
        graph[from-1].push_back(make_pair(to-1, weight));
        graph[to-1].push_back(make_pair(from-1, weight));
    }
    cout << GetMinimumSpanningWeight(graph);
}
```

**크루스칼 알고리즘:** 

탐욕적인 방법으로 최소 가중치의 간선을 계속해서 선택하고, 순환을 제외시킨다.

1. 간선리스트를 생성한다.
2. 가중치가 가장 작은 간선을 트리에 추가한다.
    1. 간선 추가 시, 분리집합 알고리즘을 사용하여 순환발생을 배제한다.
        1. 간선에 연결된 정점들에 집합 번호 매긴다.
        2. 연결된 두 정점 중 하나가 이미 집합을 가진 경우, 해당 집합을 따라간다.
        3. 연결된 두 정점이 서로 다른 집합일 경우 두 집합을 합친다.
        4. 연결된 두 정점이 서로 같은 집합일 경우 순환이 발생하므로 트리에 추가하지 않는다.
3. 추가 간선이 없을때까지 2의 과정을 반복한다.

### C++ Algorithm(BOJ1647)

```cpp
#include <iostream>
#include <vector>
#include <tuple>
#include <algorithm>

using namespace std;
int parentIdx[100001];

int GetParent(int node)
{
	if(parentIdx[node] == 0) return node;
	else return parentIdx[node] = GetParent(parentIdx[node]);
}

bool IsSameParent(int node1, int node2)
{
	int parent1 = GetParent(node1);
	int parent2 = GetParent(node2);

	if( parent1 == parent2 && parent1 != 0) return true;
	else return false;
}

bool UnionSet(int node1, int node2)
{
	int parent1 = GetParent(node1);
	int parent2 = GetParent(node2);

	parentIdx[parent1] = parent2;
	return true;
}

int main()
{
    int N, M;
    cin >> N >> M;
	vector<tuple<int, int, int>> edgeList(M);
    for(int i=0;i<M;i++)
    {
		int A,B,C;
		cin >> A >> B >> C;
		edgeList[i]=make_tuple(C,A,B);
    }

	sort(edgeList.begin(), edgeList.end());

	long long maxWeight=0;
	int lastWeight=0;
	for(tuple<int,int,int>& edge : edgeList)
	{
		if(IsSameParent(get<1>(edge), get<2>(edge)) == false)
		{
			if(UnionSet(get<1>(edge), get<2>(edge)))
			{
				lastWeight = get<0>(edge);
				maxWeight += lastWeight;
			}
		}
	}
	cout << maxWeight - lastWeight << endl;
}
```