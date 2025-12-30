# 周报生成功能设计方案

## 一、功能概述

### 1.1 目标
自动从 Memory Bank 中提取一周内的工作数据，生成结构化的周报文档，用于：
- 个人工作回顾
- 团队进展同步
- 管理层汇报
- 项目历史记录

### 1.2 核心价值
- ✅ **零手工整理**：自动从现有数据生成，无需手动记录
- ✅ **数据驱动**：基于实际完成的任务，真实可靠
- ✅ **多维度总结**：任务、经验、问题、计划等全面覆盖
- ✅ **时间可配置**：支持周报、双周报、月报等

## 二、数据来源

### 2.1 主要数据源

```
memory-bank/
├── archive/                    # 已完成任务的完整归档
│   ├── archive-task-001.md    # 包含：元数据、实现细节、测试、经验
│   └── archive-task-002.md
├── reflection/                 # 任务反思文档
│   ├── reflection-task-001.md # 包含：What Went Well, Challenges, Lessons
│   └── reflection-task-002.md
├── progress.md                 # 实施进度和观察
├── tasks.md                    # 当前任务状态（进行中）
└── lessons/                    # 新增的经验知识
    ├── _index.md
    └── [module].md
```

### 2.2 时间范围识别

**方法1：基于文件修改时间**
```bash
# 获取最近7天修改的归档文件
find memory-bank/archive/ -name "*.md" -mtime -7
```

**方法2：基于归档文档中的元数据**
```markdown
## METADATA
- Completion Date: 2024-01-15  # 解析这个日期
```

**方法3：基于文件名模式**（如果包含日期）
```
archive-2024-01-15-task-name.md
```

### 2.3 提取的信息

从每个归档任务中提取：
- **任务名称和ID**
- **完成日期**
- **复杂度级别** (Level 1-4)
- **简要描述**（Summary）
- **关键成果**（Key Changes）
- **遇到的挑战**（Challenges）
- **经验教训**（Lessons Learned）
- **引用的经验**（Applied Lessons）

## 三、周报结构

### 3.1 周报模板

```markdown
# 工作周报 - Week [N], [Year]

**报告周期**: YYYY-MM-DD 至 YYYY-MM-DD
**生成时间**: YYYY-MM-DD HH:MM
**项目**: [Project Name]

---

## 📊 本周概览

- **完成任务**: X 个
- **总工作量**: Level 1: X个 | Level 2: X个 | Level 3: X个 | Level 4: X个
- **新增经验**: X 条（涉及 Y 个模块）
- **应用经验**: X 次

---

## ✅ 完成任务清单

### 1. [Task Name] (Level X)
**完成日期**: YYYY-MM-DD
**简要说明**: 一句话描述任务内容和成果

**关键成果**:
- 成果点1
- 成果点2

**相关文档**: [`archive-task-001.md`](memory-bank/archive/archive-task-001.md)

### 2. [Another Task] (Level X)
...

---

## 💡 经验与洞察

### 技术经验
本周在以下模块积累了新经验：

#### Authentication (2 lessons)
- **L005: OAuth Token Refresh Pattern**
  简要：实现了更安全的token轮换机制
  影响：提升了API安全性

#### Database (1 lesson)
- **L008: Query Optimization for Large Tables**
  简要：通过索引优化将查询时间从2s降至200ms
  影响：显著改善用户体验

### 流程改进
- 改进点1：在规划阶段引入经验推荐，减少了重复错误
- 改进点2：...

---

## 🎯 关键成果

### 功能交付
- ✅ 用户认证系统完全重构，安全性提升30%
- ✅ 数据库查询性能优化，响应时间降低80%
- ✅ 实现了XX新功能，支持YY用户场景

### 技术提升
- 掌握了 OAuth2.0 的最佳实践
- 深入理解了数据库索引策略
- 学会了大规模数据迁移的零宕机方法

---

## 🚧 遇到的挑战

### 技术挑战
1. **JWT Token 过期处理**（已解决）
   - 问题：用户频繁掉线影响体验
   - 解决：实现refresh token机制和静默刷新
   - 经验：lessons/authentication.md#L001

2. **大表迁移锁表问题**（已解决）
   - 问题：ALTER TABLE锁表导致服务中断
   - 解决：使用pt-online-schema-change工具
   - 经验：lessons/database.md#L006

### 流程挑战
1. **估算不准确**
   - OAuth集成比预期复杂2倍
   - 改进：下次类似任务留出缓冲时间

---

## 📈 应用经验统计

本周成功应用了以下历史经验：

| 经验 | 应用次数 | 有效性 |
|------|---------|--------|
| authentication.md#L001 - JWT管理 | 2次 | 100% 成功 |
| database.md#L003 - 索引优化 | 1次 | 100% 成功 |
| api-design.md#L007 - 错误处理 | 1次 | 部分有效 |

**收获**：经验库在实际工作中发挥了重要作用，减少了约40%的调试时间。

---

## 📝 进行中的工作

### 本周未完成（计划下周继续）
1. **[Task Name]** - Level 3
   - 当前状态：BUILD 阶段，完成度 60%
   - 阻塞点：等待第三方API文档更新
   - 预计完成：下周三

2. **[Another Task]** - Level 2
   - 当前状态：PLAN 阶段
   - 预计完成：下周五

---

## 🎯 下周计划

### 主要任务
1. 完成用户通知系统（Level 3）
2. 实现数据导出功能（Level 2）
3. 修复已知的5个bug（Level 1）

### 预期成果
- 交付用户通知系统，支持邮件和推送
- 完成数据导出到CSV/Excel功能
- 解决所有P1级别的bug

### 技术重点
- 学习消息队列最佳实践
- 研究大文件导出的性能优化

---

## 📚 知识积累

### 新增文档
- `archive-task-001.md` - 用户认证重构完整记录
- `lessons/authentication.md#L005` - OAuth token refresh pattern
- `lessons/database.md#L008` - Query optimization techniques

