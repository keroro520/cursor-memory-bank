# 经验知识库系统设计

## 一、文件结构

```
memory-bank/
├── lessons/                          # 经验知识库目录
│   ├── _index.md                     # 经验索引（按模块分类）
│   ├── [module-name].md              # 按模块组织的经验文件
│   └── _extraction-rules.md          # 经验提取规则（可选）
├── reflection/                       # 现有反思文档
└── archive/                          # 现有归档文档
```

## 二、经验文件格式（精简版）

### `memory-bank/lessons/[module-name].md`

```markdown
# Module: [模块名称]

**Last Updated**: YYYY-MM-DD
**Total Lessons**: N

---

## L001: [简要标题]

**Context**: 在实现用户认证功能时遇到的会话管理问题
**Challenge**: Token过期处理导致用户体验差
**Best Practice**:
- 使用 refresh token 机制
- 在 token 过期前 5 分钟自动刷新
- 提供静默刷新，避免用户感知

**Code Pattern**:
```typescript
// 推荐模式
const refreshBeforeExpiry = (expiresIn: number) => expiresIn - 300;
```

**Related**: [Task-2023-11-15], [auth-module]
**Tags**: #authentication #token-management

---

## L002: [下一条经验]
...
```

### `memory-bank/lessons/_index.md`

```markdown
# 经验知识库索引

**Last Updated**: YYYY-MM-DD
**Total Modules**: N
**Total Lessons**: M

## 按模块分类

### 🔐 Authentication & Authorization
- [authentication.md](authentication.md) - 8 lessons
- [authorization.md](authorization.md) - 5 lessons

### 🗄️ Database & Storage
- [database-migration.md](database-migration.md) - 12 lessons
- [data-modeling.md](data-modeling.md) - 7 lessons

### 🎨 Frontend & UI
- [component-design.md](component-design.md) - 15 lessons
- [state-management.md](state-management.md) - 10 lessons

### 🔧 Infrastructure & DevOps
- [deployment.md](deployment.md) - 6 lessons
- [monitoring.md](monitoring.md) - 4 lessons

## 按标签分类

### #performance
- [database-migration.md#L003]
- [component-design.md#L007]
- [state-management.md#L002]

### #security
- [authentication.md#L001]
- [authorization.md#L003]

## 最近更新

1. [2024-01-15] authentication.md - Added token refresh pattern
2. [2024-01-14] component-design.md - React Server Components best practices
3. [2024-01-10] database-migration.md - Zero-downtime migration strategy
```

## 三、工作流程集成

### 方案 A：扩展 `/archive` 命令（推荐）

在归档阶段自动提取经验教训：

```mermaid
graph TD
    Reflect["/reflect 完成"] -->
    Archive["/archive 开始"] -->
    CreateArchive["创建归档文档"] -->
    ExtractLessons["提取经验教训"] -->
    IdentifyModules["识别相关模块"] -->
    UpdateLessons["更新模块经验文件"] -->
    UpdateIndex["更新经验索引"] -->
    Complete["归档完成"]

    style ExtractLessons fill:#f9d77e,stroke:#d9b95c
    style UpdateLessons fill:#f9d77e,stroke:#d9b95c
```

### 方案 B：新增 `/lessons` 命令

专门用于经验管理：

```bash
/lessons extract   # 从最近的反思文档提取经验
/lessons search    # 搜索相关经验
/lessons review    # 回顾特定模块的经验
```

## 四、经验提取规则

### 自动提取触发条件

从 `reflection-[task_id].md` 中提取以下内容：

1. **Technical Lessons** → 技术经验
   - 新的技术洞察
   - 架构模式发现
   - 性能优化经验

2. **Process Lessons** → 流程经验
   - 工作流程改进
   - 协作模式
   - 估算经验

3. **Challenges & Solutions** → 问题解决方案
   - 遇到的挑战
   - 解决方案
   - 避免的陷阱

### 模块识别策略

1. **从代码路径识别**：
   - `src/auth/**` → authentication 模块
   - `src/components/**` → component-design 模块
   - `src/database/**` → database 模块

2. **从任务描述识别**：
   - 关键词匹配（authentication, database, UI, etc.）
   - 使用 AI 分析任务内容

3. **手动标记**（可选）：
   - 在 reflection 文档中手动标记模块

### 经验精简原则

