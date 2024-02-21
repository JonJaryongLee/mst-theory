# 6. MST

# 1. MST

신장 트리 (Spanning Tree, ST)

그래프에서,

1. 모든 node 포함
2. edge 의 갯수는 N - 1
3. 사이클 없음

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled.png)

이런 그래프를 말한다. 모든 node 가 이어져있고, edge 의 갯수는 3 - 1 = 2 이며, 사이클이 없다.

2와 3은 1을 통해 서로 연결되어 있다고 본다.

한 그래프에서 Spanning Tree 는 여러 개 있을 수 있다.

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%201.png)

최소 신장 트리 (Minimum Spanning Tree, MST)

cost 의 합이 가장 작도록 연결

왜 쓸까? 우리가 각 도시에 수도 파이프를 연결한다고 가정하자. 파이프를 연결하는 방법은 많지만, 파이프에 쓰는 비용은 최소한이 되어야 할 것이다. 어차피 모든 도시가 파이프로 이어져 있다면, 물은 공급될 것이기 때문이다.

MST 구현을 위해 두 가지 알고리즘 중 선택한다.

1. edge 의 갯수가 적다 ⇒ Kruskal
2. edge 의 갯수가 많다 ⇒ Prim

두 알고리즘 모두 가장 합리적인 선택을 한다는 점에 있어, Greedy 의 일종이다.

# 2. Kruskal

## 2-1. 이론

전체 edge 중, 최소 cost 의 edge 를 선택해서 새로운 그래프 형성 (Union)

단, 사이클이 발생하는지 체크해야 한다. (Find)

즉, Union-Find 를 사용한다.

과정은 다음과 같다.

1. 전체 node 만 있고, edge 는 없는 그래프에서 시작한다.
2. cost 순서로 edge 를 오름차순 정렬한다.
3. Find 연산으로 사이클이 발생하는지 확인 (루트가 같은지)
    1. 사이클이면 무시하고 넘어감
    2. 사이클이 아니라면, Union
4. 3번을 반복하다, edge 의 수가 n-1 이 되면 종료

그림으로 표현하면 다음과 같다.

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%202.png)

위와 같은 그래프가 주어졌을 때,

1. 전체 node 만 있고, edge 는 없는 그래프에서 시작한다.

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%203.png)

1. cost 순서로 edge 를 오름차순 정렬한다.
    
    ⇒ 1 1 2 5 8
    
2. Find 연산으로 사이클이 발생하는지 확인 (루트가 같은지)
    1. 사이클이면 무시하고 넘어감
    2. 사이클이 아니라면, Union

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%204.png)

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%205.png)

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%206.png)

1. 3번을 반복하다, edge 의 수가 n-1 이 되면 종료

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%207.png)

Minimum Cost :  1 + 1 + 5 = 7

## 2-2. 구현

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

struct Edge
{
    int a;
    int b;
    int cost;
};

bool compare(Edge a, Edge b)
{
    return a.cost < b.cost;
}

int nodeCnt, edgeCnt;
vector<Edge> v;

int parent[4];

void init()
{
    for (int i = 0; i < 4; i++)
        parent[i] = i;
}

int find(int tar)
{
    if (tar == parent[tar])
        return tar;

    // Path Compression
    int ret = find(parent[tar]);
    parent[tar] = ret;
    return ret;
}

void setUnion(int a, int b)
{
    int t1 = find(a);
    int t2 = find(b);

    if (t1 == t2)
        return;

    parent[t2] = t1;
}

int kruskal()
{
    int result = 0;

    int selectCount = 0;

    for (Edge sel : v)
    {
        int a = sel.a;
        int b = sel.b;
        int cost = sel.cost;
        if (find(a) == find(b))
            continue;
        setUnion(a, b);
        result += cost;
        selectCount++;
        if (selectCount == nodeCnt - 1)
            break;
    }
    return result;
}

int main()
{
    // 4 5
    // 0 1 1
    // 0 2 2
    // 1 2 1
    // 1 3 5
    // 2 3 8
    // freopen("sample_input.txt", "r", stdin);
    cin >> nodeCnt >> edgeCnt;
    int a, b, cost;
    for (int i = 0; i < edgeCnt; i++)
    {
        cin >> a >> b >> cost;
        v.push_back({ a, b, cost });
    }

    init();
    sort(v.begin(), v.end(), compare);

    int result = kruskal();

    cout << result << '\n';

    return 0;
}
```

Kruscal 구현을 위해, Union Find 지식이 필요하다.

차근차근 살펴보자.

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

struct Edge
{
    int a;
    int b;
    int cost;
};
```

Edge 는 평소와는 다르게 `a` , `b` 로, 양쪽 노드를 같이 받는다. Union-Find 사용 시 둘 다 필요하기 때문이다.

```cpp
bool compare(Edge a, Edge b)
{
    return a.cost < b.cost;
}
```

커스텀 소트. edge 의 cost 를 기준으로 오름차순 정렬

