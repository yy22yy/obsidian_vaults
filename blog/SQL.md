
# 回顾基础语法

```sql

- 筛选范围
select 属性名 from 表单名 where 属性对象 between num1 and num2

- 指定范围
SELECT name, population FROM world
  WHERE name IN ('Sweden', 'Norway','Denmark');
  where name in ('Sweden', 'Norway' and 'Denmark');
  -这里会筛选全部
  -原因：

```


```sql
WHERE name IN ('Sweden', 'Norway' and 'Denmark');
```

这个语句**语法上是合法的**，但**逻辑上是错误的**，原因如下：
在 SQL 中，`AND` 是一个**逻辑运算符**，用于连接两个布尔条件。当你写：

```sql
'Norway' and 'Denmark'
```

SQL 会将其解释为：

- `'Norway'` 是一个非空字符串 → 在布尔上下文中被视为 `TRUE`
- `'Denmark'` 也是一个非空字符串 → 也被视为 `TRUE`
- 所以 `'Norway' AND 'Denmark'` 的结果是 `TRUE`

而 `TRUE` 在大多数 SQL 数据库中会被隐式转换为 **`1`**（整数）。
```sql
WHERE name IN ('Sweden', 1);
```

也就是说，你实际上是在筛选：

- `name = 'Sweden'`，或者
- `name = 1`（即名字等于数字 1）


## 常见的包含与前后缀


- 前后缀同时实现， 单前缀或者后缀原理可以自己思考
```sql
SELECT name
FROM world
WHERE name LIKE '%United%';
```

## [[ROUND函数]]

