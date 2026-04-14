[[Hexo]]


# 基本的安装步骤


安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

```bash
$ hexo init <folder>  
$ cd <folder>
$ npm install
```
初始化后，您的项目文件夹将如下所示：

![[Pasted image 20260327194825.png]]


## 文件作用解释


### Hexo 网站的配置文件--config.yml
**_config.yml** **Hexo 网站的配置文件**，你可以在这个文件里设置网站的大部分参数（比如网站标题、作者、语言、部署方式等）。

具体的配置方法： [[config.yml]]


### package.json 应用程序配置文件
**package.json** 是 **应用程序信息文件**，记录了项目依赖的 Node.js 包信息。其中 EJS（模板引擎）、Stylus（CSS 预处理器）和 Markdown（文章渲染引擎）已经默认安装，不需要可以手动移除。

### scaffolds -- 模板文件夹

==模板文件的作用==  --  存放模板
当您新建文章时，Hexo 会根据 scaffold 来创建文件。

### source --- ==源markdown文件的存放地==

资源文件夹。 是存放用户资源的地方。
除 `_posts` 文件夹之外，开头命名为 `_` (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。 
> 这里可能某种程度上实现了 .gitignore

Markdown 和 HTML 文件会被解析并放到 `public` 文件夹，而其他文件会被拷贝过去。

### themes -- ==配置主题==

文件夹。 Hexo 会根据主题来生成静态页面。