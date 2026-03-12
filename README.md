<p align="center">
  <img src="assets/logo.svg" width="180" height="180" alt="InkOS Logo">
</p>

<h1 align="center">InkOS</h1>

<p align="center">
  <strong>AI 小说写作 CLI</strong>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="License: MIT"></a>
  <a href="https://nodejs.org/"><img src="https://img.shields.io/badge/node-%3E%3D20.0.0-brightgreen.svg" alt="Node.js"></a>
  <a href="https://pnpm.io/"><img src="https://img.shields.io/badge/pnpm-%3E%3D9.0.0-orange.svg" alt="pnpm"></a>
  <a href="https://www.typescriptlang.org/"><img src="https://img.shields.io/badge/TypeScript-5.x-3178C6.svg?logo=typescript&logoColor=white" alt="TypeScript"></a>
</p>

<p align="center">
  中文 | <a href="README.en.md">English</a>
</p>

---

用 AI 写长篇小说的命令行工具。五个 agent 接力完成写作、审计、修订，人类在最后审核把关。

## 为什么需要 InkOS？

用 AI 写小说不是简单的"提示词 + 复制粘贴"。长篇小说很快就会崩：角色记忆混乱、物品凭空出现、同样的形容词每段都在重复、伏笔悄无声息地断掉。InkOS 把这些当工程问题来解决。

- **三大真相文件** — 追踪世界的真实状态，而非 LLM 的幻觉
- **反信息泄漏** — 确保角色只知道他们亲眼见证过的事
- **资源衰减** — 物资会消耗、物品会损坏，没有无限背包
- **词汇疲劳检测** — 在读者发现之前就捕捉过度使用的词语
- **修订循环** — 审计不通过就自动改，改完再审，直到没有硬伤

## 工作原理

每一章由五个 agent 接力完成：

<p align="center">
  <img src="assets/screenshot-pipeline.png" width="800" alt="管线流程图">
</p>

| Agent | 职责 |
|-------|------|
| **雷达 Radar** | 扫描平台趋势和读者偏好（可插拔，可跳过） |
| **建筑师 Architect** | 规划章节大纲、场景节拍、节奏 |
| **写手 Writer** | 根据大纲 + 当前世界状态生成正文 |
| **审计员 Auditor** | 对照三大真相文件验证草稿 |
| **修订者 Reviser** | 修复审计发现的问题，关键问题改完再审 |

### 三大真相文件

每本书维护三个文件作为唯一事实来源：

| 文件 | 用途 |
|------|------|
| `current_state.md` | 世界状态：角色位置、关系网络、已知信息、情感弧线 |
| `particle_ledger.md` | 资源账本：物品、金钱、物资数量及衰减追踪 |
| `pending_hooks.md` | 未闭合伏笔：铺垫、对读者的承诺、未解决冲突 |

审计员对照这三个文件检查每一章草稿。角色"记起"了从未亲眼见过的事？捕捉。拿出了两章前丢失的武器？捕捉。

<p align="center">
  <img src="assets/screenshot-state.png" width="800" alt="三大真相文件快照">
</p>

### 内置创作规则

写手 agent 内置 47 条创作规则，覆盖 6 个维度：

- **人物塑造** — 角色行为由"过往经历 + 当前利益 + 性格底色"驱动；配角必须有独立动机
- **叙事技法** — Show don't tell、五感代入法、每章结尾必须设置钩子
- **逻辑自洽** — 三连反问自检、信息越界检查、关系改变必须事件驱动
- **语言约束** — 句式多样化、高疲劳词限制、情绪用细节传达
- **禁忌清单** — 禁止机械降神、反派降智、主角圣母
- **数值验算** — 每次数值变动必须从账本取值验算

每本书还有自己的 `style_guide.md`（文风指南）和 `story_bible.md`（世界观设定），建书时生成，可自行定制。

## 三种用法

### 1. 一键写（完整管线）

```bash
inkos write next 吞天魔帝              # 写 → 审 → 改，一步到位
inkos write next 吞天魔帝 --count 5    # 连写 5 章
```

### 2. 拆开用（原子命令）

```bash
inkos draft 吞天魔帝 --context "本章重点写师徒矛盾" --json
inkos audit 吞天魔帝 31 --json
inkos revise 吞天魔帝 31 --json
```

每个命令只做一件事，`--json` 输出结构化数据。可以用 shell 脚本编排，也可以被 OpenClaw 等 AI agent 通过 `exec` 调用。

### 3. 自然语言（agent 模式）

