[根目录](../../CLAUDE.md) > [src](../) > **pages**

# Pages 模块文档

## 变更记录 (Changelog)

### 2025-11-26
- 初始化页面模块文档
- 分析路由结构和页面功能

## 模块职责

`src/pages` 目录包含项目的所有页面路由定义。基于Astro的文件系统路由，每个`.astro`文件对应一个URL路径。这些页面负责处理用户请求、获取数据、渲染内容，是博客网站的主要入口点。

## 入口与启动

### 路由映射
```
src/pages/
├── [...page].astro    → /posts/[page]  (文章分页)
├── about.astro        → /about         (关于页面)
├── archive.astro      → /archive       (归档页面)
├── index.astro        → /              (首页)
├── robots.txt.ts      → /robots.txt    (爬虫配置)
└── rss.xml.ts         → /rss.xml       (RSS订阅)
```

### 默认首页
- `index.astro` 是网站的默认首页
- 目前使用[...page].astro作为首页，显示最新文章列表

## 页面功能详情

### [...page].astro - 文章分页页面
- **路径**: `/posts/[page]` 或 `/` (首页)
- **功能**: 显示博客文章列表，支持分页
- **数据源**: 从内容集合获取文章数据
- **组件**: 使用 `PostPage` 和 `Pagination` 组件

### about.astro - 关于页面
- **路径**: `/about`
- **功能**: 显示博客作者信息和介绍
- **内容**: 使用内容集合中的about.md文件

### archive.astro - 归档页面
- **路径**: `/archive`
- **功能**: 按时间或分类显示文章归档
- **功能**: 文章检索和历史浏览

### robots.txt.ts - SEO配置
- **路径**: `/robots.txt`
- **功能**: 搜索引擎爬虫指令
- **格式**: 静态文本文件

### rss.xml.ts - RSS订阅
- **路径**: `/rss.xml`
- **功能**: 提供RSS/Atom订阅源
- **内容**: 最新文章的XML格式摘要

## 对外接口

### 页面数据获取
所有页面都通过Astro的`getStaticPaths`或`getStaticProps`获取数据：
- **内容集合**: 使用 `getCollection("posts")` 获取文章数据
- **工具函数**: 使用 `getSortedPosts()` 等工具函数处理数据
- **分页**: 使用Astro内置的 `paginate` 函数

### 页面参数
- **分页参数**: `{ page }` 包含当前页数据和分页信息
- **文章数据**: 包含标题、内容、标签、分类等元数据

## 关键依赖与配置

### 内容依赖
- `@/content/config.ts` - 内容集合配置
- `@/utils/content-utils.ts` - 内容处理工具
- `@/layouts/*` - 页面布局模板

### 功能组件
- `@/components/PostPage.astro` - 文章列表组件
- `@/components/control/Pagination.astro` - 分页组件
- `@/layouts/MainGridLayout.astro` - 主网格布局

### 数据源
- `src/content/posts/` - 博客文章Markdown文件
- `src/content/spec/` - 特殊页面内容

## 路由规则

### Astro文件系统路由
- 静态路由: `about.astro` → `/about`
- 动态路由: `[slug].astro` → `/[slug]`
- 捕获路由: `[...page].astro` → `/posts/[...page]`

### 路由优先级
1. 静态路由优先级最高
2. 动态路由次之
3. 捕获路由优先级最低

## 测试与质量

### 建议的测试策略
- **路由测试**: 验证所有页面能正确渲染
- **数据获取测试**: 确保页面数据正确加载
- **分页测试**: 验证分页功能正常工作
- **SEO测试**: 检查meta标签和结构化数据

### 性能优化
- 使用Astro的静态生成功能
- 启用图片优化和懒加载
- 配置适当的缓存策略

## 常见问题 (FAQ)

### Q: 如何添加新页面？
A: 在 `src/pages` 目录创建新的 `.astro` 文件，文件名对应URL路径。

### Q: 如何创建动态路由？
A: 使用方括号 `[param].astro` 创建动态路由，使用 `getStaticPaths` 提供参数。

### Q: 如何处理404页面？
A: 创建 `src/pages/404.astro` 文件作为自定义404页面。

### Q: 如何修改默认首页？
A: 修改或替换 `src/pages/index.astro` 文件。

## 扩展指南

### 添加博客功能
1. **标签页面**: 创建 `src/pages/tags/[tag].astro`
2. **分类页面**: 创建 `src/pages/categories/[category].astro`
3. **搜索页面**: 创建 `src/pages/search.astro`

### 添加静态页面
1. **联系页面**: 创建 `src/pages/contact.astro`
2. **项目页面**: 创建 `src/pages/projects.astro`
3. **友情链接**: 创建 `src/pages/links.astro`

## 相关文件清单

### 页面文件
- `[...page].astro` - 文章分页页面
- `about.astro` - 关于页面
- `archive.astro` - 归档页面
- `index.astro` - 首页（如果存在）

### 功能文件
- `robots.txt.ts` - 爬虫配置
- `rss.xml.ts` - RSS订阅源

### 相关布局
- `../layouts/Layout.astro` - 基础布局
- `../layouts/MainGridLayout.astro` - 网格布局

### 依赖工具
- `../utils/content-utils.ts` - 内容处理工具
- `../components/PostPage.astro` - 文章列表组件
- `../constants/constants.ts` - 常量定义