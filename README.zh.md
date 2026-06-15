# 受 Karpathy 启发的 Claude Code 指南

> 查看我的新项目 [Multica](https://github.com/multica-ai/multica) —— 一个用于运行和管理编码智能体的开源平台，支持可复用的技能。
>
> 在 X 上关注我：[https://x.com/jiayuan_jy](https://x.com/jiayuan_jy)

一个单一的 `CLAUDE.md` 文件，用于改善 Claude Code 的行为，源自 [Andrej Karpathy 的观察](https://x.com/karpathy/status/2015883857489522876) 关于 LLM 编码陷阱的总结。

[English](./README.md) | 简体中文

## 问题所在

来自 Andrej 的推文：

> "模型会代你做错误假设，然后不假思索地执行。它们不管理自身的困惑，不寻求澄清，不呈现矛盾，不展示权衡，在应该提出异议时也不反驳。"

> "它们真的很喜欢把代码和 API 搞复杂，堆砌抽象概念，不清理死代码……明明 100 行能搞定的事情，非要实现成 1000 行的臃肿架构。"

> "它们有时仍会改动或删除自己理解不足的代码和注释，即使这些内容与任务本身无关。"

## 解决方案

四个原则，集中在一个文件中，直接解决这些问题：

| 原则 | 解决什么问题 |
|-----------|-----------|
| **编码前思考** | 错误假设、隐藏困惑、缺少权衡 |
| **简洁优先** | 过度复杂、臃肿抽象 |
| **精准修改** | 无关编辑、触碰不应碰的代码 |
| **目标驱动执行** | 通过测试优先、可验证的成功标准 |

## 四个原则详解

### 1. 编码前思考

**不要假设。不要隐藏困惑。呈现权衡。**

LLM 经常默默选择一种解释然后执行。这个原则强制明确推理：

- **明确说明假设** — 如果不确定，询问而不是猜测
- **呈现多种解释** — 当存在歧义时，不要默默选择
- **适时提出异议** — 如果存在更简单的方法，说出来
- **困惑时停下来** — 指出不清楚的地方并要求澄清

### 2. 简洁优先

**用最少的代码解决问题。不要过度推测。**

对抗过度工程的倾向：

- 不要添加要求之外的功能
- 不要为一次性代码创建抽象
- 不要添加未要求的"灵活性"或"可配置性"
- 不要为不可能发生的场景做错误处理
- 如果 200 行代码可以写成 50 行，重写它

**检验标准：** 资深工程师会觉得这过于复杂吗？如果是，简化。

### 3. 精准修改

**只碰必须碰的。只清理自己造成的混乱。**

编辑现有代码时：

- 不要"改进"相邻的代码、注释或格式
- 不要重构没坏的东西
- 匹配现有风格，即使你更倾向于不同的写法
- 如果注意到无关的死代码，提一下 —— 不要删除它

当你的改动产生孤儿代码时：

- 删除因你的改动而变得无用的导入/变量/函数
- 不要删除预先存在的死代码，除非被要求

**检验标准：** 每一行修改都应该能直接追溯到用户的请求。

### 4. 目标驱动执行

**定义成功标准。循环验证直到达成。**

将指令式任务转化为可验证的目标：

| 不要这样做... | 转化为... |
|--------------|-----------------|
| "添加验证" | "为无效输入编写测试，然后让它们通过" |
| "修复 bug" | "编写重现 bug 的测试，然后让它通过" |
| "重构 X" | "确保重构前后测试都能通过" |

对于多步骤任务，说明一个简短的计划：

```
1. [步骤] → 验证: [检查]
2. [步骤] → 验证: [检查]
3. [步骤] → 验证: [检查]
```

强有力的成功标准让 LLM 能够独立循环执行。弱标准（"让它工作"）需要不断澄清。

## 安装

### 用于 Claude Code

**选项 A：Claude Code 插件（推荐）**

在 Claude Code 中，首先添加插件市场：
```
/plugin marketplace add forrestchang/andrej-karpathy-skills
```

然后安装插件：
```
/plugin install andrej-karpathy-skills@karpathy-skills
```

这会将指南安装为 Claude Code 插件，使其在你所有项目中可用。

**选项 B：CLAUDE.md（按项目）**

新项目：
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

已有项目（追加）：
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

### 用于 Cursor

本仓库包含一个已提交的 Cursor 项目规则 ([`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc))，因此在 Cursor 中打开项目时同样适用这些指南。

在其他项目中使用：
```bash
mkdir -p .cursor/rules
curl -o .cursor/rules/karpathy-guidelines.mdc https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/.cursor/rules/karpathy-guidelines.mdc
```

### 用于 Codex / GitHub Copilot

**新增：** 使用 [`CODEX.md`](./CODEX.md) 获得 OpenAI Codex 和 GitHub Copilot 优化后的指南！

#### 选项 A：下载 CODEX.md

```bash
# 下载到你的项目根目录
curl -o CODEX.md https://raw.githubusercontent.com/githunan/andrej-karpathy-skills/add-codex-guidelines/CODEX.md
```

#### 选项 B：GitHub Copilot 指令文件（VS Code）

```bash
# 创建 .github 目录（如果不存在）
mkdir -p .github

# 将 Codex 指南下载为 Copilot 指令文件
curl https://raw.githubusercontent.com/githunan/andrej-karpathy-skills/add-codex-guidelines/CODEX.md \
  -o .github/copilot-instructions.md
```

GitHub Copilot 会自动读取 `.github/copilot-instructions.md` 文件。

#### 选项 C：作为参考使用

在使用 Codex CLI 或 API 时作为参考：
```bash
# 在编码时参考这些原则
cat CODEX.md
```

#### 选项 D：提交到 Git

在团队项目中共享：
```bash
git add CODEX.md
git commit -m "Add Codex guidelines"
git push
```

### 使用场景

| 使用场景 | 推荐设置 |
|---------|--------|
| **VS Code 中的 GitHub Copilot** | 复制到 `.github/copilot-instructions.md` |
| **OpenAI Codex CLI** | 复制到项目根目录作为 `CODEX.md` |
| **编码时的参考** | 在编码时保持打开 |
| **团队指南** | 提交到 Git，与团队共享 |

### CLAUDE.md 与 CODEX.md 的区别

| 方面 | CLAUDE.md | CODEX.md |
|-----|-----------|---------|
| **工具** | Claude Code IDE | Codex API / GitHub Copilot |
| **交互模式** | 多轮对话 | 单轮代码补全 |
| **上下文** | 大窗口，可以推理 | 有限，需要紧凑的上下文 |
| **主要风险** | 过度讨论 | 过度抽象、产生幻觉 |
| **测试策略** | 讨论测试方案 | 必须立即测试 |
| **使用方式** | IDE 插件或 `.claude` | `.github/copilot-instructions.md` 或项目根目录 |

### 安装速查表

```bash
# Clone 或下载此仓库
git clone https://github.com/githunan/andrej-karpathy-skills.git
cd andrej-karpathy-skills

# 对于你的项目中的 Claude Code
cp CLAUDE.md /你的/项目/路径/

# 对于 Cursor IDE
mkdir -p /你的/项目/路径/.cursor/rules
cp .cursor/rules/karpathy-guidelines.mdc /你的/项目/路径/.cursor/rules/

# 对于 GitHub Copilot
mkdir -p /你的/项目/路径/.github
cp CODEX.md /你的/项目/路径/.github/copilot-instructions.md

# 对于独立的 Codex 参考
cp CODEX.md /你的/项目/路径/
```

## 核心洞察

来自 Andrej：

> "LLM 非常擅长循环执行直到达成特定目标……不要告诉它该做什么，给它成功标准，然后看着它完成。"

"目标驱动执行"原则正是捕捉了这一点：将指令式指令转化为带有验证循环的声明式目标。

## 如何判断它在起作用

如果你看到以下情况，说明这些指南正在发挥作用：

- **diff 中不必要的改动更少** —— 只有请求的改动出现
- **因过度复杂而导致的重写更少** —— 代码第一次就写得简洁
- **澄清问题在实现之前提出** —— 而不是在犯错之后
- **干净、精简的 PR** —— 没有顺带的重构或"改进"

**对于 Codex/Copilot 特别地：**
- **过度抽象的补全更少** —— Codex 尊重你的简洁意图
- **产生幻觉更少** —— 更好的测试实践能捕捉到不存在的函数
- **代码集成更顺畅** —— 补全能匹配你文件中的现有模式

## 定制

这些指南设计用于与项目特定指令合并。将它们添加到你现有的指令文件或创建一个新的。

对于项目特定规则，添加如下章节：

```markdown
## 项目特定指南

- 使用 TypeScript 严格模式
- 所有 API 端点必须有测试
- 遵循 `src/utils/errors.ts` 中现有的错误处理模式
```

## 权衡说明

这些指南倾向于**谨慎而非速度**。对于琐碎的任务（简单的拼写错误修复、显而易见的一行修改），请自行判断 —— 并非每个改动都需要完整的严格性。

目标是减少非琐碎工作中的代价高昂的错误，而不是拖慢简单任务。

## 仓库中的文件

- **[CLAUDE.md](./CLAUDE.md)** - Claude Code 指南
- **[CODEX.md](./CODEX.md)** - OpenAI Codex 和 GitHub Copilot 指南（新增）
- **[CURSOR.md](./CURSOR.md)** - Cursor IDE 设置说明
- **[EXAMPLES.md](./EXAMPLES.md)** - 四个原则的详细示例（编码前/编码后对比）
- **[.cursor/rules/karpathy-guidelines.mdc](.cursor/rules/karpathy-guidelines.mdc)** - Cursor IDE 规则文件

## 许可

MIT
