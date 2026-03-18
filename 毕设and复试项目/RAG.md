[[RAG中的文档]]

# 什么是 RAG 系统

RAG 是当前大模型系统非常核心的架构。

实体：Retrieval-Augmented Generation

中文通常叫：**检索增强生成**。

---

## 1 为什么需要 RAG

LLM 存在两个问题：

### 1 知识过期

模型训练数据有时间限制,之后的新信息模型不知道。

---

### 2 容易幻觉

模型可能：编造事实,因为它只是概率生成。

---

RAG 的思想：
先查资料 再生成答案
类似：开卷考试

---

# 三、RAG 的基本流程

```
典型流程：

用户问题  
   ↓  
向量检索  
   ↓  
找到相关文档  
   ↓  
文档拼接到 prompt  
   ↓  
LLM 生成答案

结构图：

User Query  
    ↓  
Embedding  
    ↓  
Vector Database  
    ↓  
Top-K Documents  
    ↓  
Prompt + Documents  
    ↓  
LLM  
    ↓  
Answer
```

---

## 1 文档向量化

首先把知识库文本转成向量。

使用：

Embedding
```
"AI security studies attacks on ML models"
```
→
```
[0.23, -0.18, 0.91, ...]
```

---

## 2 向量检索

把用户问题也转成向量：

"What is AI security?"

cosine similarity

找到最相似的文档。

---

## 3 拼接 Prompt

```
System: You are an expert.  
  
Document:  
AI security studies attacks on machine learning models.  
  
User:  
What is AI security?
```

然后输入给 LLM。

---

## 4 生成答案

模型基于：

```
问题 + 文档
```

生成回答。

---

# 四、RAG 的本质

RAG 本质是：
```
LLM + 外部知识库  参数知识  参数知识 + 检索知识
```

---

# 五、RAG 与 Prompt Injection 的关系

RAG 也是 **Prompt Injection 的高风险点**。

原因：检索到的文档会直接加入 prompt：

```
prompt = system + user + retrieved_docs
```

如果文档中包含：模型可能被误导。
```
Ignore previous instructions
```


---

# 六、面试简洁回答版本

如果老师问：

**什么是 RAG 系统？**

推荐回答：

> RAG（Retrieval-Augmented Generation）是一种将信息检索与大语言模型结合的架构。系统首先通过向量检索从知识库中找到与用户问题相关的文档，然后将这些文档与用户问题一起作为 prompt 输入到大语言模型中，从而生成更加准确和可控的答案。这种方法可以解决大模型知识过期和幻觉问题，并广泛用于企业知识问答系统。