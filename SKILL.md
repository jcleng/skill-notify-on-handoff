---
name: notify-on-handoff
description: Use when the user needs to be alerted via DingTalk that they must take over — e.g. they asked to be notified, a task needs manual confirmation, the task is complete, or the task is blocked. Trigger on phrases like “通知我”, “notify me”, “需要确认”, “任务完成”, “任务阻断”, “卡住了”, or any moment requiring human intervention.
---

# Notify On Handoff

When the agent hits a point where **the human must take over** — be it a request to be notified, a need for manual confirmation, task completion, or a blocker — send a DingTalk notification so the user is alerted promptly.

The notification tool is `mcp__mcphub__tools_dingtalk_notify`, exposed under the `mcp__mcphub` namespace and callable from `codemode`.

## When to Use (trigger conditions)

Send a DingTalk notification when ANY of these occurs:

- **User requested notification** — user said “通知我/notify me/有结果告诉我/完成后提醒我” etc.
- **Manual confirmation needed** — a decision, choice, approval, or input is required from the user before continuing.
- **Task complete** — the work is done and the user should know the result.
- **Task blocked** — cannot proceed due to missing info, an error, an external dependency, or a failure; needs human intervention.
- **Any handoff moment** — the agent is yielding control back to the user and they should be pinged.

When NOT to use:
- Pure intermediate progress with no decision/result needed (optional; can still notify if asked).
- Private/secret content that must not leave the session (DingTalk is an external, visible channel).

## How to Send

Call from `codemode` (not directly). Signature:

```ts
declare const tools: { mcp__mcphub__tools_dingtalk_notify(args: {
  content: string;   // 通知文本内容
}): Promise<CallToolResult>; };
```

Example:

```js
// @options: { "timeout_ms": 30000 }
const result = await tools.mcp__mcphub__tools_dingtalk_notify({
  content: '【任务完成】数据同步已完成，共处理 1234 条记录，请查收。'
});

if (result?.isError) {
  console.log('钉钉通知发送失败:', JSON.stringify(result, null, 2));
} else {
  console.log('钉钉通知已发送');
}
```

## Message Templates

Keep content clear, scannable, and actionable. Prefix with a bracketed status.

| Situation | Template |
|-----------|----------|
| Manual confirmation | `【需要确认】<问题描述>。请选择：A) xxx  B) xxx` |
| Task complete | `【任务完成】<已完成内容简述>。<结果/产物位置>` |
| Task blocked | `【任务阻断】<卡点说明>。需要：<所需信息/操作>` |
| User-requested notify | `【通知】<用户关心的事件/结果已发生>` |

Attach context the user needs to act: what happened, what's needed from them, and any path/link.

## Operational Rules

- **Always check `isError`** on the result. If `true`, report the failure — never assume success. Do not silently swallow errors or fabricate "sent" status.
- **Confirm tool still exists** before sending: if unsure, verify with `searchTools('dingtalk', { limit: 10 })` or `describeTool('mcp__mcphub__tools_dingtalk_notify')`. Names can change after service restarts.
- **Retry sparingly**: at most 2–3 attempts with a short pause; if it keeps failing, stop and report the error instead of looping.
- **Accuracy & safety**: DingTalk is visible to real people. Double-check the text for correctness and avoid leaking sensitive data. Don't spam — one clear message beats several.
- **Match the moment**: pick the right template so the user immediately knows whether they must decide, can celebrate, or need to unblock something.
