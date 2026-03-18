
# 基本操作

 ```cpp
priority_queue<int> pq;

pq.push(50);    // 插入，O(log n)
pq.push(100);
pq.push(30);

pq.top();       // 查看堆顶，O(1)，不删除 → 返回 100
pq.pop();       // 删除堆顶，O(log n)
pq.size();      // 元素个数
pq.empty();     // 是否为空

// "弹出"堆顶（取值 + 删除）
int val = pq.top();
pq.pop();
 ```

## vector初始化堆

```cpp
priority_queue<int> pq(v.begin(),v.end());


完整写法：
vector<long long> arr(n);
for(auto& x : arr) cin >> x;
priority_queue<long long, vector<long long>, greater<long long>> pq(arr.begin(), arr.end()); // O(n) 建堆

```


## 输入时进行插入的方法
```cpp
	int n; cin >> n;
    // 小根堆：直接用 vector 初始化，O(n) 建堆
    priority_queue<long long, vector<long long>, greater<long long>> pq;
    for(int i = 0; i < n; i++){
        int x; cin >> x;
        pq.push(x);
    }

```


![[Pasted image 20260315133756.png]]

