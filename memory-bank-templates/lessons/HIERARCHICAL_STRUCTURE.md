# Hierarchical Lessons Organization

## 概述

当单个模块积累的经验超过 10 条时，建议使用层次化组织结构，将经验按子主题分组，便于查找和维护。

## 何时使用层次化结构

### 使用场景
- ✅ 模块经验数量 > 10 条
- ✅ 经验可明确分为多个子主题
- ✅ 需要更细粒度的组织
- ✅ 模块范围广泛（如 authentication, database）

### 不使用场景
- ❌ 模块经验 < 10 条（保持扁平结构）
- ❌ 经验高度相关，难以分类
- ❌ 模块范围狭窄

## 目录结构对比

### 扁平结构（适用于小型模块）
```
lessons/
├── _index.md
├── utilities.md              # 5 lessons
├── testing.md                # 7 lessons
└── deployment.md             # 6 lessons
```

### 层次化结构（适用于大型模块）
```
lessons/
├── _index.md
├── authentication/           # Module directory
│   ├── _module-index.md     # Overview of all auth lessons
│   ├── jwt-basics.md        # Sub-topic: 5 lessons
│   ├── oauth-integration.md # Sub-topic: 8 lessons
│   └── session-management.md # Sub-topic: 6 lessons
├── database/
│   ├── _module-index.md
│   ├── migrations.md         # Sub-topic: 7 lessons
│   ├── query-optimization.md # Sub-topic: 9 lessons
│   └── transactions.md       # Sub-topic: 4 lessons
└── component-design.md      # Still flat (only 8 lessons total)
```

## 迁移流程

### 从扁平结构迁移到层次化

**步骤 1: 识别子主题**

分析现有经验，识别自然分组：
```
authentication.md 中的 15 条经验：
- L001-L005: JWT token 相关 → jwt-basics.md
- L006-L011: OAuth 集成相关 → oauth-integration.md
- L012-L015: 会话管理相关 → session-management.md
```

**步骤 2: 创建目录结构**
```bash
mkdir -p memory-bank/lessons/authentication
```

**步骤 3: 创建模块索引**

`memory-bank/lessons/authentication/_module-index.md`:
```markdown
# Module: Authentication

**Last Updated**: 2024-01-20
**Total Lessons**: 15 (across 3 sub-topics)
**Related Paths**: src/auth/, src/middleware/auth/

## Sub-Topics

### [JWT Basics](jwt-basics.md) - 5 lessons
Core JWT token management patterns and lifecycle

- L001: JWT Token Lifecycle Management
- L002: Token Refresh Strategies
- L003: Token Validation Best Practices
- L004: JWT Payload Design
- L005: Token Expiration Handling

### [OAuth Integration](oauth-integration.md) - 8 lessons
OAuth 2.0 provider integration and error handling

- L001: OAuth Flow Implementation
- L002: Provider Error Handling
- L003: State Parameter Management (CSRF)
- L004: Callback URL Configuration
- L005: Multi-Provider Support
- L006: OAuth Scope Management
- L007: Token Exchange Patterns
- L008: OAuth Security Considerations

### [Session Management](session-management.md) - 4 lessons
User session handling and persistence

- L001: Session Storage Strategies
- L002: Multi-Device Session Handling
- L003: Session Timeout Configuration
- L004: Remember Me Functionality

## Quick Reference

**Most Applied**:
1. jwt-basics.md#L001 - Applied 12 times
2. oauth-integration.md#L002 - Applied 8 times

**Recently Updated**:
1. [2024-01-20] oauth-integration.md - Added provider error handling
2. [2024-01-15] jwt-basics.md - Enhanced token refresh strategy
```

**步骤 4: 拆分经验到子主题文件**

移动相关经验到对应的子主题文件，**重新编号** lessons (每个文件从 L001 开始)。

**步骤 5: 更新主索引**

更新 `lessons/_index.md` 指向模块索引：
```markdown
### 🔐 Authentication & Security
- [authentication/](authentication/) - 15 lessons across 3 sub-topics
  *User authentication, OAuth, session management*
  - [JWT Basics](authentication/jwt-basics.md) - 5 lessons
  - [OAuth Integration](authentication/oauth-integration.md) - 8 lessons
  - [Session Management](authentication/session-management.md) - 4 lessons
```

## 文件模板

### 模块索引模板 (_module-index.md)

```markdown
# Module: [Module Name]

**Last Updated**: YYYY-MM-DD
**Total Lessons**: N (across M sub-topics)
**Related Paths**: src/path/to/module/

## Sub-Topics

### [Sub-Topic Name](subtopic-file.md) - X lessons
Brief description of what this sub-topic covers

- L001: Lesson title
- L002: Lesson title
- L003: Lesson title

### [Another Sub-Topic](another-file.md) - Y lessons
Brief description

- L001: Lesson title
- L002: Lesson title

## Quick Reference

**Most Applied**:
1. subtopic.md#L001 - Applied N times, Success Rate: X%
2. another.md#L003 - Applied M times, Success Rate: Y%

**Recently Updated**:
1. [YYYY-MM-DD] subtopic.md - Update description
2. [YYYY-MM-DD] another.md - Update description

**Deprecated**:
- ~~subtopic.md#L005~~ - Use subtopic.md#L009 instead

## Cross-References

Related modules:
- See also: [module-name/subtopic](../module-name/subtopic.md) for related patterns
```

