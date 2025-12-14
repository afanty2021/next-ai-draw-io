[根目录](../../CLAUDE.md) > **contexts**

# Contexts 模块 - React Context 状态管理

> 更新时间：2025-12-14 09:59:11

## 模块职责

提供全局状态管理，通过 React Context API 管理图表状态、用户交互和应用级状态。确保组件间的数据共享和状态同步。

## 入口与启动

### DiagramContext - 图表上下文
- **文件**: `diagram-context.tsx`
- **职责**:
  - 管理图表 XML 状态
  - 处理图表历史版本
  - 协调 Draw.io 嵌入组件
  - 管理导出功能
  - 处理文件保存

### Context 提供者
```typescript
// 在 layout.tsx 中包裹整个应用
<DiagramProvider>{children}</DiagramProvider>
```

## 对外接口

### Context 类型定义

```typescript
interface DiagramContextType {
  // 状态
  chartXML: string                    // 当前图表 XML
  latestSvg: string                   // 最新 SVG 渲染
  diagramHistory: DiagramSnapshot[]   // 历史版本
  isDrawioReady: boolean              // Draw.io 加载状态

  // 方法
  loadDiagram: (chart: string) => string | null
  handleExport: () => void
  handleExportWithoutHistory: () => void
  clearDiagram: () => void
  saveDiagramToFile: (filename: string, format: ExportFormat) => void

  // 引用
  drawioRef: React.Ref<DrawIoEmbedRef | null>
  resolverRef: React.Ref<((value: string) => void) | null>

  // 生命周期
  handleDiagramExport: (data: any) => void
  onDrawioLoad: () => void
  resetDrawioReady: () => void
}
```

### Hook 使用
```typescript
import { useDiagram } from '@/contexts/diagram-context'

const {
  chartXML,
  loadDiagram,
  handleExport,
  isDrawioReady
} = useDiagram()
```

## 关键依赖与配置

### 外部依赖
- **react-drawio**: Draw.io 嵌入组件类型定义
- **@/lib/utils**: XML 处理工具函数
- **@/components/chat-panel**: 存储键常量

### 内部状态管理
```typescript
const [chartXML, setChartXML] = useState<string>("")
const [latestSvg, setLatestSvg] = useState<string>("")
const [diagramHistory, setDiagramHistory] = useState<DiagramSnapshot[]>([])
const [isDrawioReady, setIsDrawioReady] = useState(false)
```

## 数据模型

### DiagramSnapshot - 历史快照
```typescript
interface DiagramSnapshot {
  svg: string      // SVG 内容
  xml: string      // XML 内容
  timestamp?: Date // 时间戳（可选）
}
```

### ExportFormat - 导出格式
```typescript
export type ExportFormat = 'drawio' | 'svg' | 'png'
```

## 核心功能实现

### 图表加载和验证
```typescript
const loadDiagram = (chart: string, skipValidation?: boolean): string | null => {
  if (!skipValidation) {
    const fixedXml = validateAndFixXml(chart)
    if (!fixedXml) {
      console.error("[DiagramContext] Invalid XML provided")
      return null
    }
    setChartXML(fixedXml)
    return fixedXml
  }
  setChartXML(chart)
  return chart
}
```

### 历史管理
- **自动快照**: 每次 AI 编辑前自动保存
- **手动导出**: 用户通过工具栏导出
- **历史去重**: 避免重复的快照

### 导出功能
- **drawio 格式**: 保存 XML 原始内容
- **SVG 格式**: 保存当前 SVG 渲染
- **PNG 格式**: 通过 Draw.io 转换导出

### 错误处理
- XML 验证失败处理
- Draw.io 未就绪处理
- 导出失败处理

## 测试与质量

### 测试覆盖
- ❌ 无 Context 测试
- ❌ 无状态管理测试
- ❌ 无集成测试

### 质量保证
- TypeScript 严格模式
- 完整的类型定义
- 错误边界处理

### 建议的测试策略
1. **单元测试** (Jest + React Testing Library)
   ```typescript
   // 测试示例
   test('should load diagram with XML validation', () => {
     const { result } = renderHook(() => useDiagram(), { wrapper: DiagramProvider })
     const fixedXml = result.current.loadDiagram(invalidXml)
     expect(fixedXml).toBe(validXml)
   })
   ```

2. **集成测试**
   - 测试 Context Provider 包裹
   - 测试跨组件状态共享
   - 测试历史管理

3. **E2E 测试**
   - 测试图表加载流程
   - 测试历史恢复功能
   - 测试导出功能

## 常见问题 (FAQ)

### Q: 为什么使用 Context 而不是 Redux？
A: 项目规模适中，Context 足够使用，减少了样板代码。

### Q: 如何优化 Context 性能？
A: 考虑拆分多个 Context，或使用 useReducer 替代多个 useState。

### Q: 如何处理大文件？
A: 当前实现会压缩历史记录，对于大型图表建议限制历史数量。

### Q: 如何实现持久化？
A: 通过 localStorage 保存，目前已有基本实现。

## 性能优化建议

### 已实现的优化
1. ** useRef 管理**
   - 避免不必要的重渲染
   - 管理外部引用

2. **条件更新**
   - 检查内容变化再更新状态
   - 避免重复渲染

### 可进一步优化
1. **使用 useMemo**
   ```typescript
   const contextValue = useMemo(() => ({
     chartXML,
     loadDiagram,
     // ...其他值
   }), [chartXML, /* 其他依赖 */])
   ```

2. **历史记录优化**
   - 实现最大历史数量限制
   - 使用 LRU 缓存策略

3. **Debounce 更新**
   - 对频繁的 XML 更新进行防抖

## 相关文件清单

### 核心文件
- `diagram-context.tsx` - 图表状态管理 Context

### 使用此 Context 的组件
- `app/page.tsx` - 主页面
- `components/chat-panel.tsx` - 聊天面板
- `components/save-dialog.tsx` - 保存对话框
- `components/history-dialog.tsx` - 历史对话框

### 相关工具函数
- `lib/utils.ts` - XML 处理函数
- `lib/storage.ts` - 本地存储工具

## 变更记录 (Changelog)

### 2025-12-14 09:59:11
- 📝 创建 contexts 模块文档
- 🔄 分析 DiagramContext 完整实现
- 📋 整理 Context 接口定义
- 💡 提供性能优化建议
- 🧪 制定测试策略