每条经验应该：
- ✅ Context 不超过 2 句话
- ✅ Best Practice 3-5 条要点
- ✅ Code Pattern 不超过 10 行
- ✅ 可选的代码示例
- ❌ 避免冗长的解释
- ❌ 避免重复常识性内容

## 五、实现计划

### 5.1 文件创建

1. 创建目录结构
2. 创建模板文件
3. 创建经验索引

### 5.2 规则文件

创建新的规则文件：
- `.cursor/rules/isolation_rules/Core/lessons-extraction.mdc`
- 更新 archive 相关规则

### 5.3 命令扩展

**选项 1：扩展 `/archive` 命令**
- 在归档流程中添加经验提取步骤
- 更新 `.cursor/commands/archive.md`

**选项 2：新增 `/lessons` 命令**
- 创建 `.cursor/commands/lessons.md`
- 实现独立的经验管理命令

### 5.4 更新文档

- 更新 README.md
- 更新相关工作流文档
- 添加使用示例

## 六、示例场景

### 场景：完成用户认证功能开发

1. **开发过程**：
   - `/van` 初始化任务
   - `/plan` 规划实现
   - `/creative` 设计 OAuth 流程
   - `/build` 实现功能
   - `/reflect` 反思遇到的问题

2. **反思内容**（reflection-auth-2024-01.md）：
   ```markdown
   ## Key Lessons Learned

   **Technical:**
   - JWT token 过期处理需要 refresh token 机制
   - 使用 HttpOnly Cookie 存储 token 更安全
   - 需要实现静默刷新避免用户中断

   **Process:**
   - OAuth 集成比预期复杂，需要更多时间测试
   ```

3. **经验提取**（自动或在归档时）：
   - 识别模块：`authentication`
   - 提取经验：
     - Token 管理最佳实践
     - Cookie 安全策略
     - OAuth 集成注意事项
   - 更新 `memory-bank/lessons/authentication.md`
   - 更新索引

4. **结果**：
   ```markdown
   # Module: Authentication

   ## L001: JWT Token 过期处理

   **Context**: 实现用户认证时的 token 生命周期管理
   **Challenge**: Token 过期导致用户频繁登录，体验差
   **Best Practice**:
   - 使用 refresh token 机制
   - 在 access token 过期前自动刷新
   - 实现静默刷新，避免用户感知中断
   - 使用 HttpOnly Cookie 存储，防止 XSS

   **Code Pattern**:
   ```typescript
   // Refresh token 5 minutes before expiry
   const shouldRefresh = (expiresAt: number) => {
     return Date.now() >= expiresAt - 5 * 60 * 1000;
   };
   ```

   **Related**: [Task-Auth-2024-01]
   **Tags**: #authentication #jwt #security
   ```

## 七、优势与价值

1. **知识积累**：项目经验不断积累，形成团队知识库
2. **快速查询**：按模块分类，需要时快速找到相关经验
3. **避免重复错误**：新功能开发前查阅相关经验
4. **最佳实践传播**：团队成员共享最佳实践
5. **精简高效**：聚焦核心要点，避免冗余

## 八、已实现的高级功能

### 8.1 主动经验推荐（改进1）✅

在 `/plan` 命令执行时自动推荐相关经验：

**工作流程**：
```
/plan 执行 →
1. 分析任务描述，识别相关模块
2. 搜索 lessons/_index.md 匹配模块和标签
3. 读取相关经验文件
4. 按相关度排序（模块匹配、标签匹配、应用次数）
5. 显示前 5-10 条最相关经验
6. 在规划中引用相关经验
```

**示例输出**：
```markdown
## 📚 Relevant Experience from Knowledge Base

Found **3 lessons** from 2 module(s) that may help with this task:

### Authentication Module
- **L002: OAuth Integration Error Handling** #oauth #error-handling
  → See: `memory-bank/lessons/authentication.md#L002`

💡 Tip: OAuth integration is complex. Pay special attention to L002.
```

**价值**：
- ✅ 让经验从"被动查询"变为"主动推荐"
- ✅ 确保开发者在规划时就看到相关最佳实践
- ✅ 提高经验库的实际使用率

**实现文件**：
- `.cursor/commands/plan.md` - 添加经验查询步骤
- `.cursor/rules/isolation_rules/Core/lessons-recommendation.mdc` - 推荐规则

### 8.2 经验去重和合并机制（改进2）✅

在提取新经验前自动检测相似经验并合并：

**相似度检测**：
- 标签重叠度（≥50%）
- 关键词重叠度（≥70%）
- 主题相似性

**合并策略**：
- **高相似度（≥80%）**：合并到现有经验
  - 增强描述和最佳实践
  - 添加新代码示例
  - 更新 Related 字段
  - 添加合并注释

- **中等相似度（50-79%）**：创建新经验并交叉引用
  - 添加 "Related Lessons: See also L00X"

- **低相似度（<50%）**：正常创建新经验

**示例**：
```markdown
## L001: JWT Token Lifecycle Management

