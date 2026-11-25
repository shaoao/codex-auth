# Codex Auth

> 无需验证手机号。粘贴 Session，即刻生成 Codex 授权配置，复制到终端即可登录。

一个单文件、纯前端的 Codex 授权配置生成器。打开网页 → 粘贴 ChatGPT 的 session JSON → 选择目标格式 → 复制 → 在终端执行。没有后端，没有账号，没有构建步骤。

```
ChatGPT Session  ──►  浏览器本地解析  ──►  授权配置  ──►  终端登录
```

---

## 特性

- **六种输出格式**，覆盖 CLI 与常见中转面板
- **纯本地运行**：不上传、不请求、不保存任何凭证
- **一个文件**：CSS、JS、字体、图标、动画全部内联，可离线使用
- **开箱即用**：拖拽文件、读取剪贴板、自动识别多种粘贴形态（完整 JSON、裸 JWT、auth.json）
- **键盘友好**：`⌘↵` 复制、`Esc` 关闭浮层、`[` `]` 切换格式、方向键切换标签
- **深浅色主题**，跟随系统并记忆选择
- 生成结果支持一键复制或下载为文件

## 支持的输出

| 格式 | 产出文件 | 用途 |
| --- | --- | --- |
| macOS | `codex-auth-macos.sh` | 写入 `~/.codex/auth.json`（heredoc） |
| Linux | `codex-auth-linux.sh` | 同上，适配 Linux |
| Windows | `codex-auth-windows.cmd` | base64 + `certutil` 还原 `auth.json` |
| Sub2API | `sub2api.json` | Sub2API 账号导入 |
| CPA | `cpa.json` | CPA 账号配置（缺失 id_token 时生成合成 JWT） |
| Cockpit | `cockpit.json` | Cockpit 账号配置 |

## 隐私与安全

- **没有任何后端**，也没有第三方统计、埋点、字体或图标 CDN。整个页面只有一个 `index.html`。
- **CSP 兜底**：页面内置 `Content-Security-Policy`，`connect-src` 与 `img-src` 均不放行外部地址。即使页面被注入脚本，也无法把数据发出去（已实测 `fetch` / `Image` 请求均被浏览器拦截）。
- **零外部请求**：字体与动画全部内联，断网状态下功能完全一致。
- **访问量统计**只用了 Vercel Web Analytics（部署在 Vercel 时生效）：同源加载 `/_vercel/insights/script.js`，只记录页面访问量，不写 cookie、不采集输入内容，凭证永远不会进入任何请求。部署在其它静态托管上时该脚本不存在，功能不受影响。
- **不落盘凭证**：`localStorage` 只记录 `codex-auth-theme`（主题）和 `codex-auth-format`（格式偏好），不保存 token、邮箱或任何账号信息。
- **唯一外链**是「如何获取」页里的 `chatgpt.com` 地址，需要你主动点击。

所有解析都在你自己的浏览器里完成。关掉页面，数据就没了。

## 使用

1. 确认浏览器里已登录 ChatGPT。
2. 打开 <https://chatgpt.com/api/auth/session>，全选复制页面返回的 JSON。
3. 回到本页面粘贴（也可以拖入保存好的 `auth.json` 文件）。
4. 选择目标格式，点「复制」或「下载」。
5. 把内容粘贴到终端执行，或把 JSON 导入对应面板。

> 输入框不会自动抢焦点，点一下再 `⌘V` / `Ctrl+V`；也可以直接点「从剪贴板」按钮。

## 本地使用

双击 `index.html` 就能用，不需要服务器。

想模拟线上环境（例如测试剪贴板权限）：

```bash
python3 -m http.server 8080
# 然后访问 http://localhost:8080
```

## 关于过期时间

`https://chatgpt.com/api/auth/session` 返回的 `accessToken` 是一个有效期约 **7 天**的 JWT，且**不包含 `refresh_token`**。这意味着：

- 由这条链路生成的配置，到期后需要重新获取 Session；
- 想长期免维护，请改用官方 `codex login`（OAuth 流程），它会把 `refresh_token` 写入 `~/.codex/auth.json`，之后由 Codex 自动续期；
- 如果粘贴的内容本身就带有 `refresh_token`（例如一份已有的 `auth.json`），本工具会原样保留，不会被覆盖。

页面会在状态行显示到期时间，剩余不足 48 小时或已过期时变红提示。

## 技术说明

- 单文件、无构建、无依赖；动画使用原生 Web Animations API 与 CSS keyframes
- JWT 解析：`atob` + `TextDecoder`，读取 `https://api.openai.com/auth` 与 `/profile` 命名空间
- 主题：CSS 变量 + `data-theme`，首屏前由内联脚本恢复，避免闪烁
- 无障碍：`role="tablist"` + roving tabindex、`aria-live` 状态播报、`prefers-reduced-motion` 降级

## 免责声明

本项目为个人工具，与 OpenAI 无任何关联。请仅用于处理你**自己的**账号凭证，并遵守 OpenAI 的服务条款。因使用本工具产生的任何后果由使用者自行承担。

## License

[MIT](./LICENSE)