```bash
inkos agent "帮我写一本都市修仙，主角是个程序员"
inkos agent "写下一章，重点写师徒矛盾"
inkos agent "先看看市场趋势，再决定写什么"
```

内置 9 个工具，LLM 通过 tool-use 决定调用哪些、什么顺序。自己提供题材时会跳过雷达。

## 快速开始

```bash
npm i -g @actalk/inkos

inkos init                                              # 初始化，生成 .env
# 编辑 .env，填入 API Key（支持所有 OpenAI 兼容接口）

inkos book create --title "吞天魔帝" --genre xuanhuan    # 建书
inkos write next 吞天魔帝                                # 写下一章
inkos status                                            # 看状态
inkos review list 吞天魔帝                               # 审阅
inkos export 吞天魔帝                                    # 导出
inkos up                                                # 挂机写
```

<p align="center">
  <img src="assets/screenshot-terminal.png" width="700" alt="终端截图">
</p>

## 命令参考

| 命令 | 说明 |
|------|------|
| `inkos init` | 初始化项目 |
| `inkos book create` | 建书（生成世界观 + 卷纲 + 文风指南） |
| `inkos book list` | 列出所有书 |
| `inkos write next <id>` | 写下一章（完整管线） |
| `inkos write rewrite <id> <n>` | 重写第 N 章（回滚状态快照） |
| `inkos draft <id>` | 只写草稿 |
| `inkos audit <id> [n]` | 审计指定章节 |
| `inkos revise <id> [n]` | 修订指定章节 |
| `inkos agent <instruction>` | 自然语言模式 |
| `inkos review list/approve/reject` | 审阅 |
| `inkos review approve-all <id>` | 批量通过 |
| `inkos status` | 项目状态 |
| `inkos export <id>` | 导出为 txt/md |
| `inkos radar scan` | 扫描平台趋势 |
| `inkos config set/show` | 配置 |
| `inkos doctor` | 诊断 |
| `inkos up / down` | 守护进程 |

所有写入类命令支持 `--context` 传入创作指导，所有命令支持 `--json` 输出。

## 实测数据

用 InkOS 跑了一本玄幻题材的《吞天魔帝》：

<p align="center">
  <img src="assets/screenshot-chapters.png" width="800" alt="生产数据">
</p>

| 指标 | 数据 |
|------|------|
| 已完成章节 | 31 章 |
| 总字数 | 452,191 字 |
| 平均章字数 | ~14,500 字 |
| 审计通过率 | 100% |
| 资源追踪项 | 48 个 |
| 活跃伏笔 | 20 条 |
| 已回收伏笔 | 10 条 |

## 特性

- **状态快照 + 章节重写** — 每章保存快照，`write rewrite` 可以回滚到任意章节重新生成
- **写入锁** — 防止并发写入同一本书
- **写前自检 + 写后结算** — 写手动笔前输出自检表，写完输出结算表，审计员交叉验证
- **可插拔雷达** — `RadarSource` 接口，内置番茄/起点，可传自定义数据源或跳过
- **守护进程** — `inkos up` 挂机按计划写，审计不过自动改，改不了推通知等人
- **通知推送** — Telegram、飞书、企业微信
- **外部 Agent 集成** — 原子命令 + `--json`，可被 OpenClaw 等 AI agent 调用

## 项目结构

```
inkos/
├── packages/
│   ├── core/              # agent、管线、状态管理
│   │   ├── agents/        # architect, writer, continuity, reviser, radar
│   │   ├── pipeline/      # runner, agent (tool-use), scheduler
│   │   ├── state/         # 文件状态管理
│   │   ├── llm/           # OpenAI 兼容接口
│   │   ├── notify/        # Telegram, 飞书, 企微
│   │   └── models/        # Zod schema
│   └── cli/               # Commander.js (15 条命令)
└── (规划中) studio/        # Web 审阅界面
```

## 路线图

- [x] 写作管线（雷达 → 建筑师 → 写手 → 审计 → 修订）
- [x] 三大真相文件 + 连续性审计
- [x] 内置创作规则（47 条）
- [x] CLI 15 条命令
- [x] 状态快照 + 章节重写
- [x] 守护进程
- [x] 通知推送
- [x] 原子命令 + JSON 输出
- [x] 自然语言 agent 模式
- [x] 可插拔雷达
- [x] 外部 Agent 集成
- [ ] Web UI 审阅界面
- [ ] 多模型路由
- [ ] 自定义 agent 插件
- [ ] 平台格式导出

## 参与贡献

```bash
pnpm install
pnpm dev          # 监听模式
pnpm test         # 运行测试
pnpm typecheck    # 类型检查
```

## 许可证

[MIT](LICENSE)
