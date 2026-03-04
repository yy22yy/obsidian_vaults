## 暴力解法

```cpp
#include<iostream>
#include<string>
#include <algorithm>
#include <iomanip> //保留小数点位数
#include <cmath>  //开平方
#include <vector>
#include <numeric> //累加函数aacumulate()

using namespace std;
int main(){
    int n,M;
    cin >> n >> M;

    //开辟n个空间
    vector<int> a(n);
    //rean n nums
    for(int i = 0; i<n;i++){
        cin >> a[i];
    }
    int count = 0;
        //尝试暴力解
    for(int i = 0; i<n;i++){
        for(int j =i;j<n;j++){
         int sum = accumulate(a.begin()+i,a.begin()+j+1,0);
         if(sum >M*(j-i+1)){
            count++;
         }
        }
    }    
    cout << count % 92084931;
}

```

## 前缀和解法--第一版基础

```cpp
#include<iostream>
#include<string>
#include <algorithm>
#include <iomanip> //保留小数点位数
#include <cmath>  //开平方
#include <vector>
#include <numeric> //累加函数aacumulate()

using namespace std;
int main(){
    //处理输入
    int n,M;
    cin >> n>> M;
    vector<int> a(n);
        for(int i = 0;i<n;i++){
        cin>> a[i];
        }
    
    //前缀和解法
    long long count = 0;//数据范围要使用longlong
    ///初始化前缀和数组
    vector<long long> pre(n + 1, 0);
    for(int i = 1; i <= n; ++i) {
        pre[i] = pre[i - 1] + a[i - 1];
    }
    
    for(int i = 0;i<n;i++)
    {
        for(int j = i;j<n;j++){
                if(pre[j+ 1] - pre[i] > M*(j-i+1) )
                   {
                       count++;
                   }
                                }
    }
    
    cout<<count%92084931;
    
}
```

# 一、你当前算法的瓶颈

你现在做的是：

```cpp
for(i)  
  for(j)  
    判断区间是否满足条件
```

复杂度：

O(n^2)

如果 n = 10⁵：

必然超时:[[运行时间估计]]

所以必须**彻底改变算法结构**，而不是小修小补。

---

# 二、问题数学等价转化（关键一步）

原条件：
```cpp
pre[j+1] - pre[i] > M * (j - i + 1)
```

移项：

```cpp
(pre[j+1]−M(j+1))>(pre[i]−Mi)(pre[j+1] - M(j+1)) > (pre[i] - Mi)

(pre[j+1]−M(j+1))>(pre[i]−Mi)

定义新前缀数组：
b[k] = pre[k] - M * k

则：

b[j+1]>b[i]b[j+1] > b[i]b[j+1]>b[i]
```





所以问题变成：

> **统计满足 i < j+1 且 b[j+1] > b[i] 的对数**

换句话说：

> **统计数组 b 中，前面的数比后面的数小的对数**

这是一个**经典的：逆序对 / 顺序对 计数问题**。

---

# 三、问题本质

你要统计：

```cpp
i<j 且 b[i]>b[j]
```

**在数组 b 中，统计有多少个升序对**

这个问题有标准解法：

- 归并排序
- 树状数组（Fenwick Tree）
- 平衡二叉树

最经典：**归并排序计数**

复杂度：



---

# 四、O(n log n) 归并排序解法

核心思想：

在**归并排序合并阶段**统计顺序对：

当：

left[i] < right[j]

说明：

left[i] < right[j...end]

一次性贡献多个答案。

---

# 五、完整优化代码（归并排序计数）

#include <bits/stdc++.h>
  
using namespace std;
  
using ll = long long;
  
  
const ll MOD = 92084931;
  
  
ll merge_count(vector<ll>& a, int l, int r) {
  
    if (l >= r) return 0;
  
    int mid = (l + r) / 2;
  
    ll res = 0;
  
    res += merge_count(a, l, mid);
  
    res += merge_count(a, mid + 1, r);
  
  
    vector<ll> tmp;
  
    int i = l, j = mid + 1;
  
  
    while (i <= mid && j <= r) {
  
        if (a[i] < a[j]) {
  
            res += (r - j + 1);   // 关键：一次性统计多个
  
            tmp.push_back(a[i++]);
  
        } else {
  
            tmp.push_back(a[j++]);
  
        }
  
    }
  
    while (i <= mid) tmp.push_back(a[i++]);
  
    while (j <= r) tmp.push_back(a[j++]);
  
  
    for (int k = l; k <= r; k++)
  
        a[k] = tmp[k - l];
  
  
    return res % MOD;
  
}
  
  
int main() {
  
    ios::sync_with_stdio(false);
  
    cin.tie(nullptr);
  
  
    int n;
  
    ll M;
  
    cin >> n >> M;
  
  
    vector<ll> a(n);
  
    for (int i = 0; i < n; i++) cin >> a[i];
  
  
    vector<ll> pre(n + 1, 0), b(n + 1);
  
    for (int i = 1; i <= n; i++)
  
        pre[i] = pre[i - 1] + a[i - 1];
  
  
    for (int i = 0; i <= n; i++)
  
        b[i] = pre[i] - M * i;
  
  
    ll ans = merge_count(b, 0, n);
  
  
    cout << ans % MOD;
  
}

---

# 六、复杂度对比

|方法|时间复杂度|n=10⁵|
|---|---|---|
|你原来双循环|O(n²)|❌ 直接 TLE|
|归并排序计数|O(n log n)|✅ 轻松通过|
|树状数组|O(n log n)|✅|

---

# 七、算法思想总结

### 你原方法的结构：

> 枚举所有区间 → 暴力统计

### 正确的算法结构：

> 数学变形 → 转化为经典计数模型 → 用高效算法

这是**算法竞赛最核心的能力**：

> **把区间问题 → 转化为前缀 + 排序计数问题**
