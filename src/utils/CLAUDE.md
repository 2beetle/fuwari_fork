[根目录](../../CLAUDE.md) > [src](../) > **utils**

# Utils 模块文档

## 变更记录 (Changelog)

### 2025-11-26
- 初始化工具模块文档
- 分析工具函数功能和接口

## 模块职责

`src/utils` 目录包含项目的所有工具函数和辅助类。这些模块提供了内容处理、URL管理、日期格式化、主题设置等通用功能，为其他模块提供可复用的业务逻辑和数据处理能力。

## 入口与启动

### 工具函数结构
```
src/utils/
├── content-utils.ts    # 内容处理工具
├── date-utils.ts       # 日期处理工具
├── setting-utils.ts    # 设置管理工具
└── url-utils.ts        # URL处理工具
```

### 使用方式
```typescript
import { getSortedPosts } from '@utils/content-utils'
import { formatDate } from '@utils/date-utils'
import { url } from '@utils/url-utils'
```

## 工具函数详解

### content-utils.ts - 内容处理工具

**主要功能**:
- 获取和排序博客文章
- 生成文章的前后导航链接
- 统计标签和分类数据
- 内容集合数据处理

**核心函数**:

```typescript
// 获取排序后的文章列表
export async function getSortedPosts()

// 获取标签列表及统计
export async function getTagList(): Promise<Tag[]>

// 获取分类列表及统计
export async function getCategoryList(): Promise<Category[]>
```

**数据处理流程**:
1. 从内容集合获取文章数据
2. 根据发布日期倒序排序
3. 过滤草稿文章（生产环境）
4. 生成文章间的前后关系
5. 统计标签和分类数据

### date-utils.ts - 日期处理工具

**主要功能**:
- 日期格式化
- 多语言日期显示
- 相对时间计算
- 时区处理

**核心函数**:
```typescript
// 格式化日期显示
export function formatDate(date: Date, lang?: string): string

// 计算相对时间
export function getRelativeTime(date: Date): string
```

### setting-utils.ts - 设置管理工具

**主要功能**:
- 主题设置保存和读取
- 用户偏好管理
- 本地存储操作
- 设置同步

**核心函数**:
```typescript
// 获取存储的主题设置
export function getStoredTheme(): string

// 设置主题
export function setTheme(theme: string): void

// 获取主题色相
export function getHue(): string

// 设置主题色相
export function setHue(hue: string): void
```

### url-utils.ts - URL处理工具

**主要功能**:
- URL构建和处理
- 路径比较和验证
- 文章URL生成
- 分类和标签URL处理

**核心函数**:
```typescript
// 构建完整URL
export function url(path: string): string

// 比较路径是否相等
export function pathsEqual(path1: string, path2: string): boolean

// 获取文章URL
export function getPostUrlBySlug(slug: string): string

// 获取分类URL
export function getCategoryUrl(category: string): string
```

## 对外接口

### 类型定义
```typescript
// 标签数据类型
export type Tag = {
  name: string;
  count: number;
};

// 分类数据类型
export type Category = {
  name: string;
  count: number;
  url: string;
};
```

### 函数签名
所有工具函数都导出明确的类型定义，支持TypeScript类型检查和智能提示。

## 关键依赖与配置

### Astro依赖
- `astro:content` - 内容集合API
- Astro的内置类型定义

### 内部依赖
- `@i18n/i18nKey` - 国际化键值
- `@i18n/translation` - 国际化翻译
- `@constants/constants` - 项目常量
- `@types/config` - 配置类型

### 浏览器API
- `localStorage` - 本地存储
- `window.matchMedia` - 媒体查询

## 性能优化

### 缓存策略
- 内容处理结果可以考虑缓存
- 设置变更时即时保存，避免频繁写入

### 计算优化
- 使用高效的数据结构和算法
- 避免重复的数据处理操作
- 合理使用异步函数

## 测试与质量

### 建议的测试策略
- **单元测试**: 每个工具函数的独立测试
- **集成测试**: 工具函数与内容系统的集成测试
- **边界测试**: 异常数据和边界条件测试
- **性能测试**: 大数据量下的性能测试

### 测试覆盖重点
- 日期格式化的多语言支持
- URL处理的边界情况
- 本地存储的失败处理
- 内容数据的完整性验证

## 常见问题 (FAQ)

### Q: 如何添加新的工具函数？
A: 在相应文件中添加新函数，确保导出和类型定义完整。

### Q: 工具函数如何处理错误？
A: 使用适当的错误处理机制，返回默认值或抛出明确错误。

### Q: 如何优化工具函数性能？
A: 使用缓存、避免重复计算、选择合适的算法。

### Q: 工具函数是否支持服务端渲染？
A: 大部分工具函数支持SSR，但要避免浏览器特定API。

## 扩展指南

### 添加新工具模块
1. 创建新的工具文件
2. 定义清晰的接口和类型
3. 编写完整的文档和示例
4. 添加相应的测试

### 增强现有功能
1. 添加更多的参数选项
2. 支持更多的数据格式
3. 优化性能和错误处理
4. 增加类型安全性

### 国际化支持
1. 在工具函数中添加多语言支持
2. 使用现有的国际化系统
3. 考虑不同地区的格式差异

## 相关文件清单

### 工具文件
- `content-utils.ts` - 内容处理工具
- `date-utils.ts` - 日期处理工具
- `setting-utils.ts` - 设置管理工具
- `url-utils.ts` - URL处理工具

### 依赖文件
- `../i18n/i18nKey.ts` - 国际化键值
- `../i18n/translation.ts` - 国际化翻译
- `../constants/constants.ts` - 项目常量
- `../types/config.ts` - 类型定义

### 使用场景
- `../pages/[...page].astro` - 页面路由
- `../components/*` - 组件开发
- `../layouts/*` - 布局模板
- `../content/config.ts` - 内容配置