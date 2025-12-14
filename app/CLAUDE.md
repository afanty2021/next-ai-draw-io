[根目录](../../CLAUDE.md) > **app**

# App 模块 - Next.js 应用路由

> 更新时间：2025-12-14 09:59:11

## 模块职责

负责整个应用的页面路由、API 端点和全局配置。采用 Next.js 13+ 的 App Router 架构，支持服务端组件、流式渲染和 API 路由。

## 入口与启动

### 根布局文件
- **文件**: `layout.tsx`
- **职责**:
  - 定义全局 HTML 结构
  - 配置字体（Plus Jakarta Sans + JetBrains Mono）
  - 设置元数据和 SEO
  - 集成 Google Analytics
  - 提供 DiagramProvider 上下文
  - 配置 JSON-LD 结构化数据

### 主页面
- **文件**: `page.tsx`
- **职责**:
  - 实现响应式双面板布局（Draw.io + 聊天面板）
  - 管理主题切换（亮色/暗色）
  - 处理键盘快捷键（Ctrl+B 切换聊天）
  - 集成 DrawIoEmbed 组件

## 对外接口

### API 路由

#### `/api/chat` - 聊天接口
- **文件**: `route.ts`
- **方法**: POST
- **功能**:
  - 处理用户聊天消息
  - 集成多 AI 提供商
  - 支持文件上传（图片、PDF）
  - 实现流式响应
  - 缓存机制
  - Token 计数和配额管理
- **特性**:
  - 最大文件大小: 2MB
  - 最大文件数: 5
  - 响应缓存
  - 错误处理和验证

#### `/api/config` - 配置接口
- **文件**: `route.ts`
- **方法**: GET
- **功能**: 返回应用配置信息
  - 访问码要求状态
  - 每日请求限制
  - Token 使用限制
  - TPM 限制

#### `/api/log-feedback` - 反馈日志
- **文件**: `route.ts`
- **方法**: POST
- **功能**: 记录用户反馈

#### `/api/log-save` - 保存日志
- **文件**: `route.ts`
- **方法**: POST
- **功能**: 记录图表保存操作

#### `/api/verify-access-code` - 访问码验证
- **文件**: `route.ts`
- **方法**: POST
- **功能**: 验证访问码有效性

### 页面路由

#### `/` - 主页
- 默认图表编辑器页面

#### `/about` - 关于页面
- 项目介绍和功能说明

#### `/about/cn` - 中文关于页面
- 中文版项目介绍

#### `/about/ja` - 日文关于页面
- 日文版项目介绍

#### `/robots.txt` - SEO 配置
- 搜索引擎爬虫规则

#### `/sitemap.xml` - 站点地图
- SEO 优化

## 关键依赖与配置

### 环境变量
- `NEXT_PUBLIC_DRAWIO_BASE_URL`: Draw.io 嵌入 URL（默认: embed.diagrams.net）
- `NEXT_PUBLIC_GA_ID`: Google Analytics ID
- `AI_PROVIDER`: 默认 AI 提供商
- `AI_MODEL`: 默认 AI 模型
- `ACCESS_CODE_LIST`: 访问控制密码列表

### 中间件
- 使用 Next.js 的中间件处理请求
- 支持国际化（i18n）

## 数据模型

### 聊天消息结构
```typescript
interface Message {
  id: string
  role: 'user' | 'assistant' | 'system'
  content: string | Part[]
  parts?: Part[]
}
```

### API 响应格式
```typescript
interface ChatResponse {
  choices: ChatChoice[]
  usage?: TokenUsage
}
```

## 测试与质量

### 测试覆盖
- ❌ 无自动化测试
- ❌ 无 API 测试
- ❌ 无 E2E 测试

### 质量工具
- 使用 Biome 进行代码检查
- TypeScript 严格模式
- ESLint 配置（继承 Next.js）

### 建议的测试策略
1. **API 路由测试**
   - 测试 `/api/chat` 的各种输入
   - 测试文件上传功能
   - 测试错误处理

2. **页面渲染测试**
   - 测试 SSR 渲染
   - 测试元数据生成
   - 测试 SEO 标签

## 常见问题 (FAQ)

### Q: 如何添加新的 API 路由？
A: 在 `app/api` 目录下创建新的文件夹和 `route.ts` 文件。

### Q: 如何配置自定义中间件？
A: 在项目根目录创建 `middleware.ts` 文件。

### Q: 如何实现国际化？
A: 使用 Next.js 的 i18n 配置，支持动态路由 `[locale]`。

### Q: 如何优化 API 性能？
A: 考虑使用 Edge Functions、缓存策略和流式响应。

## 相关文件清单

### 核心文件
- `layout.tsx` - 根布局
- `page.tsx` - 主页
- `globals.css` - 全局样式
- `robots.ts` - SEO 配置
- `sitemap.ts` - 站点地图

### API 路由
- `api/chat/route.ts` - 聊天 API
- `api/config/route.ts` - 配置 API
- `api/log-feedback/route.ts` - 反馈日志
- `api/log-save/route.ts` - 保存日志
- `api/verify-access-code/route.ts` - 访问验证

### 静态资源
- `favicon.ico` - 网站图标

## 变更记录 (Changelog)

### 2025-12-14 09:59:11
- 📝 创建 app 模块文档
- 🔍 分析所有 API 路由结构
- 📋 列出核心依赖和配置
- ⚠️ 识别缺少测试覆盖
- 💡 提供测试建议