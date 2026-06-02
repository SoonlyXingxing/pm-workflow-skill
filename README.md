# PM Workflow Skill

AI 产品经理工作流 skill，为 AI 编码助手（Claude Code / OpenAI Codex）提供结构化的产品设计决策记录、校准和 PRD 生成能力。

## 解决什么问题

在使用 AI 编码助手进行产品讨论时，设计决策容易散落在对话中、跨会话丢失。本 skill 提供：

1. **Note（记录）**：每轮产品讨论后，结构化记录设计决策到项目本地文件
2. **Digest（校准）**：定期整理积累的记录，去重、解决冲突、清除过时条目
3. **PRD（生成需求文档）**：基于积累的设计决策，自动生成结构化 PRD

所有记录隔离在项目本地的 `.claude/observations.md`，不会污染其他项目。

## 安装

### Claude Code

将本目录复制到 Claude Code 的 skills 目录：

```bash
# 全局安装（所有项目可用）
cp -r pm-workflow-skill ~/.claude/skills/pm-workflow

# 或项目级安装（仅当前项目可用）
cp -r pm-workflow-skill .claude/skills/pm-workflow
```

### OpenAI Codex CLI

```bash
cp -r pm-workflow-skill ~/.codex/skills/pm-workflow
```

## 使用方法

### 记录设计决策（Note）

在产品讨论结束后，直接告诉 AI：

```
记录这轮讨论
记一下
/note
```

AI 会自动提取本轮对话中的设计决策、交互细节、技术方案等，追加到 `.claude/observations.md`。

### 校准记录（Digest）

当积累了多轮记录后，整理和去重：

```
校准 observations
整理产品决策记录
/digest
```

AI 会展示分组整理结果，标注冲突/过时/重复条目，等你确认后再修改文件。

### 生成 PRD

基于积累的设计决策生成产品需求文档：

```
生成 PRD
写需求文档
/pm [功能名称]
```

AI 会先询问是否需要记录当前轮和校准，然后基于所有已确认记录起草 PRD。

## 自动行为

- 每次产品讨论后，AI 会主动询问是否记录
- 每记录 3 轮后，AI 会主动询问是否校准
- 生成 PRD 前，AI 会执行 preflight（可跳过）
- Digest 永远不会静默改写文件，必须经你确认

## 目录结构

```
pm-workflow-skill/
├── SKILL.md                          # 主 skill 定义和工作流路由
├── references/
│   ├── note.md                       # Note workflow 详细流程
│   ├── digest.md                     # Digest workflow 详细流程
│   ├── pm.md                         # PRD 生成规则和文档模板
│   └── observations-template.md      # 新项目 observations 文件模板
└── agents/
    └── openai.yaml                   # OpenAI Codex CLI 兼容配置
```

## PRD 输出特点

- 不包含评估方案章节
- 不包含"本次 PRD 说明"元信息
- 只写产品能力和用户体验，不把接口名称、字段名写成产品需求
- 只有涉及 API/模型调用的步骤才给出技术示例
- 信息来源轻量标注：无标注=已确认记录，💡=推断/建议，❓=待补充

## 适用场景

- 产品经理与 AI 搭档进行需求讨论
- 独立开发者边开发边梳理产品设计
- 团队使用 AI 辅助产品文档编写
- 任何需要跨会话保持产品设计上下文的场景

## License

MIT
