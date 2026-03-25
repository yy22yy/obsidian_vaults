用于按行输入，而且不知道个数

## C++ `getline` 使用方法

### 基本语法

```cpp
getline(输入流, 字符串变量);
getline(输入流, 字符串变量, 分隔符);
```

---

### 1. 从标准输入读取一行

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string line;
    cout << "请输入一行文字: ";
    getline(cin, line);
    cout << "你输入了: " << line << endl;
    return 0;
}
```

> 与 `cin >>` 不同，`getline` 会读取**整行包括空格**，直到遇到换行符 `\n`。

---

### 2. 自定义分隔符

```cpp
string data;
getline(cin, data, ',');  // 遇到逗号停止读取
```

---

### 3. 与 `cin >>` 混用时的陷阱

```cpp
int age;
string name;

cin >> age;         // 读取数字后，缓冲区残留 '\n'
cin.ignore();       // ⚠️ 必须清除残留的换行符
getline(cin, name); // 否则会直接读到空字符串
```

---

### 4. 从文件读取

```cpp
#include <fstream>
#include <string>
using namespace std;

int main() {
    ifstream file("data.txt");
    string line;
    while (getline(file, line)) {  // 逐行读取直到文件末尾
        cout << line << endl;
    }
    return 0;
}
```

---

### 5. 从字符串流读取（`istringstream`）

```cpp
#include <sstream>
#include <string>
using namespace std;

int main() {
    string csv = "apple,banana,cherry";
    istringstream ss(csv);
    string token;
    
    while (getline(ss, token, ',')) {  // 按逗号分割
        cout << token << endl;
    }
    // 输出: apple / banana / cherry
}
```

---

### 关键点对比

|特性|`cin >>`|`getline`|
|---|---|---|
|读取空格|❌ 遇空格停止|✅ 包含空格|
|读取换行|❌ 遇换行停止|✅ 读到换行为止（换行符被丢弃）|
|自定义分隔符|❌|✅|
|返回值|流引用|流引用（可用于循环判断）|

---

### 常见错误速查

```cpp
// ❌ 错误：cin >> 后直接用 getline，会读到空行
cin >> n;
getline(cin, s); // s 为空！

// ✅ 正确：加 ignore 清除缓冲
cin >> n;
cin.ignore(numeric_limits<streamsize>::max(), '\n');
getline(cin, s);
```