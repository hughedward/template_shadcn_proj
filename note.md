# Shadcn/ui 完整安装配置指南

本文档记录了在 React + Vite + TypeScript 项目中完整安装和配置 Shadcn/ui 的详细步骤。

## 项目环境

- **框架**: React 19.1.1
- **构建工具**: Vite 7.1.2
- **语言**: TypeScript 5.8.3
- **包管理器**: pnpm
- **UI 库**: Shadcn/ui
- **样式**: Tailwind CSS v3

## 安装步骤

### 1. 创建 React + Vite 项目

```bash
# 创建新项目
pnpm create vite@latest my-shadcn-project -- --template react-ts
cd my-shadcn-project

# 安装依赖
pnpm install
```

### 2. 安装 Tailwind CSS

#### 2.1 安装 Tailwind CSS 相关依赖

```bash
# 安装 Tailwind CSS v3 及相关工具
pnpm add -D tailwindcss@^3.4.0 postcss autoprefixer
```

**注意**: 不要安装 `@tailwindcss/vite` (v4 版本)，因为 Shadcn/ui 目前主要支持 v3 版本。

#### 2.2 初始化 Tailwind CSS 配置

```bash
# 创建 tailwind.config.js 和 postcss.config.js
npx tailwindcss init -p
```

如果 `pnpm dlx` 命令失败，可以使用 `npx` 替代。

#### 2.3 配置 tailwind.config.js

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

#### 2.4 更新 src/index.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 3. 配置 TypeScript 路径别名

#### 3.1 安装 @types/node

```bash
pnpm add -D @types/node
```

#### 3.2 更新 tsconfig.json

```json
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },
    { "path": "./tsconfig.node.json" }
  ],
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

#### 3.3 更新 tsconfig.app.json

在 `compilerOptions` 中添加：

```json
{
  "compilerOptions": {
    // ... 其他配置
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

#### 3.4 更新 vite.config.ts

```typescript
import path from "path"
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
})
```

### 4. 初始化 Shadcn/ui

#### 4.1 运行初始化命令

```bash
pnpm dlx shadcn@latest init
```

#### 4.2 配置选项

在初始化过程中，选择以下配置：

- **TypeScript**: Yes
- **Style**: Default
- **Base color**: Slate
- **Global CSS file**: src/index.css
- **CSS variables**: Yes
- **Tailwind config**: tailwind.config.js
- **Components**: src/components
- **Utils**: src/lib/utils.ts
- **React Server Components**: No

#### 4.3 生成的文件

初始化完成后，会生成以下文件：

- `components.json` - Shadcn/ui 配置文件
- `src/lib/utils.ts` - 工具函数
- 更新的 `src/index.css` - 包含 CSS 变量
- 更新的 `tailwind.config.js` - 包含 Shadcn/ui 配置

### 5. 安装和使用组件

#### 5.1 安装组件

```bash
# 安装单个组件
pnpm dlx shadcn@latest add button

# 安装多个组件
pnpm dlx shadcn@latest add button card input

# 查看可用组件
pnpm dlx shadcn@latest add
```

#### 5.2 使用组件

```tsx
import { Button } from "@/components/ui/button"

function App() {
  return (
    <div className="p-8">
      <Button>Click me</Button>
    </div>
  )
}

export default App
```

### 6. 验证安装

#### 6.1 启动开发服务器

```bash
pnpm dev
```

#### 6.2 测试 Tailwind CSS

在组件中添加 Tailwind 类名，确保样式生效：

```tsx
<div className="bg-blue-500 text-white p-4 rounded-lg">
  Tailwind CSS 工作正常！
</div>
```

#### 6.3 测试路径别名

确保可以使用 `@/` 导入：

```tsx
import { cn } from "@/lib/utils"
```

## 常见问题和解决方案

### 问题 1: `pnpm dlx tailwindcss init -p` 失败

**错误**: 找不到二进制文件

**解决方案**:
```bash
# 使用 npx 替代
npx tailwindcss init -p

# 或直接调用本地安装的二进制文件
./node_modules/.bin/tailwindcss init -p
```

### 问题 2: Shadcn init 提示找不到 Tailwind 配置

**原因**: 
- 缺少 `tailwind.config.js` 文件
- 同时安装了 Tailwind CSS v3 和 v4

**解决方案**:
1. 确保只安装 Tailwind CSS v3
2. 移除 `@tailwindcss/vite` 插件
3. 使用传统的配置文件方式

### 问题 3: TypeScript 路径别名不工作

**解决方案**:
1. 确保 `@types/node` 已安装
2. 检查 `tsconfig.json` 和 `tsconfig.app.json` 中的路径配置
3. 确保 `vite.config.ts` 中配置了别名

### 问题 4: 样式不生效

**检查清单**:
1. `src/index.css` 是否包含 Tailwind 指令
2. `tailwind.config.js` 的 `content` 数组是否正确
3. CSS 文件是否在 `main.tsx` 中导入

## 项目结构

```
project-root/
├── components.json          # Shadcn/ui 配置
├── tailwind.config.js       # Tailwind CSS 配置
├── postcss.config.js        # PostCSS 配置
├── vite.config.ts           # Vite 配置（包含路径别名）
├── tsconfig.json            # TypeScript 根配置
├── tsconfig.app.json        # 应用 TypeScript 配置
├── package.json             # 项目依赖
└── src/
    ├── components/
    │   └── ui/              # Shadcn/ui 组件
    ├── lib/
    │   └── utils.ts         # 工具函数
    ├── index.css            # 全局样式（包含 Tailwind）
    └── main.tsx             # 应用入口
```

## 最终的关键配置文件

### components.json
```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "default",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "src/index.css",
    "baseColor": "slate",
    "cssVariables": true
  },
  "aliases": {
    "components": "src/components",
    "utils": "src/lib/utils"
  }
}
```

### src/index.css（最终版本）
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    /* ... 更多 CSS 变量 */
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    /* ... 更多暗色主题变量 */
  }
}

@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground;
  }
}
```

## 总结

通过以上步骤，你已经成功配置了一个完整的 Shadcn/ui 开发环境。关键要点：

1. **使用 Tailwind CSS v3**，避免与 v4 混用
2. **正确配置 TypeScript 路径别名**，确保 `@/` 导入正常工作
3. **按顺序执行配置步骤**，特别是先配置 Tailwind 再初始化 Shadcn
4. **使用 pnpm** 作为包管理器，提高安装效率

现在你可以开始使用 Shadcn/ui 构建美观的 React 应用了！