### 经验库增长
- 总经验数：45 → 48（+3）
- 活跃模块：authentication, database, api-design
- 本周最有价值经验：authentication.md#L005（被推荐3次）

---

## 💭 反思与改进

### 做得好的地方
- ✅ 在规划阶段就查阅了相关经验，避免了重复错误
- ✅ 及时记录遇到的问题和解决方案，形成了3条新经验
- ✅ 任务分解合理，各阶段进展顺利

### 需要改进的地方
- ⚠️ OAuth集成低估了复杂度，导致延期1天
- ⚠️ 测试用例覆盖不够全面，上线后发现1个edge case
- ⚠️ 文档更新不及时，团队成员询问较多

### 下周改进措施
1. 对于不熟悉的技术，规划时留出20%缓冲时间
2. 代码审查阶段重点检查边界条件测试
3. 每完成一个功能立即更新README

---

## 📊 统计数据

### 任务分布
- Level 1 (Bug修复): 2个 (20%)
- Level 2 (简单功能): 3个 (30%)
- Level 3 (中等功能): 4个 (40%)
- Level 4 (复杂系统): 1个 (10%)

### 时间分布
- 规划(PLAN): 15%
- 设计(CREATIVE): 20%
- 实施(BUILD): 50%
- 测试和反思: 15%

### 经验应用
- 主动推荐：5次
- 实际应用：4次
- 应用成功率：87.5%

---

## 🔗 相关链接

- [归档任务列表](memory-bank/archive/)
- [反思文档](memory-bank/reflection/)
- [经验知识库](memory-bank/lessons/)
- [进度追踪](memory-bank/progress.md)

---

