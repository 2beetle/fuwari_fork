[根目录](../../CLAUDE.md) > [src](../) > **assets**

# Assets 模块文档

## 变更记录 (Changelog)

### 2025-11-26
- 初始化资源模块文档
- 分析静态资源结构和组织

## 模块职责

`src/assets` 目录包含项目的静态资源文件，主要是图片资源。这些资源通过Astro的构建系统处理和优化，为网站提供图片、字体、图标等媒体内容。

## 入口与启动

### 资源目录结构
```
src/assets/
└── images/
    ├── avatar_fix.png      # 作者头像
    ├── banner.png          # 网站横幅
    ├── demo-avatar.png     # 示例头像
    └── demo-banner.png     # 示例横幅
```

### 资源引用方式
```astro
<!-- 在Astro组件中引用 -->
<img src="@assets/images/avatar_fix.png" alt="头像">

<!-- 使用ImageWrapper组件 -->
<ImageWrapper src="@assets/images/banner.png" alt="横幅" />
```

## 资源类型

### 图片资源
- **PNG格式**: 适合需要透明背景的图片
- **JPEG格式**: 适合照片和复杂图像
- **SVG格式**: 适合图标和简单图形（如果有）

### 命名约定
- 使用描述性的文件名
- 小写字母和连字符分隔
- 包含尺寸或用途信息

## 资源优化

### 图片优化
- 使用Astro的 `<Image />` 组件进行自动优化
- 支持多种格式和尺寸生成
- 响应式图片处理

### 构建处理
- 资源文件被复制到构建输出目录
- 可以通过URL直接访问
- 支持版本控制和缓存

## 使用场景

### 网站配置中的资源
- 用户头像 (`profileConfig.avatar`)
- 网站横幅 (`siteConfig.banner.src`)
- 文章封面图片

### 组件中的资源
- 用户界面图标
- 装饰性图片
- 示例和演示图片

### 内容中的资源
- 文章内嵌图片（通常放在content目录）
- 页面装饰元素
- 功能性图标

## 资源管理

### 文件组织
- 按类型或功能分组
- 避免过深的目录结构
- 使用清晰的命名规范

### 版本控制
- 包含必要的资源文件
- 排除过大的开发用文件
- 使用适当的Git LFS策略

## 性能优化建议

### 图片优化
1. 选择合适的图片格式
2. 压缩图片文件大小
3. 使用响应式图片
4. 启用懒加载

### 加载优化
1. 关键图片优先加载
2. 使用现代图片格式
3. 实现渐进式加载
4. 配置适当的缓存策略

## 常见问题 (FAQ)

### Q: 如何添加新的图片资源？
A: 将图片文件放到相应的子目录中，然后在组件中引用。

### Q: 资源路径如何处理？
A: 使用 `@assets/` 别名进行相对路径引用。

### Q: 图片如何优化？
A: 使用Astro的Image组件或配置图片优化插件。

### Q: 如何处理不同尺寸的设备？
A: 使用响应式图片和srcset属性。

## 扩展指南

### 添加新的资源类型
1. 创建相应的子目录
2. 定义引用规范
3. 更新构建配置

### 资源自动化
1. 添加图片压缩工具
2. 实现资源版本控制
3. 配置CDN部署

### 主题资源
1. 为不同主题准备资源
2. 实现动态资源切换
3. 优化资源加载策略

## 相关文件清单

### 资源文件
- `images/avatar_fix.png` - 作者头像
- `images/banner.png` - 网站横幅
- `images/demo-avatar.png` - 示例头像
- `images/demo-banner.png` - 示例横幅

### 配置文件
- `../../src/config.ts` - 资源路径配置
- `../../astro.config.mjs` - 资源处理配置

### 处理工具
- `sharp` - 图片处理库（在package.json中）
- Astro内置的资源优化功能

### 使用示例
- `../components/widget/Profile.astro` - 头像使用
- `../layouts/MainGridLayout.astro` - 横幅使用
- `../components/misc/ImageWrapper.astro` - 图片包装组件