**[[NexT]]** 是 Hexo 最流行的主题之一，特点是极简、Typography 优先、支持大量配置项。它本身就是「极简无装饰风格」的代表主题。


# 
# Hexo的主要作用

主要就是讲markdown文档编译成前端html界面供别人阅读（ps：自己也可以阅读，用网站就不用费劲巴拉地下一个obsidian了，不过可能后续解决更多关于obsidian地可视化问题；但是我相信这一切做成了都是有价值地

不久的将来，ai肯定都可以随意读取你的思想、你的方法、你的一切；但是前提你要将他数据化；
未来一定是一个信息和数据驱动的世界，将关于你自己的一切数据化吧！

---
## 为什么要编译成 HTML

浏览器是一个**只懂 HTML/CSS/JS 的渲染引擎**。它不认识 Markdown，不认识 `.vue`，不认识 `.jsx`。

你在地址栏输入一个 URL，本质上是向服务器发出请求，服务器返回一段文本，浏览器解析这段文本并渲染。浏览器唯一能解析的文本格式就是 HTML。

```
你写的 Markdown（给人读的，方便写）
         ↓ 编译
      HTML（给浏览器读的）
```

Markdown 是为了让**人类写起来舒服**而发明的语法糖。`# 标题` 比 `<h1>标题</h1>` 简洁得多。但最终必须转成 HTML 才能在浏览器里显示。Hexo/Hugo 做的核心事情就是这个转换。

---


# Hexo的安装过程
### 第一步：安装 Hexo

bash

```bash
npm install -g hexo-cli
```
有关[[npm]]的相关知识
### 第二步：初始化博客



```bash
hexo init my-blog
cd my-blog
npm install
```

目录结构长这样：
```
my-blog/
├── source/
│   └── _posts/        ← 你的文章放这里（.md 文件）
├── themes/            ← 主题放这里
├── _config.yml        ← 全局配置文件
└── package.json
```

### 第三步：本地预览

bash

```bash
hexo server
```

### 第四步：安装 [[NexT ]]主题

bash

```bash
npm install hexo-theme-next
```

然后在 `_config.yml` 里改一行：

yaml

```yaml
theme: next
```

# 编译完之后交给[[GitHub Pages]]管理html