*本周报由 Memory Bank 系统自动生成于 YYYY-MM-DD HH:MM*
*数据来源: memory-bank/archive/, memory-bank/reflection/, memory-bank/lessons/*
```

### 3.2 周报变体

**精简版**（适合快速回顾）：
- 只包含：概览、任务清单、关键成果、下周计划
- 去除详细的经验、挑战、统计等

**详细版**（适合深度总结）：
- 完整版所有内容
- 额外包含每个任务的详细技术方案
- 代码变更统计（行数、文件数）
- 性能指标对比（如果有）

**管理层版**（适合向上汇报）：
- 聚焦业务价值和成果
- 风险和阻塞点突出
- 数据可视化（如果可能）
- 去除技术细节

## 四、实现方案

### 4.1 命令设计

**新增命令**: `/report` 或 `/weekly`

```bash
# 基本用法：生成本周周报
/report

# 指定时间范围
/report --weeks 2          # 生成最近2周的周报
/report --start 2024-01-01 --end 2024-01-07  # 指定日期范围

# 指定格式
/report --format full      # 完整版（默认）
/report --format brief     # 精简版
/report --format executive # 管理层版

# 输出位置
/report --output weekly-reports/  # 指定输出目录（默认：memory-bank/reports/）
```

### 4.2 文件结构

```
memory-bank/
└── reports/
    ├── _index.md                      # 报告索引
    ├── weekly/
    │   ├── 2024-W03.md               # Week 3 of 2024
    │   ├── 2024-W04.md
    │   └── 2024-W05.md
    ├── monthly/                       # 月报（可选）
    │   ├── 2024-01.md
    │   └── 2024-02.md
    └── _templates/
        ├── weekly-report-template.md
        └── monthly-report-template.md
```

### 4.3 数据提取逻辑

```javascript
// 伪代码示例
async function generateWeeklyReport(startDate, endDate) {
  // 1. 收集归档任务
  const archivedTasks = await findArchivedTasks(startDate, endDate);

  // 2. 分析任务
  const analysis = {
    totalTasks: archivedTasks.length,
    byLevel: groupByLevel(archivedTasks),
    keyAchievements: extractKeyAchievements(archivedTasks),
    challenges: extractChallenges(archivedTasks),
  };

  // 3. 收集新增经验
  const newLessons = await findNewLessons(startDate, endDate);

  // 4. 统计经验应用
  const lessonUsage = await analyzeLessonUsage(archivedTasks);

  // 5. 读取进行中的任务
  const ongoingTasks = await readOngoingTasks();

  // 6. 生成报告
  const report = await renderTemplate({
    dateRange: { start: startDate, end: endDate },
    analysis,
    tasks: archivedTasks,
    lessons: newLessons,
    lessonUsage,
    ongoing: ongoingTasks,
  });

  return report;
}
```

### 4.4 关键算法

#### 时间范围计算
```javascript
function getWeekDateRange(weeksAgo = 0) {
  const today = new Date();
  const dayOfWeek = today.getDay(); // 0 = Sunday
  const monday = new Date(today);
  monday.setDate(today.getDate() - dayOfWeek + 1 - (weeksAgo * 7));
  monday.setHours(0, 0, 0, 0);

  const sunday = new Date(monday);
  sunday.setDate(monday.getDate() + 6);
  sunday.setHours(23, 59, 59, 999);

  return { start: monday, end: sunday };
}
```

#### 任务过滤
```javascript
function isTaskInDateRange(archiveFile, startDate, endDate) {
  // 方法1: 读取文件元数据中的完成日期
  const metadata = parseArchiveMetadata(archiveFile);
  const completionDate = new Date(metadata.completionDate);

  // 方法2: 使用文件修改时间作为fallback
  const fileModTime = fs.statSync(archiveFile).mtime;

  const taskDate = completionDate || fileModTime;
  return taskDate >= startDate && taskDate <= endDate;
}
```

#### 经验提取
```javascript
function extractNewLessons(startDate, endDate) {
  const lessons = [];

  // 读取所有经验文件
  const lessonFiles = glob('memory-bank/lessons/**/*.md');

  for (const file of lessonFiles) {
    const content = fs.readFileSync(file, 'utf-8');
    const lessonsInFile = parseMarkdownLessons(content);

    for (const lesson of lessonsInFile) {
      // 检查创建日期
      if (lesson.effectiveness?.created) {
        const created = new Date(lesson.effectiveness.created);
        if (created >= startDate && created <= endDate) {
          lessons.push({
            module: getModuleName(file),
            id: lesson.id,
            title: lesson.title,
            created: created,
          });
        }
      }
    }
  }

  return lessons;
}
```

### 4.5 Cursor 命令集成

**新文件**: `.cursor/commands/report.md`

```markdown
# REPORT Command - Weekly/Monthly Report Generation

This command generates structured reports from Memory Bank data.

## Memory Bank Integration

Reads from:
- `memory-bank/archive/archive-*.md` - Completed tasks
- `memory-bank/reflection/reflection-*.md` - Task reflections
- `memory-bank/lessons/` - Lessons knowledge base
- `memory-bank/tasks.md` - Ongoing tasks
- `memory-bank/progress.md` - Progress tracking

