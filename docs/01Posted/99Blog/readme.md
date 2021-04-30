# 给我，也来一份

这里简单说下怎么复制一份这样的博客。

## 创建GithubPages

```note
这个你应该会的ovo，唯一的问题是如何选择分支和目录，这个，随缘吧。
```

## 配置主题

这里，我的主题是这样配置的。文件是`_config.yml`

```yml
remote_theme: rundocs/jekyll-rtd-theme

title: Awesome Yoko
lang: en
description: Write an awesome description for your new site here
```

## 使用

基本遵循常见的markdown语法,但从组织形式上来说类似下面这样

```plaintext
-- <blog_root_dir>
   |-- <A目录>
      |-- readme.md 必须有这个文件这个目录才会显示在侧边栏，标题同侧边栏
      |-- post.md 文章
```

### 卡片

效果如下，代码看仓库就好啦

note:

```plaintext
    ```note
    ### This is a note

    Markdown is supported, Text can be **bold**, _italic_, or ~~strikethrough~~. [Links](https://github.com) should be blue with no underlines

    `inline code`

    [`inline code inside link`](#)
    ```
```

```note
### This is a note

Markdown is supported, Text can be **bold**, _italic_, or ~~strikethrough~~. [Links](https://github.com) should be blue with no underlines

`inline code`

[`inline code inside link`](#)
```

tip:

```tip
It's bigger than a bread box.
```

warning:

```warning
Strong prose may provoke extreme mental exertion. Reader discretion is strongly advised.
```

danger:

```danger
Mad scientist at work!
```
