# opencli-skill × Claude Code

> Control Bilibili, Zhihu, Twitter/X, YouTube, Weibo, Reddit and 10 more platforms with natural language — reusing your Chrome login session, no API keys needed.
>
> 用自然语言操控 B站、知乎、Twitter/X、YouTube、微博、Reddit 等 16 个平台——直接复用你的 Chrome 登录态，无需任何 API Key。

**[English](#english) | [中文](#中文)**

---

<a name="english"></a>
## English

### What is this?

Have you ever wanted Claude to:
- Browse **Bilibili** trending, search **Zhihu**, check **Weibo** hot topics
- Search **YouTube**, get **Reddit** posts, read **HackerNews**
- Check **stock prices** on Yahoo Finance or Xueqiu
- Post a **tweet** or search your **Twitter** timeline

...but Claude has no access to these platforms?

**This skill bridges that gap.**

It wraps [opencli](https://github.com/jackwener/opencli) — a brilliant open-source CLI tool that turns 16 major platforms into command-line interfaces by **reusing your existing Chrome login sessions**. No API keys. No re-authentication. Just open Chrome, log in as usual, and Claude can do the rest.

### Supported Platforms (16)

| Platform | Read | Search | Write |
|----------|------|--------|-------|
| Bilibili (B站) | ✅ Hot/Ranking/Feed/History | ✅ Videos/Users | — |
| Zhihu (知乎) | ✅ Hot list | ✅ | ✅ Question details |
| Weibo (微博) | ✅ Trending | — | ✅ Post (via Playwright) |
| Twitter/X | ✅ Timeline/Trending/Bookmarks | ✅ | ✅ Post/Reply/Like |
| YouTube | — | ✅ | — |
| Xiaohongshu (小红书) | ✅ Recommended feed | ✅ | — |
| Reddit | ✅ Home/Hot | ✅ | — |
| HackerNews | ✅ Top stories | — | — |
| V2EX | ✅ Hot/Latest | — | ✅ Daily check-in |
| Xueqiu (雪球) | ✅ Hot/Stocks/Watchlist | ✅ | — |
| BOSS直聘 | — | ✅ Jobs | — |
| BBC | ✅ News | — | — |
| Reuters | — | ✅ | — |
| 什么值得买 | — | ✅ Deals | — |
| Yahoo Finance | ✅ Stock quotes | — | — |
| Ctrip (携程) | — | ✅ Attractions/Cities | — |

### Prerequisites

Before installation, check that you have all of these:

- [ ] **Node.js** v16+ — [Download from nodejs.org](https://nodejs.org/)
- [ ] **Chrome browser** open and logged in to your target platforms
- [ ] **Playwright MCP Bridge** Chrome extension — [Install from Chrome Web Store](https://chromewebstore.google.com/detail/playwright-mcp-bridge/kldoghpdblpjbjeechcaoibpfbgfomkn)
- [ ] **Playwright MCP** configured in Claude Code (see Step 3 below)
- [ ] **Claude Code** — [Install](https://claude.ai/code)

### Installation (4 Steps)

**Step 1 — Install the opencli CLI tool**

```bash
npm install -g @jackwener/opencli
```

Verify it works:
```bash
opencli --version
```

**Step 2 — Install Playwright MCP Bridge in Chrome**

1. Open Chrome and go to the [Playwright MCP Bridge](https://chromewebstore.google.com/detail/playwright-mcp-bridge/kldoghpdblpjbjeechcaoibpfbgfomkn) page on the Chrome Web Store
2. Click **"Add to Chrome"** and confirm
3. Check that the extension icon appears in Chrome's toolbar (top-right)

> **Why is this needed?** opencli controls your Chrome browser to reuse your existing login sessions. This extension acts as the bridge between opencli and Chrome, so you never have to enter your credentials again.

**Step 3 — Configure Playwright MCP in Claude Code**

Claude Code needs the Playwright MCP server to control Chrome. Run this once in your terminal:

```bash
claude mcp add playwright --scope user -- npx @playwright/mcp@latest
```

Verify it was added:
```bash
claude mcp list
```

You should see `playwright` in the list.

**Step 4 — Install this skill**

```bash
npx skills add joeseesun/opencli-skill
```

That's it! Restart Claude Code to activate the skill.

### Usage

Make sure Chrome is open and you're logged in to the target platforms, then talk to Claude naturally:

```
"Search YouTube for LLM tutorials"
"Get the top 20 stories on HackerNews"
"What's trending on Twitter right now?"
"Search Reddit r/MachineLearning for transformer papers"
"Get BBC news headlines"
"Check AAPL stock price"
"Post a tweet: Just discovered Claude Code skills!"
```

Claude automatically picks the right opencli command, runs it, and displays results in a clean table with translated titles.

### Command Reference

```bash
# Bilibili
opencli bilibili hot --limit 10 -f json
opencli bilibili search --keyword "AI"

# Twitter/X
opencli twitter timeline -f json
opencli twitter post --text "Hello from Claude!"
opencli twitter search --query "claude AI"

# YouTube
opencli youtube search --query "LLM tutorial"

# HackerNews
opencli hackernews top --limit 20 -f json

# Reddit
opencli reddit hot --subreddit MachineLearning

# Yahoo Finance
opencli yahoo-finance quote --symbol AAPL
```

Full reference for all 55 commands: [references/commands.md](references/commands.md)

### ⚠️ Write Operations Warning

For write operations (posting tweets, Weibo updates, etc.), Claude will **show you the content and ask for confirmation** before posting anything.

Known risks:
- Platform bot-detection may trigger CAPTCHAs or temporary rate limits
- Once published, content is immediately public and cannot be recalled by the AI
- Avoid rapid repeated posting

### Troubleshooting

| Problem | Fix |
|---------|-----|
| `opencli: command not found` | Re-run `npm install -g @jackwener/opencli`; check your PATH |
| Chrome not being controlled | Make sure Chrome is open; verify Playwright MCP Bridge extension is enabled |
| Login state not recognized | In Chrome, manually log in to the target site first |
| Repeated browser authorization prompts / works in terminal but not in agent runtime | Check `PLAYWRIGHT_MCP_EXTENSION_TOKEN`. In OpenClaw, write it to `~/.openclaw/.env` and restart Gateway. In Claude Code or a direct terminal workflow, persist it in the shell env used to launch the agent, then restart the session |
| "Playwright MCP not found" error | Re-run Step 3: `claude mcp add playwright ...` |
| `npx skills add` fails | Make sure Node.js v16+ is installed |

#### Environment Token Check

If the browser bridge keeps asking you to authorize again, or `opencli` works in a normal terminal but fails inside an agent runtime, verify that the current process can see:

```bash
PLAYWRIGHT_MCP_EXTENSION_TOKEN
```

Quick verification:

```bash
python -c "import os; print('PRESENT' if os.getenv('PLAYWRIGHT_MCP_EXTENSION_TOKEN') else 'MISSING')"
```

OpenClaw users should usually persist the token here:

```bash
echo 'PLAYWRIGHT_MCP_EXTENSION_TOKEN=your-token' >> ~/.openclaw/.env
openclaw gateway restart
```

Claude Code or direct terminal users should persist the token in the shell environment that launches the agent, then start a new session before retrying `opencli`.

### Credits

Built on **[jackwener/opencli](https://github.com/jackwener/opencli)**.

Huge thanks to **[@jakevin7 (卡比卡比)](https://github.com/jackwener)** for building this genuinely clever tool — turning major websites into CLI interfaces that reuse existing browser sessions is a brilliant idea that makes AI agents far more capable.

---

<a name="中文"></a>
## 中文

### 这是什么？

你有没有遇到过这种情况：

- 想让 Claude 帮你**查 B站热门**、**搜知乎**、**看微博热搜**，但 Claude 根本没有这些平台的访问权限
- 用 Playwright 自动化太麻烦，还要单独处理登录态
- 各平台 API 要申请资质，普通用户根本用不了

**这个 Skill 解决了这个问题。**

它把 [opencli](https://github.com/jackwener/opencli) 封装成 Claude Code 的能力——opencli 是一个开源 CLI 工具，把 16 个主流平台变成命令行接口，**直接复用你 Chrome 浏览器里已有的登录态**。零配置，零 API Key。你在哪个网站登录了，Claude 就能帮你操作哪个网站。

### 支持的平台（16 个）

| 平台 | 读取 | 搜索 | 写操作 |
|------|------|------|--------|
| 哔哩哔哩 (B站) | ✅ 热门/排行/动态/历史 | ✅ 视频/用户 | — |
| 知乎 | ✅ 热榜 | ✅ | ✅ 问题详情 |
| 微博 | ✅ 热搜 | — | ✅ 发微博（Playwright） |
| Twitter/X | ✅ 时间线/热门/书签 | ✅ | ✅ 发推/回复/点赞 |
| YouTube | — | ✅ | — |
| 小红书 | ✅ 推荐 Feed | ✅ | — |
| Reddit | ✅ 首页/热门 | ✅ | — |
| HackerNews | ✅ Top | — | — |
| V2EX | ✅ 热门/最新 | — | ✅ 签到 |
| 雪球 | ✅ 热门/行情/自选股 | ✅ | — |
| BOSS直聘 | — | ✅ 职位 | — |
| BBC | ✅ 新闻 | — | — |
| 路透社 | — | ✅ | — |
| 什么值得买 | — | ✅ | — |
| Yahoo Finance | ✅ 股票行情 | — | — |
| 携程 | — | ✅ 景点/城市 | — |

### 前置条件

安装前，请逐一确认以下条件都已满足：

- [ ] **Node.js** v16 及以上 — [从 nodejs.org 下载](https://nodejs.org/)
- [ ] **Chrome 浏览器** 已打开，并已登录目标网站
- [ ] **Playwright MCP Bridge** Chrome 扩展 — [Chrome 商店安装](https://chromewebstore.google.com/detail/playwright-mcp-bridge/kldoghpdblpjbjeechcaoibpfbgfomkn)
- [ ] **Playwright MCP** 已在 Claude Code 中配置（见第三步）
- [ ] **Claude Code** — [安装地址](https://claude.ai/code)

### 安装配置（四步）

**第一步：安装 opencli CLI 工具**

opencli 是由 [@jakevin7（卡比卡比）](https://github.com/jackwener/opencli) 开发的开源工具：

```bash
npm install -g @jackwener/opencli
```

验证安装成功：
```bash
opencli --version
```

如果看到版本号，说明安装正常。

**第二步：在 Chrome 安装 Playwright MCP Bridge 扩展**

1. 打开 Chrome，访问 [Playwright MCP Bridge 扩展页面](https://chromewebstore.google.com/detail/playwright-mcp-bridge/kldoghpdblpjbjeechcaoibpfbgfomkn)
2. 点击「添加到 Chrome」，在弹窗中确认
3. 检查 Chrome 右上角工具栏，确认扩展图标已出现

> **为什么需要这个扩展？** opencli 需要控制你的 Chrome 浏览器来复用你的登录态。这个扩展充当了 opencli 和 Chrome 之间的桥梁，让 Claude 可以直接用你已经登录好的账号，不需要你重新输入密码。

**第三步：在 Claude Code 中配置 Playwright MCP**

Claude Code 需要 Playwright MCP 服务才能控制 Chrome。在终端运行一次即可：

```bash
claude mcp add playwright --scope user -- npx @playwright/mcp@latest
```

验证是否配置成功：
```bash
claude mcp list
```

看到列表中有 `playwright` 就说明配置好了。

**第四步：安装本 Skill**

```bash
npx skills add joeseesun/opencli-skill
```

安装完成后重启 Claude Code，Skill 即可生效。

### 使用方法

确保 Chrome 已打开且已登录目标网站，然后在 Claude Code 中用自然语言说：

```
查下B站今天的热门
搜知乎上关于AI大模型的讨论
看微博热搜前10条
帮我发一条微博：今天天气真好
查一下茅台的股票行情
搜YouTube上的LLM教程
```

Claude 会自动调用 opencli 或 Playwright 完成操作，结果以表格形式展示，英文标题附带中文翻译。

### 命令速查

```bash
# B站
opencli bilibili hot --limit 10 -f json
opencli bilibili search --keyword "AI"
opencli bilibili history

# 知乎
opencli zhihu hot -f json
opencli zhihu search --keyword "大模型"

# 微博
opencli weibo hot -f json

# Twitter/X
opencli twitter timeline -f json
opencli twitter post --text "Hello from Claude!"
opencli twitter search --query "claude AI"

# YouTube
opencli youtube search --query "LLM tutorial"

# 雪球
opencli xueqiu stock --symbol SH600519   # 茅台行情
opencli xueqiu watchlist                  # 我的自选股

# HackerNews
opencli hackernews top --limit 20 -f json

# Reddit
opencli reddit hot --subreddit MachineLearning
```

完整 55 条命令详见 [references/commands.md](references/commands.md)。

### ⚠️ 写操作风险说明

对于 Twitter 发推、微博发帖等**写操作**，Claude 会在执行前明确展示将要发布的内容，并等待你确认后才执行。自动化发帖存在以下风险，请知悉：

- 平台风控可能检测到自动化行为，触发验证码或限流
- 发布后内容立即公开，AI 无法帮你撤回
- 避免短时间内频繁操作

### 常见问题

| 问题 | 解决方法 |
|------|----------|
| `opencli: command not found` | 重新运行 `npm install -g @jackwener/opencli`，检查 PATH 配置 |
| Chrome 无法被控制 | 确保 Chrome 已打开，且 Playwright MCP Bridge 扩展已启用 |
| 登录态未识别 | 在 Chrome 中手动登录目标网站后再试 |
| 浏览器反复要求授权，或 terminal 能跑但 agent 里不行 | 检查 `PLAYWRIGHT_MCP_EXTENSION_TOKEN`。如果是 OpenClaw，优先写入 `~/.openclaw/.env` 并重启 Gateway；如果是 Claude Code 或普通终端工作流，把它持久化到启动 agent 的 shell 环境后重开会话 |
| 提示找不到 Playwright MCP | 重新执行第三步的配置命令 |
| `npx skills add` 报错 | 确认 Node.js v16+ 已安装 |

#### 环境变量检查

如果浏览器桥接链路反复要求你重新授权，或者 `opencli` 在普通 terminal 里可用、但在 agent 运行时里失效，先确认当前进程是否能看到：

```bash
PLAYWRIGHT_MCP_EXTENSION_TOKEN
```

快速验证：

```bash
python -c "import os; print('PRESENT' if os.getenv('PLAYWRIGHT_MCP_EXTENSION_TOKEN') else 'MISSING')"
```

如果你在 OpenClaw 中使用，通常建议把 token 写到这里：

```bash
echo 'PLAYWRIGHT_MCP_EXTENSION_TOKEN=your-token' >> ~/.openclaw/.env
openclaw gateway restart
```

如果你在 Claude Code 或普通终端里使用，请把 token 持久化到启动 agent 的 shell 环境中，然后开启一个新的会话后再重试 `opencli`。

### 致谢

本 Skill 基于 **[jackwener/opencli](https://github.com/jackwener/opencli)** 构建。

感谢原作者 **[@jakevin7（卡比卡比）](https://github.com/jackwener)** 开发了这个极具创意的工具——把主流网站变成 CLI 接口，让 AI 可以直接复用用户的浏览器登录态，是个非常聪明的设计。

---

## License

MIT

---

## 📱 Follow the Author / 关注作者

If this skill helps you, follow me for more AI tools and tips.

如果这个 Skill 对你有帮助，欢迎关注我获取更多 AI 工具分享：

- **X (Twitter)**: [@vista8](https://x.com/vista8)
- **微信公众号「向阳乔木推荐看」**:

<p align="center">
  <img src="https://github.com/joeseesun/terminal-boost/raw/main/assets/wechat-qr.jpg?raw=true" alt="向阳乔木推荐看公众号二维码" width="300">
</p>
