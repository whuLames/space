---
title: "Hello Hugo"
date: 2026-07-07
draft: false
tags: ["hugo", "papermod", "博客"]
categories: ["折腾记录"]
summary: "从 VuePress 迁移到 Hugo + PaperMod，博客重新开张。"
---

## 为什么换到 Hugo

之前用 VuePress 搭的博客（`whulames.github.io`）资源丢失了，索性趁这次重开，换到 **Hugo + PaperMod**。

主要考虑：

- **构建快**：Hugo 是单二进制，启动和构建都是毫秒级。
- **PaperMod 极简好看**：开箱即用，自带搜索、暗色模式、目录、代码复制按钮。
- **Project Page 模式**：刻意把博客放在 `/space/` 子路径，把根域名 `whulames.github.io` 留给将来的学术主页。

## 一段代码示例

验证一下代码高亮（monokai 风格）和复制按钮：

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Hugo!")
}
```

## 接下来

- 把基础页面跑起来
- 慢慢把以前的笔记搬回来
- 学术主页单独再起一个项目

欢迎来到我的新博客。
