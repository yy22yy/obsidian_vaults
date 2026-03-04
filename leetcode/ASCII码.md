# 常见应用

## 1.大小写转换

```cpp
#include <iostream>
using namespace std;

int main() {
    char ch;
    cin >> ch;
    
    if (ch >= 'A' && ch <= 'Z') {
        ch = ch + 32;  // 大写转小写
    } else if (ch >= 'a' && ch <= 'z') {
        ch = ch - 32;  // 小写转大写
    }
    
    cout << ch << endl;
    return 0;
}
```