### 子主题文件模板

格式与标准 lesson 文件相同，但：
- 每个子主题文件独立编号（从 L001 开始）
- 文件头部引用返回模块索引

```markdown
# Sub-Topic: [Topic Name]

**Module**: [Module Name] | [Back to Module Index](_module-index.md)
**Last Updated**: YYYY-MM-DD
**Total Lessons**: N
**Related Paths**: src/specific/path/

---

## L001: [Lesson Title]

**Context**: ...
**Challenge**: ...
**Best Practice**: ...

[... rest of lesson format ...]
```

## 引用规范

### 层次化结构中的引用

**在规划文档中**:
```markdown
## Relevant Lessons Referenced
- `lessons/authentication/jwt-basics.md#L001` - Token lifecycle management
- `lessons/authentication/oauth-integration.md#L002` - Provider error handling
```

**在主索引中**:
```markdown
## 🏷️ By Tag

### #jwt
- [authentication/jwt-basics.md#L001] - Token lifecycle management
- [authentication/jwt-basics.md#L002] - Token refresh strategies
```

**跨模块引用**:
```markdown
**Related Lessons**:
- See also: [api-design/error-handling.md#L003](../api-design/error-handling.md#L003)
- See also: [database/transactions.md#L002](../database/transactions.md#L002)
```

## 搜索和查找

### 扁平结构
```bash
# 查找所有 authentication 相关经验
cat memory-bank/lessons/authentication.md
```

### 层次化结构
```bash
# 查看模块概览
cat memory-bank/lessons/authentication/_module-index.md

# 查看特定子主题
cat memory-bank/lessons/authentication/jwt-basics.md

# 搜索所有 authentication 经验
grep -r "L0" memory-bank/lessons/authentication/
```

## 最佳实践

### 子主题划分原则

1. **按技术/功能划分**（推荐）
   ```
   authentication/
   ├── jwt-basics.md
   ├── oauth-integration.md
   └── session-management.md
   ```

2. **按生命周期阶段**（适用于流程类）
   ```
   deployment/
   ├── build-process.md
   ├── ci-cd-pipeline.md
   ├── staging-deployment.md
   └── production-deployment.md
   ```

3. **按性能/安全等维度**（适用于跨切关注点）
   ```
   database/
   ├── query-optimization.md
   ├── migrations.md
   ├── security.md
   └── backup-recovery.md
   ```

### 避免过度分层

❌ **不推荐**（过度分层）:
```
authentication/
├── jwt/
│   ├── basics/
│   │   ├── creation.md
│   │   └── validation.md
│   └── advanced/
│       └── refresh.md
└── oauth/
    ├── google/
    └── github/
```

✅ **推荐**（2层足够）:
```
authentication/
├── jwt-basics.md
├── jwt-advanced.md
└── oauth-integration.md
```

### 经验数量建议

- **子主题文件**: 4-12 条经验/文件
- **模块总数**: 15-50 条经验（超过 50 考虑拆分模块）
- **太少**: < 4 条经验考虑合并子主题
- **太多**: > 15 条经验考虑进一步拆分

## 示例：完整的层次化模块

```
memory-bank/lessons/
└── database/
    ├── _module-index.md          # 模块概览（25 lessons across 4 sub-topics）
    ├── migrations.md              # 7 lessons
    ├── query-optimization.md      # 9 lessons
    ├── transactions.md            # 5 lessons
    └── schema-design.md           # 4 lessons
```

`_module-index.md` 内容：
```markdown
# Module: Database

**Last Updated**: 2024-01-20
**Total Lessons**: 25 (across 4 sub-topics)
**Related Paths**: src/database/, src/models/, migrations/

## Sub-Topics

### [Migrations](migrations.md) - 7 lessons
Database schema migration strategies and best practices

### [Query Optimization](query-optimization.md) - 9 lessons
SQL query performance optimization techniques

### [Transactions](transactions.md) - 5 lessons
Transaction management and isolation levels

### [Schema Design](schema-design.md) - 4 lessons
Database schema design patterns

## Quick Reference

**Most Applied**:
1. query-optimization.md#L003 - Index strategies (15 applications, 93% success)
2. migrations.md#L001 - Zero-downtime migrations (12 applications, 100% success)

**Recently Updated**:
1. [2024-01-18] query-optimization.md - Added N+1 query prevention
2. [2024-01-10] transactions.md - Updated isolation level guidance
```

## 兼容性

- **向后兼容**: 扁平结构仍然完全支持
- **渐进迁移**: 可以混合使用扁平和层次化结构
- **工具支持**: 推荐和搜索功能支持两种结构

## 何时重构

触发重构的信号：
- 单个文件超过 15 条经验
- 经验查找困难（需要滚动查看）
- 出现明显的自然分组
- 团队反馈查找效率低
