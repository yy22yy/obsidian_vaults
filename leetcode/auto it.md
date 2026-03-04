# 一、`auto it` 的用法与本质

## 1. `auto` 是什么？

`auto` = **编译期类型推导**

```cpp
auto x = 10;     // x 自动推导为 int  
auto y = 3.14;   // y 自动推导为 double
```

	本质：

> 让 **编译器替你写类型**

![[Pasted image 20260301112832.png]]
![[Pasted image 20260301112912.png]]
- 当it遍历的对象是结构体
![[Pasted image 20260301112955.png]]

## 使用auto it写循环的经典方法

```cpp
for(auto it = v.begin(); it != v.end();it++){
	//遍历对象的使用
	*it 
}
```