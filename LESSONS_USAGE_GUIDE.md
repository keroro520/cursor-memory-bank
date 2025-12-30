# Lessons Knowledge Base - Usage Guide

## 概述

经验知识库（Lessons Knowledge Base）是 Memory Bank v0.8+ 的新功能，自动从完成的任务中提取和组织最佳实践，按代码模块分类存储，形成项目特定的知识积累。

## 核心价值

1. **知识积累**：每个完成的任务都贡献经验，形成不断增长的知识库
2. **避免重复错误**：新功能开发前查阅相关模块经验，避免踩坑
3. **快速查询**：按模块和标签组织，快速找到相关经验
4. **最佳实践传播**：团队成员共享项目特定的最佳实践
5. **精简高效**：聚焦核心要点，每条经验控制在最小必要信息

## 工作流程

### 自动提取（推荐）

经验会在执行 `/archive` 命令时自动从反思文档中提取：

```
开发流程:
/van → /plan → /creative → /build → /reflect → /archive
                                                    ↓
                                              自动提取经验
                                                    ↓
                                        更新 lessons/[module].md
                                                    ↓
                                          更新 lessons/_index.md
```

**提取条件**：
- ✅ Level 2-4 任务（复杂度足够）
- ✅ 反思文档中有明确的经验教训
- ✅ 经验符合可操作、可复用、简洁的标准
- ❌ Level 1 任务通常不生成经验（太简单）

### 查询经验

1. **按模块查询**：
   - 打开 `memory-bank/lessons/_index.md`
   - 浏览模块分类，找到相关模块
   - 打开对应的模块文件查看经验

2. **按标签查询**：
   - 在索引文件中查看"By Tag"部分
   - 找到相关标签下的所有经验

3. **关键词搜索**：
   - 使用编辑器的搜索功能（Ctrl/Cmd+F）
   - 在 `lessons/` 目录中搜索关键词

## 文件结构

```
memory-bank/
└── lessons/
    ├── _index.md                    # 主索引（按模块和标签分类）
    ├── authentication.md            # 认证模块经验
    ├── database.md                  # 数据库模块经验
    ├── component-design.md          # 组件设计经验
    ├── api-design.md                # API设计经验
    └── _templates/                  # 模板（参考用）
        ├── lesson-template.md
        └── index-template.md
```

## 经验格式

每条经验遵循精简格式：

```markdown
## L001: [简短标题 - 最多8个词]

**Context**: [1-2句话描述应用场景]

**Challenge**: [1-2句话描述遇到的问题]

**Best Practice**:
- [可操作要点1]
- [可操作要点2]
- [可操作要点3]

**Code Pattern** (可选):
```language
// 推荐做法（最多10行）
```

**Anti-Pattern** (可选):
```language
// 避免这样做（最多5行）
```

**Related**: [任务ID], [模块名]
**Tags**: #tag1 #tag2 #tag3
```

## 示例场景

### 场景：开发用户认证功能

**1. 开发阶段**
```bash
/van Add user authentication with JWT
# Level 3 任务，需要完整工作流

/plan
# 规划认证系统架构

/creative
# 设计 JWT token 生命周期管理

/build
# 实现认证功能
# 遇到问题：token过期处理不当导致用户体验差

/reflect
# 反思阶段记录经验教训
```

**2. 反思文档内容** (`reflection-auth-2024-01.md`)
```markdown
## Key Lessons Learned

**Technical:**
- JWT token 过期处理需要 refresh token 机制
- 使用 HttpOnly Cookie 存储 token 更安全
- 需要实现静默刷新避免用户中断

**Challenges:**
- Token 过期导致用户频繁登录
- 最初使用 localStorage 存在 XSS 风险
```

**3. 归档阶段自动提取**
```bash
/archive
# 系统分析反思文档
# 识别模块：authentication
# 提取经验：JWT token 生命周期管理
# 创建/更新 lessons/authentication.md
# 更新 lessons/_index.md
```

**4. 生成的经验** (`lessons/authentication.md`)
```markdown
## L001: JWT Token Lifecycle Management

**Context**: 实现用户认证时的 token 生命周期管理

**Challenge**: Token 过期导致用户频繁登录，体验差

**Best Practice**:
- 使用 refresh token 机制（长期有效）配合 access token（短期）
- 在 access token 过期前5分钟自动刷新
- 实现静默刷新，避免用户感知中断
- 使用 HttpOnly Cookie 存储，防止 XSS 攻击

**Code Pattern**:
```typescript
const shouldRefresh = (expiresAt: number) => {
  return Date.now() >= expiresAt - 5 * 60 * 1000;
};
```

**Anti-Pattern**:
```typescript
// 避免：存储在 localStorage（XSS风险）
localStorage.setItem('token', accessToken);
```

**Related**: [Task-Auth-2024-01]
**Tags**: #authentication #jwt #security
```

**5. 未来开发时查询**

下次开发OAuth集成时：
```bash
# 查询认证相关经验
cat memory-bank/lessons/authentication.md

# 或在索引中搜索
grep -r "authentication" memory-bank/lessons/_index.md
```

