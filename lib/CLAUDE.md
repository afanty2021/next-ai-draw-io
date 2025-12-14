[根目录](../../CLAUDE.md) > **lib**

# Lib 模块 - 工具函数和配置

> 更新时间：2025-12-14 09:59:11

## 模块职责

提供所有工具函数、AI 配置、系统提示词和共享逻辑。是应用的核心基础设施，支撑图表生成、AI 集成和数据处理等功能。

## 入口与启动

### 核心 AI 配置
- **文件**: `ai-config.ts`
- **职责**: AI 模型和提供商配置管理

### AI 提供商
- **文件**: `ai-providers.ts`
- **职责**: 多 AI 提供商集成和配置

### 系统提示词
- **文件**: `system-prompts.ts`
- **职责**: 定义各模型的系统提示词

## 对外接口

### AI 配置接口

```typescript
// ai-providers.ts
export type ProviderName =
  | "bedrock" | "openai" | "anthropic"
  | "google" | "azure" | "ollama"
  | "openrouter" | "deepseek" | "siliconflow"

export interface ClientOverrides {
  provider?: string | null
  baseUrl?: string | null
  apiKey?: string | null
  modelId?: string | null
}

// 主要导出函数
export function getAIModel(config: ModelConfig): any
export function supportsPromptCaching(modelName: string): boolean
```

### 工具函数导出

```typescript
// utils.ts
export function extractDiagramXML(xmlString: string): string | null
export function validateAndFixXml(xml: string): string | null
export function createDownloadFile(data: string, filename: string, type: string): void

// storage.ts
export function getLocalStorageItem(key: string): string | null
export function setLocalStorageItem(key: string, value: string): void

// ai-config.ts
export function getModelName(): string
export function getTemperature(): number | undefined
```

## 关键依赖与配置

### AI SDK 依赖
```json
{
  "@ai-sdk/amazon-bedrock": "^3.0.70",
  "@ai-sdk/anthropic": "^2.0.44",
  "@ai-sdk/azure": "^2.0.69",
  "@ai-sdk/deepseek": "^1.0.30",
  "@ai-sdk/google": "^2.0.0",
  "@ai-sdk/openai": "^2.0.19",
  "@ai-sdk/react": "^2.0.107",
  "ai": "^5.0.89",
  "ollama-ai-provider-v2": "^1.5.4",
  "@openrouter/ai-sdk-provider": "^1.2.3"
}
```

### 处理依赖
```json
{
  "jsdom": "^26.0.0",
  "@xmldom/xmldom": "^0.9.8",
  "pako": "^2.1.0",
  "unpdf": "^1.4.0"
}
```

## 数据模型

### ModelConfig - 模型配置
```typescript
interface ModelConfig {
  model: any
  providerOptions?: any
  headers?: Record<string, string>
  modelId: string
}
```

### CachedResponse - 缓存响应
```typescript
interface CachedResponse {
  xml: string
  timestamp: number
  model: string
}
```

## 核心功能实现

### AI 提供商集成 (`ai-providers.ts`)
支持 9 个 AI 提供商：
1. **AWS Bedrock** - 企业级 AI 服务
2. **OpenAI** - GPT 系列模型
3. **Anthropic** - Claude 系列
4. **Google AI** - Gemini 系列
5. **Azure OpenAI** - 微软托管
6. **Ollama** - 本地模型
7. **OpenRouter** - 模型聚合
8. **DeepSeek** - 深度求索
9. **SiliconFlow** - 硅基流动

### 系统提示词策略 (`system-prompts.ts`)
- **默认提示词** (~1900 tokens): 通用型
- **扩展提示词** (~4500 tokens): 高性能模型
- **模型适配**: 根据 token 缓存策略自动选择

### XML 处理 (`utils.ts`)
- **XML 验证**: 确保格式正确
- **自动修复**: 修复常见 XML 错误
- **压缩优化**: 减少 token 使用

### 缓存机制 (`cached-responses.ts`)
- **响应缓存**: 避免重复请求
- **TTL 策略**: 24小时过期
- **Key 生成**: 基于消息内容的哈希

### PDF 处理 (`pdf-utils.ts`)
- **文本提取**: 从 PDF 提取内容
- **页面处理**: 支持多页 PDF
- **编码处理**: UTF-8 转换

### 令牌计数 (`token-counter.ts`)
- **多模型支持**: 不同模型的计数策略
- **优化建议**: Token 使用优化

## 测试与质量

### 测试覆盖
- ❌ 无工具函数测试
- ❌ 无 AI 集成测试
- ❌ 无 XML 处理测试

### 质量保证
- TypeScript 严格模式
- JSDoc 注释
- 错误处理

### 建议的测试策略
1. **单元测试**
   ```typescript
   // 测试 XML 处理
   test('should fix invalid XML', () => {
     const invalid = '<mxGraphModel><root></mxGraphModel>'
     const fixed = validateAndFixXml(invalid)
     expect(fixed).toContain('</root>')
   })
   ```

2. **集成测试**
   - 测试 AI 提供商切换
   - 测试缓存命中
   - 测试文件处理

3. **Mock 测试**
   - Mock AI SDK 响应
   - Mock 文件系统

## 常见问题 (FAQ)

### Q: 如何添加新的 AI 提供商？
A: 在 `ai-providers.ts` 中添加新的创建函数和类型定义。

### Q: 缓存策略是什么？
A: 基于消息内容的哈希，24小时 TTL，本地存储。

### Q: XML 验证规则？
A: 检查根元素、闭合标签、特殊字符转义。

### Q: 如何优化 Token 使用？
A: 使用扩展提示词、压缩历史消息、智能缓存。

## 性能优化

### 已实现
1. **响应缓存** - 避免重复请求
2. **历史压缩** - 减少 Token 使用
3. **延迟加载** - 按需加载模块

### 可优化
1. **代码分割**
   ```typescript
   // 动态导入大型库
   const pdfUtils = await import('./pdf-utils')
   ```

2. **Worker 线程**
   - XML 处理移至 Worker
   - PDF 解析异步化

3. **缓存策略优化**
   - LRU 缓存
   - 预加载热门响应

## 安全考虑

### API 密钥管理
- 服务端密钥不暴露给客户端
- 客户端密钥仅存储在 localStorage
- 访问控制和速率限制

### 输入验证
- XML 注入防护
- 文件类型验证
- 大小限制（2MB）

### 数据隐私
- 不在服务端存储用户数据
- 可选择本地部署
- 支持访问码保护

## 相关文件清单

### AI 集成
- `ai-config.ts` - AI 配置管理
- `ai-providers.ts` - 多提供商集成
- `system-prompts.ts` - 系统提示词

### 工具函数
- `utils.ts` - XML 和通用工具
- `token-counter.ts` - Token 计数
- `storage.ts` - 本地存储
- `pdf-utils.ts` - PDF 处理

### 缓存和监控
- `cached-responses.ts` - 响应缓存
- `langfuse.ts` - AI 监控集成

### React Hook
- `use-file-processor.tsx` - 文件处理 Hook
- `use-quota-manager.tsx` - 配额管理 Hook

## 变更记录 (Changelog)

### 2025-12-14 09:59:11
- 📝 创建 lib 模块文档
- 🤖 分析 AI 集成架构
- 🔧 整理工具函数接口
- 📊 制定缓存策略
- 🔒 列出安全考虑点