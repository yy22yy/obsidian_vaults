[[token]]

>**在模型内部并没有真正“分开的两段序列”，而是一个连续序列。**
   但在实现上通常会用 **角色标记（role tokens）** 来做逻辑分隔。

---

### 1 实际输入结构

以对话模型为例，真实输入通常类似：

```text
<system>  
You are a helpful assistant.  
</system>  
  
<user>  
What is AI security?  
</user>  
  
<assistant>
```

在模型看来只是 token 序列：

```text
[t1, t2, t3, ..., tn]
```

典型结构：

```cpp
system tokens  
+  
user tokens  
+  
assistant tokens (待预测)

```


---

### 2 生成过程

模型生成答案时遵循：


例如：

System: You are helpful  
User: What is AI security?  
Assistant:
	AI  
	security  
	refers  
	to  
	...

生成过程：

x1 x2 x3 ... xt → 预测 xt+1

然后继续：

x1 x2 ... xt xt+1 → 预测 xt+2

直到结束 token。

因此：

**回答 token 其实也是在同一个序列上继续生成。**

---

### 3 为什么看起来像“分开”

工程系统会加入 **特殊标记**：

例如：
```text
<system>  
<user>  
<assistant>
```

这些 token 的作用是告诉模型：

```
当前是谁在说话
```

但本质仍然是：
```
单一 token 序列
```

而不是两个独立输入。

---

### 4 一个直观理解

可以把 LLM 看成：

**文本续写机器**

例如输入：
```
User: What is AI?  
Assistant:
```

模型的任务只是：

```
继续写出合理文本
```


