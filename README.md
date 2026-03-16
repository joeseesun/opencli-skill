# opencli × Claude Code Skill

> 用自然语言操控 B站、知乎、微博、Twitter、YouTube、小红书、Reddit……
> **无需 API Key，直接复用你在 Chrome 里的登录状态。**

---

## 这是什么？

你有没有遇到过这种情况：

- 想让 Claude 帮你**查 B站热门**、**搜知乎**、**看微博热搜**，但 Claude 根本没有这些平台的访问权限
- 用 playwright 自动化太麻烦，还要处理登录态
- 各平台 API 要申请资质，普通用户根本用不了

**opencli 解决了这个问题。**

它把 B站、知乎、微博等 16 个主流平台变成 CLI 工具，**直接复用你 Chrome 浏览器里已有的登录态**——你在哪个网站登录了，Claude 就能帮你操作哪个网站。零配置，零 API Key。

这个 Skill 把 opencli 封装成 Claude Code 的能力，让你用一句话就能：

```
"查下 B站今天的热门视频"
"搜一下知乎上关于大模型的讨论"
"看看我的微博热搜"
"帮我在 Twitter 上搜索 Claude AI 相关的推文"
"查一下雪球上茅台的实时行情"
```

---

## 支持的平台（16 个）

| 平台 | 读取 | 搜索 | 写操作 |
|------|------|------|--------|
| 哔哩哔哩 (B站) | ✅ 热门/排行/动态/历史 | ✅ 视频/用户 | — |
| 知乎 | ✅ 热榜 | ✅ | ✅ 问题详情 |
| 微博 | ✅ 热搜 | — | ✅ 发微博(Playwright) |
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

---

## 安装配置（三步）

### 第一步：安装 opencli CLI 工具

opencli 是由 [@jakevin7（卡比卡比）](https://github.com/jackwener/opencli) 开发的开源工具，先安装它：

```bash
npm install -g @jackwener/opencli
```

验证安装：
```bash
opencli --version
```

### 第二步：在 Chrome 安装 Playwright MCP Bridge 扩展

opencli 通过控制 Chrome 来复用你的登录态，需要安装这个扩展作为桥接层：

1. 打开 Chrome，访问扩展商店搜索 **"Playwright MCP Bridge"**，或直接访问：
   [Chrome Web Store - Playwright MCP Bridge](https://chromewebstore.google.com/detail/playwright-mcp-bridge/kldoghpdblpjbjeechcaoibpfbgfomkn)
2. 点击「添加到 Chrome」安装
3. 确保扩展已启用（Chrome 右上角扩展图标处可见）

> **原理说明**：这个扩展让 Claude Code 可以通过 Playwright 协议控制你的 Chrome 浏览器，从而复用你已有的网站登录态，不需要你重新输入任何账号密码。

### 第三步：安装本 Skill

```bash
npx skills add joeseesun/opencli-skill
```

---

## 使用方法

安装完成后，打开 Chrome 并确保已登录目标网站，然后在 Claude Code 中直接用自然语言说：

```
查下B站今天的热门
搜知乎上关于AI的讨论
看微博热搜前10条
帮我发一条微博：今天天气真好
查一下茅台的股票行情
搜YouTube上LLM教程
```

Claude 会自动调用 opencli 或 Playwright 来完成操作，结果以表格形式展示，中英文标题均附带翻译。

---

## 命令速查

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

---

## ⚠️ 写操作风险说明

对于 Twitter 发推、微博发帖等**写操作**，Claude 会在执行前明确展示将要发布的内容并等待你确认。自动化发帖存在以下风险，请知悉：

- 平台风控可能检测到自动化行为，触发验证码或限流
- 发布后内容立即公开，请确认内容无误再执行
- 避免短时间内频繁操作

---

## 致谢

本 Skill 基于 **[jackwener/opencli](https://github.com/jackwener/opencli)** 构建。

感谢原作者 **[@jakevin7（卡比卡比）](https://github.com/jackwener)** 开发了这个极具创意的工具——把主流网站变成 CLI 接口，让 AI 可以直接复用用户的浏览器登录态，是个非常聪明的设计。

---

## License

MIT

---

## 📱 关注作者

如果这个 Skill 对你有帮助，欢迎关注我获取更多 AI 工具分享：

- **X (Twitter)**: [@vista8](https://x.com/vista8)
- **微信公众号「向阳乔木推荐看」**:

<p align="center">
  <img src="https://github.com/joeseesun/terminal-boost/raw/main/assets/wechat-qr.jpg?raw=true" alt="向阳乔木推荐看公众号二维码" width="300">
</p>
