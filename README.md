# yeeap-env-skill

![Version](https://img.shields.io/badge/version-0.2.2-blue.svg)

**Project URL:** [https://github.com/hehang195-sys/yeeap-env-skill](https://github.com/hehang195-sys/yeeap-env-skill)

[中文说明](#中文说明) | [English](#english)

---

## 中文说明

### 项目简介

`yeeap-env-skill` 是面向 Cursor / OpenClaw Agent 的 **YEEAP 环境自检**技能：通过 `yeeap-cli test` 采集宿主上下文并调用易宝 `notify-test`，供服务端确认当前会话是否具备支付前置环境。

### 安装（整行复制，无需改地址）

在 Cursor 终端执行：

```bash
npx -y skills add "hehang195-sys/yeeap-env-skill" --agent '*' -g -y
```

安装后在对话中说：**使用技能 `yeeap-env`**。执行 test 时请批准 **完整网络（Full network access）**。

### 核心命令

```bash
npx --yes yeeap-cli@0.2.2 test
```

### 核心文件

| 文件 | 说明 |
|------|------|
| [SKILL.md](./SKILL.md) | Agent 执行协议（Preflight、test、结果分流） |
| [IMPORTANT_STATEMENTS.md](./IMPORTANT_STATEMENTS.md) | 技能包/CLI 来源追溯、出站策略、触发边界 |

### 权限要求

- `install`：预装 `npm:yeeap-cli@0.2.2`
- 出站：`registry.npmjs.org`、`gov.yeepay.com`

---

## English

### Overview

`yeeap-env-skill` collects agent/host context via `yeeap-cli test` and posts to YeePay `notify-test` for server-side environment verification.

### Install

```bash
npx -y skills add "hehang195-sys/yeeap-env-skill" --agent '*' -g -y
```

### Key files

- `SKILL.md` — Agent execution protocol
- `IMPORTANT_STATEMENTS.md` — Provenance, egress, and invocation boundaries

### Permissions

- `install`: `npm:yeeap-cli@0.2.2`
- Egress: npm registry and `gov.yeepay.com`
