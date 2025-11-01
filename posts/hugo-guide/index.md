# Hugo 使用指南


## Hugo 简介

Hugo 是目前最流行的静态网站生成器之一，它具有以下特点：

- **极快的构建速度**: 每页构建时间少于 1ms
- **跨平台**: 支持 Windows、Linux、macOS
- **灵活的配置**: 通过 TOML、YAML 或 JSON 配置
- **强大的模板**: 基于 Go 模板引擎

## 基本概念

### 1. 内容组织

Hugo 使用 `content/` 目录组织内容：

```
content/
├── posts/        # 博客文章
├── about/        # 关于页面
└── projects/     # 项目展示
```

### 2. Front Matter

每篇文章开头的元数据部分：

```yaml
---
title: "文章标题"
date: 2025-11-01
tags: ["tag1", "tag2"]
draft: false
---
```

### 3. 短代码

Hugo 提供了强大的短代码功能：

{{</* highlight go */>}}
func main() {
    fmt.Println("Hello, Hugo!")
}
{{</* /highlight */>}}

## 数学公式支持

Hugo 可以通过配置支持 $\LaTeX$ 数学公式：

$$
f(x) = \int_{-\infty}^\infty \hat{f}(\xi)\,e^{2 \pi i \xi x} \,d\xi
$$

## 常用命令

1. 创建新站点：
   ```bash
   hugo new site myblog
   ```

2. 创建新文章：
   ```bash
   hugo new posts/my-first-post.md
   ```

3. 本地预览：
   ```bash
   hugo server -D
   ```

## 部署流程

1. 生成静态文件：
   ```bash
   hugo
   ```

2. 部署到服务器：
   - GitHub Pages
   - Netlify
   - Vercel
   - 自托管服务器

## 性能优化

Hugo 提供了多种优化选项：

1. 图片处理
2. 资源压缩
3. CDN 配置
4. 延迟加载

## 下一步学习

1. 学习 Go 模板语法
2. 自定义主题开发
3. 短代码创建
4. CI/CD 集成

## 参考资源

- [Hugo 官方文档](https://gohugo.io/documentation/)
- [Hugo 主题](https://themes.gohugo.io/)
- [Hugo 论坛](https://discourse.gohugo.io/)
