
![[Pasted image 20260327215901.png]]
## 各配置块的作用和你应该改什么

### Site — 第一个要改的块

```yaml
title: 呆呆的喷喷菇初始的blog搭建   # 改成你想要的站点名，显示在浏览器标签和页面顶部
subtitle: 'blogs'                    # 副标题，NexT 主题会显示在标题下方
description: '暂时不做任何的描述'    # 给搜索引擎看的，对 SEO 有影响
author: acr0ss                       # 你的名字，文章底部的作者署名
language: en                         # 改成 zh-CN，界面文字变中文（分页、归档等）
timezone: ''                         # 改成 Asia/Shanghai，文章时间显示正确
```

`language: zh-CN` 这一项很多人忘改，结果 NexT 主题的"归档"显示成 Archives，"标签"显示成 Tags。

---

### URL — 决定你的文章地址长什么样

```yaml
url: http://123.com          # 部署到 GitHub Pages 后改成你的真实域名
permalink: :year/:month/:day/:title/
```

`permalink` 控制每篇文章的 URL 格式。现在的配置会生成：

```
yourdomain.com/2026/03/27/关于我/
```

对于个人博客，更简洁的格式是：

```yaml
permalink: :title/           # 直接用文章标题，最短
# 或者
permalink: posts/:title/     # 带一个 posts 前缀，更清晰
```

---

### Writing — 控制写作体验

```yaml
new_post_name: :title.md     # hexo new post "xxx" 生成的文件名格式，不用改
default_layout: post         # 新文章默认用 post 布局，不用改
syntax_highlighter: highlight.js   # 代码块高亮方案
highlight:
  line_number: true          # 显示代码行号，个人偏好，可改 false
```

这块基本不用动，除非你不想要代码行号。

---

### Extensions — 最重要的一行

```yaml
theme: landscape             # 把这行改成 next，整个网站视觉立刻变成极简风格
```

这一行就是主题切换的开关。`landscape` 是 Hexo 默认主题，改成 `next` 之后 Hexo 会去 `node_modules/hexo-theme-next/` 里找模板。

---

### ==Deployment — 部署配置，现在先不急===

```yaml
deploy:
  type: ''    # 等你准备推到 GitHub Pages 时再填，现在留空
```

Github的仓库配置

```yaml
deploy:
  type: git
  repo: https://github.com/你的用户名/你的用户名.github.io.git
  branch: main
```

---

## 现在立刻做的三件事

**第一件：改语言和主题**，这两行改完效果最明显：

```yaml
language: zh-CN
theme: next
```

**第二件：确认 NexT 已安装**

```bash
npm install hexo-theme-next
```

**第三件：重启预览看效果**

```bash
hexo clean && hexo server
```

`hexo clean` 是清除上次生成的缓存，改了配置之后养成先 clean 再 server 的习惯，避免看到旧版本。

---

改完之后把浏览器截图发过来，我帮你看效果对不对。