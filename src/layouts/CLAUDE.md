[根目录](../../CLAUDE.md) > [src](../) > **layouts**

# Layouts 模块文档

## 变更记录 (Changelog)

### 2025-11-26
- 初始化布局模块文档
- 分析布局结构和模板系统

## 模块职责

`src/layouts` 目录包含页面的布局模板，定义了网站的整体结构和视觉风格。布局模板负责HTML结构、CSS样式、JavaScript功能的组织，为页面提供统一的外观和交互体验。

## 入口与启动

### 布局文件
```
src/layouts/
├── Layout.astro         # 基础布局模板
└── MainGridLayout.astro # 主网格布局
```

### 使用方式
页面通过在顶部导入布局组件并包装内容：
```astro
---
import Layout from '../layouts/Layout.astro'
---

<Layout title="页面标题">
  <main>页面内容</main>
</Layout>
```

## 布局结构

### Layout.astro - 基础布局
**职责**: 提供整个网站的基础HTML结构

**主要功能**:
- HTML文档结构声明
- Meta标签和SEO配置
- 样式表和字体加载
- 主题初始化和CSS变量设置
- 全局JavaScript功能（滚动条、PhotoSwipe等）
- Swup页面过渡系统集成

**关键特性**:
- 支持深色/浅色主题切换
- 响应式设计和移动端适配
- 自定义滚动条集成
- 图片预览功能
- 页面过渡动画

### MainGridLayout.astro - 主网格布局
**职责**: 实现博客的主页面布局结构

**主要组件**:
- 导航栏 (Navbar)
- 横幅图片 (Banner)
- 侧边栏 (SideBar)
- 主内容区域
- 页脚 (Footer)
- 目录 (TOC)
- 返回顶部按钮

**布局特点**:
- CSS Grid布局系统
- 响应式设计（移动端/桌面端）
- 横幅图片支持
- 固定侧边栏和目录
- 平滑滚动和过渡效果

## 对外接口

### Layout.astro Props
```typescript
interface Props {
  title?: string;        // 页面标题
  banner?: string;       // 横幅图片路径
  description?: string;  // 页面描述
  lang?: string;         // 页面语言
  setOGTypeArticle?: boolean; // OpenGraph类型
}
```

### MainGridLayout.astro Props
```typescript
interface Props {
  title?: string;        // 页面标题
  banner?: string;       // 横幅图片路径
  description?: string;  // 页面描述
  lang?: string;         // 页面语言
  setOGTypeArticle?: boolean; // OpenGraph类型
  headings?: MarkdownHeading[]; // 文章标题列表（用于目录）
}
```

## 关键依赖与配置

### 样式依赖
- **TailwindCSS**: 主要CSS框架
- **Katex**: 数学公式渲染
- **OverlayScrollbars**: 自定义滚动条
- **PostCSS**: CSS处理工具

### 功能依赖
- **@swup/astro**: 页面过渡动画
- **photoswipe**: 图片预览
- **@fontsource/roboto**: 字体加载
- **@iconify**: 图标系统

### 内部依赖
- `@/config` - 网站配置
- `@/constants/*` - 布局常量
- `@/components/*` - UI组件
- `@/utils/*` - 工具函数

## CSS变量系统

### 主题色变量
- `--hue`: 主题色相
- `--primary`: 主色调
- `--page-bg`: 页面背景色
- `--card-bg`: 卡片背景色

### 布局变量
- `--page-width`: 页面最大宽度
- `--banner-height`: 横幅高度
- `--banner-height-extend`: 扩展横幅高度
- `--toc-width`: 目录宽度

## 响应式设计

### 断点配置
- 移动端: 默认样式
- 平板: `md:` 前缀 (768px+)
- 桌面: `lg:` 前缀 (1024px+)
- 大屏: `2xl:` 前缀 (1536px+)

### 布局适配
- 移动端: 单列布局，折叠式导航
- 桌面端: 网格布局，固定侧边栏和目录

## 测试与质量

### 建议的测试策略
- **响应式测试**: 验证不同屏幕尺寸的布局
- **主题切换测试**: 确保深色/浅色主题正常工作
- **性能测试**: 检查布局加载和渲染性能
- **可访问性测试**: 验证布局的可访问性

### 优化建议
- 优化CSS加载顺序
- 减少布局重绘和重排
- 使用CSS containment优化性能

## 常见问题 (FAQ)

### Q: 如何修改网站主题色？
A: 修改 `src/config.ts` 中的 `themeColor.hue` 值。

### Q: 如何添加新的CSS变量？
A: 在布局模板中添加 `define:vars` 或直接在CSS中定义。

### Q: 如何自定义横幅图片？
A: 在 `src/config.ts` 中配置 `banner` 相关选项。

### Q: 如何修改布局结构？
A: 编辑 `MainGridLayout.astro` 文件，调整组件顺序和样式。

## 扩展指南

### 添加新布局
1. 创建新的 `.astro` 文件
2. 定义Props接口
3. 实现HTML结构和样式
4. 在页面中使用新布局

### 自定义主题
1. 修改CSS变量定义
2. 添加新的主题选项
3. 更新主题切换逻辑

### 添加新的响应式断点
1. 修改 `tailwind.config.cjs`
2. 更新布局中的响应式类名

## 相关文件清单

### 布局文件
- `Layout.astro` - 基础布局模板
- `MainGridLayout.astro` - 主网格布局

### 依赖配置
- `../../tailwind.config.cjs` - Tailwind配置
- `../../src/config.ts` - 网站配置
- `../../src/constants/constants.ts` - 布局常量

### 组件依赖
- `../components/Navbar.astro` - 导航栏
- `../components/Footer.astro` - 页脚
- `../components/widget/SideBar.astro` - 侧边栏
- `../components/widget/TOC.astro` - 目录

### 样式文件
- `../styles/transition.css` - 过渡动画样式
- `../styles/scrollbar.css` - 滚动条样式
- `../styles/photoswipe.css` - 图片预览样式