```cpp
int nodeCnt, edgeCnt;
vector<Edge> v;
```

노드 갯수, 엣지 갯수이다.

그래프는 `v` 에 두겠다.

```cpp
int parent[4];

void init()
{
    for (int i = 0; i < 4; i++)
        parent[i] = i;
}

int find(int tar)
{
    if (tar == parent[tar])
        return tar;

    // Path Compression
    int ret = find(parent[tar]);
    parent[tar] = ret;
    return ret;
}

void setUnion(int a, int b)
{
    int t1 = find(a);
    int t2 = find(b);

    if (t1 == t2)
        return;

    parent[t2] = t1;
}
```

Union-Find 알고리즘. 초기화 코드는 `init()`  에 설정했다.

`kruskal()` 함수를 보기 전에, `main()` 함수를 살펴보자.

```cpp
int main()
{
    // 4 5
    // 0 1 1
    // 0 2 2
    // 1 2 1
    // 1 3 5
    // 2 3 8
    // freopen("sample_input.txt", "r", stdin);
    cin >> nodeCnt >> edgeCnt;
    int a, b, cost;
    for (int i = 0; i < edgeCnt; i++)
    {
        cin >> a >> b >> cost;
        v.push_back({a, b, cost});
    }

    init();
    sort(v.begin(), v.end(), compare);

    int result = kruskal();

    cout << result << '\n';

    return 0;
}
```

그래프를 세팅한다.

Union-Find 를 위해 `parent` 배열도 초기화하도록 `init()` 을 반드시 실행하고, 정렬도 해주자.

`kruskal()` 함수 실행 후, `result` 를 리턴받아 출력.

이제 `kruskal()` 함수를 보자.

```cpp
int kruskal()
{
    int result = 0;

    int selectCount = 0;

    for (Edge sel : v)
    {
        int a = sel.a;
        int b = sel.b;
        int cost = sel.cost;
        if (find(a) == find(b))
            continue;
        setUnion(a, b);
        result += cost;
        selectCount++;
        if (selectCount == nodeCnt - 1)
            break;
    }
    return result;
}
```

`result` : MST 의 경로의 합이며, (최소합) 함수의 리턴값

`selectCount` : edge 의 갯수를 세어나가며, `nodeCnt - 1` 이 되었을 때 루프를 종료하기 위해 쓰임

range for loop 사용

`a` 와 `b` 가 같은 집합에 속해있다 ⇒ 사이클 발생하므로 `continue`

아니라면, `setUnion()` 를 사용해 같은 집합으로 묶어주고, `result` 를 갱신하고, `selectCount` 도 올린다.

만약 `selectCount` 가 `nodeCnt - 1` 이 된다면 MST 가 완성되었으므로 `break`

이후 `result` 리턴

`result` : 7

# 3. Prim

## 3-1. 이론

연결된 node 중, 최소 cost 의 edge 선택해 나감.

node 를 선택해나가기 때문에, 기존 node 를 다시 선택할 일이 없도록 `visited` 체크를 해줘야 한다.

두 개의 그래프를 합치는 게 아니라, 하나의 그래프를 만들어가기 때문에 집합을 합칠 필요가 없으므로, Union-Find 가 필요없다.

과정은 다음과 같다.

1. 아무 node 나 선택해 시작한다.
2. 시작 node 에서 최소 cost 를 선택해 트리 구성
3. 그래프의 전체 노드 중, 가장 작은 cost 를 선택해 트리 구성
4. 과정을 반복하다, node 를 모두 방문했을 때 종료

node 를 방문하면서, 해당 node 의 edge 를 모두 PQ 에 저장해가며 구현하며, `visited` 배열이 필요하다.

`pq.top()` 시에 edge 는 가장 최소 cost 의 edge 가 나올 것이며, `visited` 를 체크해, 방문한 노드이면 `continue` 

그림으로 표현하면 다음과 같다.

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%208.png)

그래프는 Kruskal 과 동일하며, 결과도 동일할 것이다.

1. 아무 node 나 선택해 시작한다.

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%209.png)

1. 시작 node 에서 최소 cost 를 선택해 트리 구성

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%2010.png)

1. 그래프의 전체 노드 중, 가장 작은 cost 를 선택해 트리 구성

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%2011.png)

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%2012.png)

1. 과정을 반복하다, node 를 모두 방문했을 때 종료

![Untitled](6%20MST%20fbe809e3e4334431b125b7742429941a/Untitled%207.png)

Minimum Cost :  1 + 1 + 5 = 7

## 3-2. 구현

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

struct Edge
{
    int to;
    int cost;
};

struct compare
{
    bool operator()(Edge a, Edge b)
    {
        return a.cost > b.cost;
    }
};

int nodeCnt, edgeCnt;
vector<Edge> v[4];
int visited[4];

