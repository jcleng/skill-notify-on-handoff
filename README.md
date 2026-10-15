# skill-notify-on-handoff

当用户需要接管时，通过**钉钉机器人**发送通知告知用户的 agent skill。触发时机：用户要求通知、需要手动确认、任务完成、任务阻断/卡住。

## 触发场景

| 场景 | 钉钉通知前缀 |
|------|-------------|
| 用户说「通知我 / 完成后提醒我」 | 【通知】 |
| 需要手动确认 / 决策 / 选择 | 【需要确认】 |
| 任务完成 | 【任务完成】 |
| 任务阻断 / 卡住 / 缺信息 | 【任务阻断】 |

## 安装

```bash
npx skills add jcleng/skill-notify-on-handoff -y -g
```

- `-g`：安装到全局（用户级）技能目录。
- `-y`：跳过确认提示（适合 CI / 非交互场景）。

仅安装到当前项目（不加 `-g`）：

```bash
npx skills add jcleng/skill-notify-on-handoff -y
```

手动安装（不使用 CLI）：

```bash
git clone https://github.com/jcleng/skill-notify-on-handoff.git \
  ~/.pi/agent/skills/notify-on-handoff
```

## 更新

按 **skill 名称** 更新（`update` 子命令不支持 `owner/repo` 形式）：

```bash
npx skills update notify-on-handoff -g -y
```

## 依赖

- MCP 工具 `mcp__mcphub__tools_dingtalk_notify`（通过 `codemode` 调用）

## 规范

遵循 [Agent Skills](https://agentskills.io) 规范，根目录 `SKILL.md` 含 `name` 与 `description` 前置字段，可被 `skills` CLI（`vercel-labs/agent-skills`）自动发现。

## License

MIT