Creates:
- `memory-bank/reports/weekly/YYYY-WNN.md` - Weekly report
- `memory-bank/reports/_index.md` - Report index

## Progressive Rule Loading

### Step 1: Load Core Rules
```
Load: .cursor/rules/isolation_rules/main.mdc
Load: .cursor/rules/isolation_rules/Core/memory-bank-paths.mdc
```

### Step 2: Load Report Generation Rules
```
Load: .cursor/rules/isolation_rules/Core/report-generation.mdc
```

## Workflow

1. **Determine Date Range**
   - Default: Current week (Monday to Sunday)
   - Parse command parameters for custom range
   - Calculate week number (ISO 8601)

2. **Collect Archived Tasks**
   - Scan `memory-bank/archive/` directory
   - Filter tasks by completion date
   - Parse metadata, summary, lessons learned

3. **Analyze Task Data**
   - Group by complexity level
   - Extract key achievements
   - Identify challenges and solutions
   - Count tasks by type

4. **Collect New Lessons**
   - Scan `memory-bank/lessons/` for new entries
   - Filter by creation date within range
   - Group by module

5. **Analyze Lesson Usage**
   - Find tasks that referenced lessons
   - Calculate application success rate
   - Identify most valuable lessons

6. **Read Ongoing Tasks**
   - Parse `memory-bank/tasks.md`
   - Identify in-progress tasks
   - Estimate completion status

7. **Generate Report**
   - Render template with collected data
   - Format markdown sections
   - Add statistics and charts (text-based)
   - Include links to source documents

8. **Save Report**
   - Create report file: `reports/weekly/YYYY-WNN.md`
   - Update `reports/_index.md`
   - Display summary to user

## Usage

```bash
# Generate report for current week
/report

# Generate report for last 2 weeks
/report --weeks 2

# Generate brief format
/report --format brief

# Custom date range
/report --start 2024-01-01 --end 2024-01-07
```

## Output Format

Reports are saved in ISO week format: `YYYY-WNN.md`
- 2024-W03.md = Week 3 of 2024
- 2024-W04.md = Week 4 of 2024

## Next Steps

After generating report:
- Review the report for completeness
- Share with team if needed
- Use insights for planning next week (/van)
```

**新文件**: `.cursor/rules/isolation_rules/Core/report-generation.mdc`

详细的报告生成规则，包括：
- 数据提取规则
- 模板渲染规则
- 统计计算规则
- 格式化规则

## 五、实现优先级

### 5.1 MVP功能（第一版）

**必须有**：
- ✅ 基本周报生成（当周）
- ✅ 任务清单提取
- ✅ 关键成果总结
- ✅ 新增经验列表
- ✅ 下周计划提示

**可以省略**：
- ❌ 自定义日期范围（固定当周）
- ❌ 多种格式（只有完整版）
- ❌ 详细统计图表
- ❌ 月报功能

### 5.2 第二版增强

- 自定义日期范围
- 多种报告格式（精简、详细、管理层）
- 经验应用统计
- 时间分布分析

### 5.3 第三版高级功能

- 月报、季报生成
- 趋势分析（多周对比）
- 导出为PDF/HTML
- 自动发送邮件
- 数据可视化（图表）

## 六、技术考虑

### 6.1 解析挑战

**问题**：归档文档格式可能不完全一致

**解决**：
- 使用宽松的Markdown解析
- 提取关键section（## METADATA, ## SUMMARY等）
- 容错处理，缺失数据用默认值

### 6.2 日期识别

**优先级**：
1. 文档中的 `Completion Date` 元数据
2. 文档中的 `Date` 字段
3. 文件修改时间（mtime）
4. 文件名中的日期（如果有）

### 6.3 性能优化

**策略**：
- 只读取日期范围内的文件（避免全量扫描）
- 缓存解析结果（同一周期多次生成）
- 增量更新索引

## 七、用户体验

### 7.1 生成时输出

```
📊 Generating Weekly Report for Week 5, 2024...

✓ Scanning archived tasks... found 8 tasks
✓ Analyzing task data...
  - Level 1: 2 tasks
  - Level 2: 3 tasks
  - Level 3: 2 tasks
  - Level 4: 1 task
