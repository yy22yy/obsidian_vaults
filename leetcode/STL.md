## 一、不用手写循环的遍历操作

```cpp
vector<int> arr = {3,1,4,1,5,9,2,6};

// 求最大最小值
int mx = *max_element(arr.begin(), arr.end());
int mn = *min_element(arr.begin(), arr.end());

// 求和
int sum = accumulate(arr.begin(), arr.end(), 0);  // 第三个参数是初始值

// 统计某个值出现次数
int cnt = count(arr.begin(), arr.end(), 1);       // 统计1出现几次
```

## 二、查找


```cpp
// 线性查找，返回迭代器
auto it = find(arr.begin(), arr.end(), 5);
if (it != arr.end()) cout << "找到了，下标=" << it - arr.begin();

// 条件查找
auto it = find_if(arr.begin(), arr.end(), [](int x){ return x > 8; });

// 有序数组二分查找
bool found = binary_search(arr.begin(), arr.end(), 5);
auto it    = lower_bound(arr.begin(), arr.end(), 5); // 第一个>=5
auto it    = upper_bound(arr.begin(), arr.end(), 5); // 第一个>5
```

## 四、排列与重排


```cpp
// 翻转
reverse(arr.begin(), arr.end());

// 旋转（把下标2的元素转到开头）
rotate(arr.begin(), arr.begin() + 2, arr.end());
// {3,1,4,1,5} → {4,1,5,3,1}
```

## 七、堆操作

cpp

```cpp
vector<int> arr = {3,1,4,1,5};

make_heap(arr.begin(), arr.end());   // 建大根堆，O(n)
push_heap(arr.begin(), arr.end());   // 新元素加入堆（先push_back再调用）
pop_heap(arr.begin(), arr.end());    // 堆顶移到末尾（再pop_back删除）
sort_heap(arr.begin(), arr.end());   // 堆排序

// 实际机试更常用 priority_queue，不直接操作heap
priority_queue<int> maxHeap;                            // 大根堆
priority_queue<int, vector<int>, greater<int>> minHeap; // 小根堆
```