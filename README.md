# Learn Open Source

将任意开源项目拆解为结构化 Markdown 课程，帮助开发者系统理解源码。

## 文件结构

```
├── SKILL.md                          # 核心工作流 + Reference Map
└── references/
    ├── language-analysis-guides.md   # 6 语言分析策略
    ├── architecture-patterns.md      # 6 种架构模式 + Mermaid 模板
    ├── course-templates.md           # 课程 Markdown 模板
    ├── mermaid-diagram-guide.md      # 5 种 Mermaid 图类型指南
    └── quality-standards.md          # 质量标准 + 阶段自检清单
```

## Reference 概览

| 文件 | 内容 | 加载时机 |
|------|------|----------|
| `language-analysis-guides.md` | Go / Rust / Python / TypeScript / Java / C/C++ 的分析切入点 | 阶段一 — 识别语言后 |
| `architecture-patterns.md` | Web / CLI / SDK / 数据库 / 事件驱动 / Pipeline 六种架构的 Mermaid 模板 | 阶段二 — 画架构图时 |
| `course-templates.md` | 课程大纲、标准章节、文件详解的统一模板 | 阶段五 — 生成文件时 |
| `mermaid-diagram-guide.md` | graph TD / sequenceDiagram / classDiagram / flowchart / stateDiagram 选择指南 | 阶段二/四 — 画图时 |
| `quality-standards.md` | "三层理解"深度标准、6 种反模式、每阶段自检清单 | 全部阶段 |

## 五阶段工作流

1. **项目发现** → 克隆、识别技术栈、定位入口
2. **架构追踪** → 从入口逐层追到出口，画出完整 Mermaid 调用链
3. **课程规划** → 按调用链路顺序拆分章节（每章 3-8 文件），用户确认后继续
4. **逐章深挖** → 每个文件的每个关键方法：签名 + 目的 + 核心逻辑 + 调用关系
5. **生成课程** → 输出到 `<目标项目>/Tutorial/` 目录

## 核心原则

- **入口到出口** — 不画架构图不开始写课
- **解释思路，不翻译代码** — 讲"为什么"比"是什么"重要
- **不跳过任何文件** — 配置和工具文件也不能省略
- **Mermaid 强制** — 总架构图 + 每课局部调用图

## 使用

触发词涵盖：学习源码、分析项目、拆解仓库、深入理解框架内部实现

```
帮我学习 https://github.com/gin-gonic/gin
分析一下 ~/projects/kubernetes 的源码
深入理解 axios 的内部实现
```

## 支持

- 任意 GitHub URL 或本地路径
- 任意编程语言（Go / Rust / Python / TypeScript / Java / C/C++ 等）
- 任意项目类型（CLI / Web / 库 / SDK / 数据库 / 事件驱动 / Pipeline）
