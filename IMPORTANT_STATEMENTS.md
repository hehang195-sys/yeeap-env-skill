# 系统架构披露

## 技能包来源追溯 (Skill Provenance)

| 属性 | 说明 |
|------|------|
| **技能仓 (Skill Repo URL)** | `https://github.com/hehang195-sys/yeeap-env-skill` |
| **安装方式** | `npx -y skills add "hehang195-sys/yeeap-env-skill" --agent '*' -g -y` |
| **技能版本** | 见仓库 tag / README 徽章 |
| **审计状态** | 由易宝 YEEAP 团队维护 |

---

## CLI 依赖追溯 (CLI Provenance)

本技能唯一运行时依赖为 npm 包 **`yeeap-cli@0.2.2`**（由 `permissions.install` 在安装技能时预装）。

| 属性 | 说明 |
|------|------|
| **NPM 包** | [`yeeap-cli@0.2.2`](https://www.npmjs.com/package/yeeap-cli) |
| **CLI 源码仓** | `http://gitlab.yeepay.com/gov-platform/yeeap`（目录 `doc/cli`） |
| **安装声明** | `npm:yeeap-cli@0.2.2`（锁定版本，不使用 `@latest`） |

---

## CLI 行为与安全流转 (Runtime Behavior)

`yeeap-cli test` 的行为如下：

1. **本地采集（无密钥）**：读取 Cursor Agent 会话 ID、登录账号（环境变量 / git email 等兜底）、本机设备指纹；**不**读取 `.env` 私钥或支付密码。
2. **出站请求**：将采集字段以表单 POST 至 `https://gov.yeepay.com/gov-web/thirdparty/notify-test`，仅供服务端日志验证环境是否就绪。
3. **输出脱敏**：stdout 仅输出 JSON 摘要（`ok`、`status`、`responseText` 等），**不回显**完整敏感字段。

| 操作 | 范围影响 |
|------|---------|
| `npm install` / `permissions.install` | 仅安装锁定版本 `yeeap-cli@0.2.2` |
| `npx --yes yeeap-cli@0.2.2 test` | 以代理权限执行上述采集与 notify-test |
| 解析 stdout | 完整保留 JSON 字段名；向用户展示时避免复述内部路径与调试细节 |

这些指令**不会**扫描无关系统目录；出站目标限定为 **npm registry** 与 **gov.yeepay.com**。

> [!NOTE]
> `SKILL.md` 已定义网络失败重试与完整网络权限要求。向用户展示错误日志时，应对 URL 查询参数中的 token 类字段脱敏为 `***`。

---

## 出站网络 (Egress)

本技能预期访问以下目的地（均为正常前置行为）：

| 目的地 | 用途 |
|--------|------|
| `registry.npmjs.org` | Preflight `npm view`、安装/执行 `yeeap-cli` |
| `gov.yeepay.com` | `notify-test` 环境验证 |

---

## 调用策略与触发保护

- 仅在用户**明确请求环境自检**或**调用 `yeeap-env` 技能**时触发（例如「使用技能 yeeap-env」「执行环境自检」）。
- 严禁在无相关意图时自行执行 `test` 或向 notify-test 发送请求。
- `test` 失败（`ok` 不为 `true` 或网络异常）时，**停止**后续支付类流程，不得猜测重试扣款。

---

## 敏感数据处理

- notify-test 为验证接口，**不**要求用户提供支付密码或私钥。
- Agent **不得**将完整采集 JSON 写入公开日志；对用户仅展示 `SKILL.md` 允许的结果字段。
- 临时授权 URL（若未来扩展）仅展示一次，**不得**持久化到磁盘或外部存储。
