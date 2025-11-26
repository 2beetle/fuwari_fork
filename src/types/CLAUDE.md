[根目录](../../CLAUDE.md) > [src](../) > **types**

# Types 模块文档

## 变更记录 (Changelog)

### 2025-11-26
- 初始化类型定义模块文档
- 分析TypeScript类型系统

## 模块职责

`src/types` 目录包含项目的TypeScript类型定义。这些类型为整个项目提供类型安全，定义了配置接口、组件属性、数据结构等，确保代码的一致性和可维护性，同时提供优秀的开发体验和IDE支持。

## 入口与启动

### 类型定义结构
```
src/types/
└── config.ts           # 主要配置类型定义
```

### 类型导入方式
```typescript
import type { SiteConfig, NavBarConfig } from '@/types/config'
import type { BlogPostData } from '@/types/config'
```

## 类型定义详解

### config.ts - 配置类型

**主要类型接口**:

#### SiteConfig - 网站配置
```typescript
export type SiteConfig = {
  title: string;              // 网站标题
  subtitle: string;           // 网站副标题
  lang: string;               // 默认语言
  themeColor: {               // 主题色配置
    hue: number;              // 色相值 (0-360)
    fixed: boolean;           // 是否固定主题色
  };
  banner: {                   // 横幅配置
    enable: boolean;          // 是否启用横幅
    src: string;              // 横幅图片路径
    position?: "top" | "center" | "bottom"; // 图片位置
    credit: {                 // 图片版权信息
      enable: boolean;
      text: string;
      url?: string;
    };
  };
  toc: {                      // 目录配置
    enable: boolean;          // 是否启用目录
    depth: 1 | 2 | 3;         // 目录深度
  };
  favicon: Favicon[];         // 网站图标列表
};
```

#### BlogPostData - 文章数据
```typescript
export type BlogPostData = {
  body: string;               // 文章内容
  title: string;              // 文章标题
  published: Date;            // 发布日期
  description: string;        // 文章描述
  tags: string[];             // 标签列表
  draft?: boolean;            // 草稿状态
  image?: string;             // 封面图片
  category?: string;          // 分类
  prevTitle?: string;         // 上一篇标题
  prevSlug?: string;          // 上一篇链接
  nextTitle?: string;         // 下一篇标题
  nextSlug?: string;          // 下一篇链接
};
```

#### 其他重要类型
```typescript
// 导航栏配置
export type NavBarConfig = {
  links: (NavBarLink | LinkPreset)[];
};

// 用户资料配置
export type ProfileConfig = {
  avatar?: string;
  name: string;
  bio?: string;
  links: {
    name: string;
    url: string;
    icon: string;
  }[];
};

// 许可证配置
export type LicenseConfig = {
  enable: boolean;
  name: string;
  url: string;
};

// 主题模式
export type LIGHT_DARK_MODE =
  | typeof LIGHT_MODE
  | typeof DARK_MODE
  | typeof AUTO_MODE;
```

### 枚举定义

#### LinkPreset - 导航预设
```typescript
export enum LinkPreset {
  Home = 0,
  Archive = 1,
  About = 2,
}
```

## 类型系统特性

### 严格模式配置
项目启用了TypeScript的严格模式：
- `strictNullChecks: true` - 严格的空值检查
- `allowJs: false` - 不允许JavaScript文件
- `declaration: true` - 生成声明文件

### 路径映射
在 `tsconfig.json` 中配置了路径映射：
```json
{
  "paths": {
    "@components/*": ["src/components/*"],
    "@assets/*": ["src/assets/*"],
    "@constants/*": ["src/constants/*"],
    "@utils/*": ["src/utils/*"],
    "@i18n/*": ["src/i18n/*"],
    "@layouts/*": ["src/layouts/*"],
    "@/*": ["src/*"]
  }
}
```

## 类型使用场景

### 配置文件类型
- `src/config.ts` - 网站主配置
- `src/content/config.ts` - 内容集合配置

### 组件属性类型
- Astro组件Props接口
- Svelte组件属性定义
- 布局模板参数

### 数据处理类型
- 内容集合Schema
- API响应数据
- 工具函数参数和返回值

## 类型安全特性

### 空值处理
- 使用可选操作符 `?`
- 明确的null和undefined处理
- 类型守卫和断言

### 联合类型和枚举
- 严格的字符串字面量类型
- 枚举值约束
- 联合类型检查

### 泛型支持
- 工具函数的泛型参数
- 组件Props的泛型约束
- 数据处理的类型推断

## 扩展指南

### 添加新的类型定义
1. 在相应文件中定义新接口
2. 使用描述性的属性名
3. 添加必要的注释和文档
4. 考虑向后兼容性

### 类型验证
1. 使用zod等库进行运行时验证
2. 添加类型守卫函数
3. 实现自定义类型检查

### 类型优化
1. 使用工具类型简化代码
2. 避免过深的嵌套结构
3. 合理使用可选属性

## 开发工具集成

### IDE支持
- Visual Studio Code的TypeScript支持
- WebStorm/PhpStorm的类型检查
- 其他支持TS的编辑器

### 类型检查
- 编译时类型检查
- ESLint集成
- 代码导航和重构

## 常见问题 (FAQ)

### Q: 如何扩展现有类型？
A: 使用TypeScript的扩展语法或创建新的接口继承原有类型。

### Q: 如何处理外部库的类型？
A: 使用 `@types/` 包或创建自定义声明文件。

### Q: 类型错误如何调试？
A: 查看编译错误信息，使用IDE的类型检查功能。

### Q: 如何保持类型的一致性？
A: 使用统一的命名规范，定期审查类型定义。

## 相关文件清单

### 类型文件
- `config.ts` - 主要配置类型定义

### 配置文件
- `../../tsconfig.json` - TypeScript配置
- `../../src/config.ts` - 配置实现
- `../../src/content/config.ts` - 内容配置

### 使用示例
- `../layouts/Layout.astro` - 布局类型使用
- `../components/*` - 组件类型定义
- `../utils/*` - 工具函数类型
- `../pages/*` - 页面参数类型

### 类型检查工具
- `../../package.json` - TypeScript版本
- `../../biome.json` - 代码质量工具