✓ Extracting new lessons... found 3 lessons
✓ Analyzing lesson usage... 5 applications
✓ Checking ongoing tasks... 2 in progress

📝 Report generated: memory-bank/reports/weekly/2024-W05.md

📊 Summary:
- Completed: 8 tasks
- New lessons: 3 (authentication: 2, database: 1)
- Most applied lesson: authentication.md#L001 (applied 3 times)
- Key achievement: User authentication system refactored

💡 Tip: Review the report before sharing with your team!
```

### 7.2 报告索引

`memory-bank/reports/_index.md`:
```markdown
# Work Reports Index

## Weekly Reports

### 2024

- [Week 05](weekly/2024-W05.md) - 8 tasks, 3 new lessons
- [Week 04](weekly/2024-W04.md) - 6 tasks, 2 new lessons
- [Week 03](weekly/2024-W03.md) - 5 tasks, 1 new lesson

## Statistics

- Total weeks tracked: 3
- Average tasks per week: 6.3
- Most productive week: Week 05 (8 tasks)
- Total lessons accumulated: 6

## Trends

- Task completion trend: ↗️ increasing
- Lesson creation rate: stable
- Most active modules: authentication, database
```

## 八、与现有系统集成

### 8.1 与 Archive 的关系

周报从归档数据中提取，不修改原始数据：
- 读取 `archive/*.md`
- 不修改任何归档文件
- 生成独立的报告文件

### 8.2 与 Lessons 的关系

周报展示经验库的增长：
- 统计新增经验数量
- 展示经验应用情况
- 链接到具体经验文档

### 8.3 工作流集成

```
常规工作流:
/van → /plan → /creative → /build → /reflect → /archive

周报生成:
周五执行: /report
查看: cat memory-bank/reports/weekly/2024-W05.md
```

## 九、示例场景

### 场景1：个人周回顾

```bash
# 周五下午，生成本周报告
/report

# 查看报告
cat memory-bank/reports/weekly/2024-W05.md

# 发现本周完成了8个任务，积累了3条新经验
# 主要成就：重构了用户认证系统
# 下周重点：实现通知系统
```

### 场景2：团队周会

```bash
# 周一上午，准备团队周会材料
/report --format executive

# 生成管理层版本，突出业务价值
# 分享链接给团队成员查看
```

### 场景3：月度总结

```bash
# 月底，生成整月报告
/report --weeks 4

# 或使用月报命令（未来功能）
/report --monthly
```

## 十、未来扩展

### 10.1 自动化

- 定时任务：每周五自动生成周报
- Git hook：归档任务时触发报告更新
- CI/CD集成：自动发布到Wiki或文档系统

### 10.2 协作功能

- 多人项目：合并团队成员的周报
- 模板定制：企业级周报模板
- 审批流程：报告需要审核通过

### 10.3 数据分析

- 生产力趋势：任务完成率变化
- 经验ROI：经验应用带来的时间节省
- 瓶颈识别：哪些环节最耗时

## 十一、实现计划

### Phase 1: 基础实现（1-2天）
- [ ] 创建 report.md 命令文件
- [ ] 创建 report-generation.mdc 规则文件
- [ ] 实现基本的数据提取逻辑
- [ ] 生成简单的周报（任务清单）
- [ ] 更新 memory-bank-paths.mdc

### Phase 2: 完整功能（2-3天）
- [ ] 添加所有报告sections
- [ ] 实现经验统计
- [ ] 添加进行中任务
- [ ] 生成统计数据
- [ ] 创建报告索引

### Phase 3: 优化和文档（1-2天）
- [ ] 性能优化
- [ ] 错误处理
- [ ] 用户文档
- [ ] 示例报告
- [ ] 更新 README

---

## 总结

这个周报生成功能将：
- ✅ **零额外负担**：从现有数据自动生成，无需手动记录
- ✅ **真实可靠**：基于实际完成的任务和反思
- ✅ **多维度价值**：个人回顾、团队协作、管理汇报
- ✅ **与系统集成**：充分利用Memory Bank的所有数据

通过这个功能，开发者可以轻松：
1. 回顾一周的工作成果
2. 总结经验教训
3. 规划下周重点
4. 向团队和管理层展示进展

这是Memory Bank系统的重要补充，将"任务追踪"升级为"工作总结"。
