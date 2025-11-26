[根目录](../../CLAUDE.md) > [src](../) > **content**

# Content 模块文档

## 变更记录 (Changelog)

### 2025-11-26
- 初始化内容模块文档
- 分析内容结构和配置

## 模块职责

`src/content` 目录使用 Astro Content Collections 管理所有的 Markdown 内容和静态资源。该模块负责博客文章、页面内容的存储、组织、验证和处理，为整个网站提供结构化的内容数据。

## 入口与启动

### 内容目录结构
```
src/content/
├── config.ts           # 内容集合配置
├── posts/             # 博客文章
│   ├── guide/         # 指南类文章
│   ├── emby/          # Emby相关文章
│   ├── changedetection/
│   ├── crontab-op-mac-app/
│   ├── crontab-tz/
│   ├── collect/       # 收藏类文章
│   ├── docker-issue/
│   ├── vaultwarden/
│   ├── minio/
│   ├── nginx-resolver/
│   ├── acme/
│   ├── windows/
│   ├── headscale/
│   ├── mac-vpn-conflict/
│   ├── prometheus-grafana/
│   ├── rclone/
│   ├── log-manage/
│   └── ...            # 其他文章目录
└── spec/              # 特殊页面
    └── about.md       # 关于页面
```

### 配置入口
- `config.ts` - 内容集合Schema定义
- 定义文章数据结构和验证规则

## 内容集合配置

### 文章集合 (posts)
```typescript
const postsCollection = defineCollection({
  schema: z.object({
    title: z.string(),           // 文章标题
    published: z.date(),         // 发布日期
    updated: z.date().optional(), // 更新日期
    draft: z.boolean().optional().default(false), // 草稿状态
    description: z.string().optional().default(""), // 文章描述
    image: z.string().optional().default(""), // 封面图片
    tags: z.array(z.string()).optional().default([]), // 标签
    category: z.string().optional().nullable().default(""), // 分类
    lang: z.string().optional().default(""), // 语言

    // 内部使用字段
    prevTitle: z.string().default(""),
    prevSlug: z.string().default(""),
    nextTitle: z.string().default(""),
    nextSlug: z.string().default(""),
  }),
});
```

## 内容类型

### 博客文章 (posts)
每个文章目录包含：
- `index.md` - 文章正文和FrontMatter
- `cover.jpg/jpeg/png` - 文章封面图片
- `images/` - 文章内图片资源
- 其他媒体文件

### FrontMatter 示例
```yaml
---
title: 文章标题
published: 2024-01-01
updated: 2024-01-02  # 可选
description: 文章描述
image: cover.jpg      # 可选
tags: ["tag1", "tag2"]
category: 技术文章     # 可选
draft: false         # 可选，默认false
lang: zh_CN          # 可选
---
```

### 特殊页面 (spec)
用于存储静态页面内容，如关于页面：
- `about.md` - 关于页面内容

## 对外接口

### 内容获取API
- `getCollection("posts")` - 获取所有文章
- `getCollection("posts", filter)` - 根据条件筛选文章
- `getEntry("posts", slug)` - 获取单篇文章

### 工具函数
```typescript
// 获取排序后的文章列表
getSortedPosts()

// 获取标签列表
getTagList()

// 获取分类列表
getCategoryList()
```

## 内容处理流程

### 数据获取
1. 从Markdown文件读取内容
2. 验证FrontMatter是否符合Schema
3. 解析Markdown内容为HTML
4. 处理文章间的前后关系
5. 生成统计信息（标签、分类计数）

### 内容转换
- Markdown → HTML
- 图片路径处理
- 链接转换
- 特殊语法扩展

## 关键依赖与配置

### Markdown处理
- `@astrojs/markdown-remark` - Markdown处理
- `remark-math` - 数学公式支持
- `rehype-katex` - KaTeX渲染
- `rehype-slug` - 标题锚点
- `rehype-autolink-headings` - 自动链接

### 自定义插件
- `remark-excerpt` - 摘要提取
- `remark-reading-time` - 阅读时间计算
- `rehype-component-*` - 自定义组件
- `expressive-code` - 代码高亮

### 工具函数
- `../utils/content-utils.ts` - 内容处理工具
- `../utils/date-utils.ts` - 日期处理
- `../utils/url-utils.ts` - URL处理

## 内容管理

### 创建新文章
使用脚本命令创建新文章：
```bash
pnpm new-post -- 文章标题
```
会自动生成符合模板的Markdown文件。

### 文章组织
- 按主题分类创建子目录
- 每个目录包含相关图片资源
- 支持多级目录结构

### 内容状态
- `draft: true` - 草稿，生产环境不显示
- `published` - 发布日期，用于排序
- `updated` - 更新日期，用于显示更新时间

## 内容特性

### 多语言支持
- `lang` 字段指定文章语言
- 支持中文、英文、日文等多种语言
- 可根据语言筛选内容

### 分类和标签
- `category` - 文章分类（单选）
- `tags` - 文章标签（多选）
- 自动生成分类和标签页面

### SEO优化
- 自动生成文章摘要
- 支持自定义描述
- 图片alt标签优化

## 测试与质量

### 内容验证
- FrontMatter Schema验证
- 必填字段检查
- 日期格式验证

### 建议的测试策略
- **内容加载测试**: 确保所有文章正确加载
- **Schema验证测试**: 验证FrontMatter格式
- **链接检查测试**: 检查内部链接有效性
- **图片优化测试**: 确保图片正确显示

## 常见问题 (FAQ)

### Q: 如何创建多级目录的文章？
A: 使用 `pnpm new-post -- category/subcategory/article-name` 命令。

### Q: 如何修改文章URL？
A: 文章URL基于文件路径，重命名文件或目录即可改变URL。

### Q: 如何设置文章发布时间？
A: 在FrontMatter中设置 `published` 字段。

### Q: 如何添加新的FrontMatter字段？
A: 修改 `config.ts` 中的Schema定义。

## 扩展指南

### 添加新的内容类型
1. 在 `config.ts` 中定义新的集合
2. 创建对应的Schema
3. 添加内容文件到相应目录

### 自定义Markdown处理
1. 在 `astro.config.mjs` 中添加新的remark/rehype插件
2. 创建自定义插件文件

### 内容模板定制
1. 修改 `scripts/new-post.js` 中的模板
2. 添加自定义的FrontMatter字段

## 相关文件清单

### 配置文件
- `config.ts` - 内容集合配置

### 内容目录
- `posts/` - 博客文章
- `spec/` - 特殊页面

### 处理工具
- `../utils/content-utils.ts` - 内容处理工具
- `../scripts/new-post.js` - 新文章创建脚本

### 插件配置
- `../../astro.config.mjs` - Markdown处理配置
- `../plugins/` - 自定义插件目录

### 相关组件
- `../components/PostCard.astro` - 文章卡片
- `../components/PostPage.astro` - 文章页面
- `../pages/[...page].astro` - 文章列表页面