int prim(int st)
{
    priority_queue<Edge, vector<Edge>, compare> pq;
    int minCost = 0;
    int visitedNodeCnt = 0;
    visited[st] = 1;
    visitedNodeCnt++;
    for (int i = 0; i < v[st].size(); i++)
    {
        pq.push({ v[st][i].to, v[st][i].cost });
    }
    while (!pq.empty())
    {
        Edge now = pq.top();
        pq.pop();

        if (visited[now.to])
            continue;

        visited[now.to] = 1;
        visitedNodeCnt++;
        minCost += now.cost;

        if (visitedNodeCnt == nodeCnt)
            break;

        for (int i = 0; i < v[now.to].size(); i++)
        {
            pq.push({ v[now.to][i].to, v[now.to][i].cost });
        }
    }
    return minCost;
}

int main()
{
    // 4 5
    // 0 1 1
    // 0 2 2
    // 1 2 1
    // 1 3 5
    // 2 3 8
    // freopen("sample_input.txt", "r", stdin);
    cin >> nodeCnt >> edgeCnt;
    int from, to, cost;
    for (int i = 0; i < edgeCnt; i++)
    {
        cin >> from >> to >> cost;
        v[from].push_back({ to, cost });
        v[to].push_back({ from, cost });
    }

    int minCost = prim(0);

    cout << minCost << '\n';
}
```

차근차근 살펴보자.

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

struct Edge
{
    int to;
    int cost;
};

struct compare
{
    bool operator()(Edge a, Edge b)
    {
        return a.cost > b.cost;
    }
};

int nodeCnt, edgeCnt;
vector<Edge> v[4];
int visited[4];
```

이번엔 from 을 `Edge` 구조체에 바로 넣지 않고, DFS/BFS 할때처럼 인덱스로 받도록 하겠다.

`compare` 는 kruskal 과 동일하다.

벡터 배열은 사이즈를 잡아주고, `from` 을 인덱스로 사용한다.

`visited` 배열을 사용해 방문을 체크할 것이다.

`main()` 함수 먼저 보면,

```cpp
int main()
{
    // 4 5
    // 0 1 1
    // 0 2 2
    // 1 2 1
    // 1 3 5
    // 2 3 8
    // freopen("sample_input.txt", "r", stdin);
    cin >> nodeCnt >> edgeCnt;
    int from, to, cost;
    for (int i = 0; i < edgeCnt; i++)
    {
        cin >> from >> to >> cost;
        v[from].push_back({ to, cost });
        v[to].push_back({ from, cost });
    }

    int minCost = prim(0);

    cout << minCost << '\n';
}
```

양방향으로 연결지어서 그래프를 구성하고, `prim()` 함수에 시작 노드번호를 넣어주었을 때 `minCost` 를 구해 출력한다.

`prim()` 함수를 살펴보자.

```cpp
int prim(int st)
{
    priority_queue<Edge, vector<Edge>, compare> pq;
    int minCost = 0;
    int visitedNodeCnt = 0;
    visited[st] = 1;
    visitedNodeCnt++;
    for (int i = 0; i < v[st].size(); i++)
    {
        pq.push({ v[st][i].to, v[st][i].cost });
    }
```

`pq` 하나 선언하고, 구하고자 하는 값인 `minCost = 0` 설정한다.

`visitedNodeCnt` : 방문한 노드의 갯수를 센다. `visitedNodeCnt == nodeCnt` 일 때, `pq` 에 남은 edge 가 얼마나 있든 상관없이 루프를 종료한다.

첫 노드이기 때문에 `visited` 에 방문을 기록하고, `visitedNodeCnt` 를 올린다.

시작 노드의 edge 들을 모두 `pq` 에 담는다.

그럼 `pq.top()` 시에, "가장 cost 가 작은" edge 를 받을 수 있을 것이다.

```cpp
while (!pq.empty())
{
    Edge now = pq.top();
    pq.pop();

    if (visited[now.to])
        continue;
```

만약 가야 할 노드를 이미 방문했다면, 무시하고 다음 번 우선순위의 edge 를 받아온다.

```cpp
        visited[now.to] = 1;
        visitedNodeCnt++;
        minCost += now.cost;

        if (visitedNodeCnt == nodeCnt)
            break;

        for (int i = 0; i < v[now.to].size(); i++)
        {
            pq.push({ v[now.to][i].to, v[now.to][i].cost });
        }
    }
    return minCost;
}

```

방문했으므로, `visited` 체크하고, `visitedNodeCnt` 올리고, 거리를 합산한다.

종료조건: 방문 노드 갯수가 전체 노드 갯수에 도달했을 때

그리고 다음 노드에서의 edge 를 모두 `pq` 에 담으면 된다.

루프의 처음으로 돌아왔을 땐, 다시 전체 edge 에서 최솟값을 가진 edge 가 나오며, 해당 edge 의 목적지를 이미 방문했다면, 다음 edge 로 진행해 계속될 것이다.

마지막으로, `minCost` 를 리턴하면 된다.

`minCost` : 7

# 4. Kruskal vs Prim

edge 의 갯수가 적다 ⇒ Kruskal

edge 의 갯수가 많다 ⇒ Prim