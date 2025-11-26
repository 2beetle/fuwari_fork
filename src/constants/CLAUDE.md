[根目录](../../CLAUDE.md) > [src](../) > **constants**

# Constants 模块文档

## 变更记录 (Changelog)

### 2025-11-26
- 初始化常量模块文档
- 分析常量定义和使用场景

## 模块职责

`src/constants` 目录包含项目的全局常量定义。这些常量提供了配置值、枚举定义、默认参数等，避免在代码中硬编码重复值，提高代码的可维护性和一致性。

## 入口与启动

### 常量文件结构
```
src/constants/
├── constants.ts         # 主要常量定义
└── icon.ts             # 图标常量定义
```

### 使用方式
```typescript
import { PAGE_SIZE, DEFAULT_THEME } from '@constants/constants'
import { defaultFavicons } from '@constants/icon'
```

## 常量定义详解

### constants.ts - 主要常量

**布局和UI常量**:
```typescript
// 分页配置
export const PAGE_SIZE = 8;                    // 每页显示的文章数量

// 主题模式
export const LIGHT_MODE = "light";             // 浅色主题
export const DARK_MODE = "dark";               // 深色主题
export const AUTO_MODE = "auto";               // 自动主题
export const DEFAULT_THEME = AUTO_MODE;        // 默认主题

// 布局尺寸
export const PAGE_WIDTH = 75;                  // 页面最大宽度 (rem)
export const BANNER_HEIGHT = 45;               // 横幅高度 (%)
export const BANNER_HEIGHT_HOME = 70;          // 首页横幅高度 (%)
export const BANNER_HEIGHT_EXTEND = 25;        // 横幅扩展高度 (%)

// 组件间距
export const MAIN_PANEL_OVERLAPS_BANNER_HEIGHT = 4.5; // 主面板重叠横幅高度 (rem)
```

**功能配置常量**:
```typescript
// 动画延迟
export const CONTENT_DELAY = 300;              // 内容加载延迟 (ms)
export const PAGE_TRANSITION_DURATION = 300;   // 页面过渡持续时间 (ms)
```

### icon.ts - 图标常量

**默认网站图标**:
```typescript
export const defaultFavicons: Favicon[] = [
  {
    src: '/favicon/favicon-light-32.png',
    theme: 'light',
    sizes: '32x32',
  },
  {
    src: '/favicon/favicon-dark-32.png',
    theme: 'dark',
    sizes: '32x32',
  },
  // 更多图标配置...
];
```

**图标定义**:
- 浅色主题图标
- 深色主题图标
- 不同尺寸的图标
- 图标格式和路径

## 常量分类

### 主题相关常量
- 主题模式枚举值
- 默认主题设置
- 主题切换相关配置

### 布局相关常量
- 页面尺寸和宽度
- 横幅高度配置
- 组件间距定义
- 响应式断点值

### 功能相关常量
- 分页大小配置
- 动画持续时间
- 加载延迟时间
- 缓存过期时间

### UI相关常量
- 默认图标配置
- 颜色值定义
- 字体大小配置
- 边距和填充值

## 常量使用场景

### 在组件中使用
```astro
---
import { PAGE_SIZE, BANNER_HEIGHT } from '@constants/constants'
---

<!-- 分页组件 -->
<Pagination pageSize={PAGE_SIZE} />

<!-- 横幅样式 -->
<div style={`height: ${BANNER_HEIGHT}vh`}>
```

### 在配置中使用
```typescript
import { DEFAULT_THEME, LIGHT_MODE } from '@constants/constants'

// 主题配置
const theme = DEFAULT_THEME || LIGHT_MODE;
```

### 在工具函数中使用
```typescript
import { CONTENT_DELAY } from '@constants/constants'

// 动画延迟
setTimeout(() => {
  // 执行动画
}, CONTENT_DELAY);
```

## 常量管理原则

### 命名规范
- 使用大写字母和下划线
- 名称要具有描述性
- 遵循一致的命名风格
- 避免缩写和模糊词汇

### 分类组织
- 按功能分组常量
- 使用注释说明用途
- 考虑逻辑关联性
- 便于查找和维护

### 值的选择
- 使用有意义的默认值
- 考虑可配置性
- 提供合理的范围
- 文档化特殊值

## 扩展指南

### 添加新常量
1. 确定常量的用途和范围
2. 选择合适的文件和位置
3. 使用清晰的命名规范
4. 添加必要的注释说明

### 常量重构
1. 检查重复的硬编码值
2. 提取为全局常量
3. 更新相关引用
4. 验证功能正确性

### 配置化常量
1. 考虑将常量改为可配置
2. 提供环境变量支持
3. 添加验证逻辑
4. 更新配置文档

## 测试与验证

### 常量值验证
- 确保常量值的有效性
- 检查范围和类型正确性
- 验证默认值合理性
- 测试边界情况

### 影响范围测试
- 检查常量修改的影响
- 验证相关功能正常
- 测试不同常量组合
- 确保向后兼容性

## 常见问题 (FAQ)

### Q: 何时应该使用常量？
A: 当值在多处使用、需要集中管理、或者有特殊含义时使用常量。

### Q: 常量和配置的区别？
A: 常量是固定值，配置是可变的参数。常量通常不需要用户修改。

### Q: 如何组织常量文件？
A: 按功能分类，使用清晰的文件结构，避免单个文件过大。

### Q: 常量值可以动态修改吗？
A: 常量通常不应该在运行时修改，如果需要可变值应该使用配置。

## 相关文件清单

### 常量文件
- `constants.ts` - 主要常量定义
- `icon.ts` - 图标常量定义

### 配置文件
- `../config.ts` - 网站配置
- `../../tsconfig.json` - TypeScript配置

### 使用示例
- `../layouts/Layout.astro` - 布局常量使用
- `../components/*` - 组件中的常量引用
- `../utils/*` - 工具函数中的常量使用
- `../pages/*` - 页面中的常量应用

### 依赖关系
- `../types/config.ts` - 类型定义
- `../utils/setting-utils.ts` - 设置管理