**Context**: When implementing user authentication with JWT tokens for session management
**Challenge**: Token expiration and security vulnerabilities cause poor UX and security risks
**Best Practice**:
- Implement refresh token mechanism (separate from access token)
- Auto-refresh access token 5 minutes before expiry
- Use HttpOnly cookies to prevent XSS attacks
- Rotate refresh tokens on each use for added security ← [Merged from Task-2024-03]
- Implement token blacklist for proper logout handling ← [Merged from Task-2024-03]

**Related**: [Task-Auth-2024-01], [Task-Auth-2024-03]

*Enhanced: 2024-01-20 - Added security best practices from Task-Auth-2024-03*
```

**价值**：
- ✅ 保持经验库精简
- ✅ 避免信息过载和冗余
- ✅ 经验质量随验证次数提升

**实现文件**：
- `.cursor/rules/isolation_rules/Core/lessons-extraction.mdc` - Step 3.5 去重检查

### 8.3 经验有效性追踪（改进3）✅

为每条经验添加元数据，追踪应用情况和成功率：

**追踪指标**：
```markdown
**Effectiveness** (tracked automatically):
- Created: 2024-01-10
- Applied: 12 times
- Last Applied: 2024-01-18
- Success Rate: 100% (12/12 successful)
- Status: Active
```

**Status 状态值**：
- **Active**: 经验有效且当前（默认）
- **Deprecated**: 经验已过时
- **Under Review**: 成功率低（<50%且≥3次应用）

**更新时机**：
在 `/reflect` 命令中评估经验应用效果：
- 成功：任务顺利完成，经验有帮助
- 部分：任务完成但经验可改进
- 失败：经验未能防止问题

**废弃标准**：
- 技术/方法已过时
- 有更好的经验取代
- 成功率 < 50%（≥5次应用）

**价值**：
- ✅ 识别最有价值的经验
- ✅ 及时发现和更新过时经验
- ✅ 数据驱动的知识库维护

**实现文件**：
- `lessons-extraction.mdc` - 元数据格式定义
- `.cursor/commands/reflect.md` - Step 4 追踪经验效果
- `memory-bank-templates/lessons/_templates/lesson-template.md` - 模板更新

### 8.4 层次化经验组织（改进4）✅

支持大型模块的层次化组织，按子主题分组：

**适用场景**：
- 模块经验 > 10 条
- 经验可分为多个子主题
- 需要细粒度组织

**目录结构**：
```
lessons/
├── _index.md
├── authentication/                    # 模块目录
│   ├── _module-index.md              # 模块概览
│   ├── jwt-basics.md                 # 子主题（5 lessons）
│   ├── oauth-integration.md          # 子主题（8 lessons）
│   └── session-management.md         # 子主题（4 lessons）
└── component-design.md               # 扁平结构（8 lessons）
```

**模块索引功能**：
- 所有子主题概览
- 快速参考（最常用、最近更新）
- 跨模块引用
- 统计数据

**优势**：
- ✅ 更好地组织大型模块
- ✅ 渐进式查找（概览→细节）
- ✅ 便于维护
- ✅ 向后兼容（可混用扁平和层次结构）

**实现文件**：
- `memory-bank-templates/lessons/HIERARCHICAL_STRUCTURE.md` - 设计文档
- `memory-bank-templates/lessons/_templates/module-index-template.md` - 模块索引模板
- `memory-bank-templates/lessons/database-example/` - 完整示例
- `.cursor/rules/isolation_rules/Core/memory-bank-paths.mdc` - 路径定义

## 九、未来扩展

1. **AI 辅助语义搜索**：使用自然语言查询经验（需 AI 集成）
2. **经验知识图谱**：可视化经验间关系和依赖
3. **自动验证**：为经验添加可执行测试
4. **经验模板库**：架构决策、检查清单、决策矩阵等
5. **跨项目共享**：导出/导入通用经验
6. **社区知识库**：团队级别的经验共享