## 模块识别

系统使用以下策略自动识别模块：

### 1. 代码路径映射
```
src/auth/**           → authentication
src/components/**     → component-design
src/api/**            → api-design
src/database/**       → database
src/services/**       → services
src/utils/**          → utilities
```

### 2. 关键词匹配
```
"authentication", "login", "JWT"     → authentication
"component", "React", "UI"           → component-design
"database", "SQL", "migration"       → database
"API", "endpoint", "REST"            → api-design
```

### 3. 任务类型
- Bug fix → 相关模块
- Feature → 主要涉及模块
- Refactoring → 重构的模块

## 质量标准

### 提取标准

经验必须满足所有条件才会被提取：
- ✅ **可操作**：提供具体的未来指导
- ✅ **可复用**：适用于同模块的未来任务
- ✅ **简洁**：可用3-5个要点表达
- ✅ **技术/流程相关**：关于代码、架构或工作流程

不会提取：
- ❌ 仅适用于特定任务的细节
- ❌ 显而易见的常识
- ❌ 没有可操作指导的模糊观察
- ❌ 一次性情况问题

### 格式标准

每条经验应该：
- Context：≤ 2 句话
- Challenge：≤ 2 句话
- Best Practice：3-5 条要点
- Code Pattern：≤ 10 行（可选）
- Anti-Pattern：≤ 5 行（可选）

## 常见标签

使用一致的标签便于跨模块搜索：

**技术标签**：
- #performance, #security, #scalability, #testing
- #architecture, #patterns, #refactoring
- #api, #database, #frontend, #backend

**流程标签**：
- #workflow, #debugging, #estimation, #planning
- #collaboration, #documentation

**领域标签**：
- #authentication, #authorization, #data-modeling
- #state-management, #error-handling

## 最佳实践

### 开发新功能前

1. 查阅相关模块的经验
2. 搜索相关标签
3. 参考推荐的代码模式
4. 避免记录的反模式

### 完成任务后

1. 在 `/reflect` 阶段详细记录经验教训
2. 明确区分技术和流程经验
3. 记录具体的挑战和解决方案
4. 让系统在 `/archive` 阶段自动提取

### 维护知识库

- 定期回顾经验，确保仍然相关
- 合并重复或相似的经验
- 更新过时的最佳实践
- 保持经验的精简性

## 示例：完整经验提取流程

```bash
# 1. 初始化任务
/van Implement user profile editing feature

# 输出：Level 3 任务，包含表单验证、状态管理

# 2. 规划
/plan

# 输出：创建组件、API端点、验证逻辑的详细计划

# 3. 设计
/creative

# 输出：设计表单状态管理方案

# 4. 实现
/build

# 遇到问题：
# - 表单验证逻辑复杂
# - 状态管理导致不必要的重渲染
# - API错误处理不一致

# 5. 反思
/reflect

# 记录经验：
# Technical:
# - 使用 React Hook Form 简化表单验证
# - 使用 useMemo 避免验证函数重新创建
# - 统一 API 错误处理模式

# 6. 归档（自动提取）
/archive

# 系统自动：
# 1. 分析反思文档
# 2. 识别模块：component-design, api-design
# 3. 提取经验：
#    - component-design.md: 表单验证最佳实践
#    - api-design.md: 统一错误处理模式
# 4. 更新索引，添加标签

# 结果：
# memory-bank/lessons/component-design.md (新增经验)
# memory-bank/lessons/api-design.md (新增经验)
# memory-bank/lessons/_index.md (更新索引)
```

## 参考文档

- [LESSONS_DESIGN.md](LESSONS_DESIGN.md) - 详细设计文档
- [lessons-extraction.mdc](.cursor/rules/isolation_rules/Core/lessons-extraction.mdc) - 提取规则
- [archive.md](.cursor/commands/archive.md) - 归档命令文档
- [Memory Bank Optimizations](MEMORY_BANK_OPTIMIZATIONS.md) - 优化概述

## 故障排查

### 经验没有被提取

**可能原因**：
1. 任务复杂度太低（Level 1）
2. 反思文档中没有明确的经验教训
3. 经验不符合提取标准（不可操作/不可复用）

**解决方法**：
- 在反思文档中明确记录"Key Lessons Learned"部分
- 确保经验具有可操作性和可复用性
- 手动创建经验文件（参考模板）

### 模块识别不准确

**可能原因**：
1. 代码路径不标准
2. 关键词不明显

**解决方法**：
- 在反思文档中明确提到模块名称
- 手动调整生成的经验文件到正确模块
- 更新索引文件

### 经验过于冗长

**解决方法**：
- 遵循格式标准（Context ≤ 2句，Best Practice 3-5点）
- 移除不必要的细节
- 聚焦核心要点

## 未来增强

计划中的功能：
1. 专用 `/lessons` 命令用于经验管理
2. AI 辅助语义搜索
3. 经验评分和推荐
4. 跨项目知识共享
5. 可视化经验地图
