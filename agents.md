# 项目说明：skill-notify-on-handoff

> 本文件汇总 `skill-notify-on-handoff` 项目的内容、结构与使用方式，供后续维护与复用参考。

---

## 一、项目概述

- **仓库**：`jcleng/skill-notify-on-handoff`（GitHub 公开仓库）
- **用途**：当用户需要接管时（用户要求通知、需要手动确认、任务完成、任务阻断/卡住），通过钉钉机器人发送通知告知用户。
- **规范**：遵循 [Agent Skills](https://agentskills.io) 规范，可被 `skills` CLI（`vercel-labs/agent-skills`）自动发现与安装。
- **核心内容**：四种触发场景、钉钉通知文案模板（【通知】/【需要确认】/【任务完成】/【任务阻断】）、调用方式、失败重试与安全规范。

---

## 二、目录结构

```
skill-notify-on-handoff/
├── SKILL.md      # 技能定义文件（含 YAML 前置字段 name/description），核心内容
├── README.md     # 安装与使用说明
├── LICENSE       # MIT 许可证
├── .gitignore    # 忽略 .vscode/
└── agents.md     # 本文件，项目汇总说明
```

---

## 三、SKILL.md 关键字段

```yaml
---
name: notify-on-handoff
description: Use when the user needs to be alerted via DingTalk that they must take over — e.g. they asked to be notified, a task needs manual confirmation, the task is complete, or the task is blocked.
---
```

- `name` 仅含小写字母与 `-`，符合 CLI 的 `sanitizeName` 规则。
- `description` 说明技能功能与触发场景，用于 agent 自动匹配加载。
- 注意：`name` 为 `notify-on-handoff`，与仓库名 `skill-notify-on-handoff` 的 `skill-` 前缀不同，`update` 时须使用 `name` 值。

---

## 四、技能涵盖的规则（来自 SKILL.md）

| 章节 | 作用 |
| --- | --- |
| 触发条件 | 用户要求通知、需手动确认、任务完成、任务阻断四类接管时刻 |
| 发送方式 | 通过 `codemode` 调用 `mcp__mcphub__tools_dingtalk_notify`，参数 `content` 文本 |
| 文案模板 | 【通知】/【需要确认】/【任务完成】/【任务阻断】四类前缀 |
| 操作规范 | 检查 `isError`、发送前确认工具存在、失败最多重试 2–3 次、钉钉对外可见需核对文案与避免敏感信息 |

> 规则内容以 `SKILL.md` 为准，本表为当前快照。

---

## 五、安装方式

```bash
# 全局安装（用户级，推荐搭配 -y 非交互）
npx skills add jcleng/skill-notify-on-handoff -y -g

# 仅安装到当前项目（不加 -g）
npx skills add jcleng/skill-notify-on-handoff -y
```

- `-g`：安装到全局技能目录（如 `~/.agents/skills/`、Pi 为 `~/.pi/agent/skills/`）。
- `-y`：跳过确认提示，适合 CI / 非交互场景。

手动安装（不使用 CLI）：

```bash
git clone https://github.com/jcleng/skill-notify-on-handoff.git \
  ~/.pi/agent/skills/notify-on-handoff
```

---

## 六、维护说明

- 修改 `SKILL.md` 内容后，提交并 `git push` 到 `main` 分支即可更新。
- 已安装用户更新：**按 skill 名称**执行 `npx skills update notify-on-handoff -g -y`（`update` 子命令不支持 `owner/repo` 形式）。
- 发布前请确认 `SKILL.md` 的 `name` 与 `description` 字段完整且为合法 YAML。
- 依赖 MCP 工具 `mcp__mcphub__tools_dingtalk_notify`，调用前需确认该工具在当前环境可用。
