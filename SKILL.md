---
name: opencli
description: |
  Use opencli CLI to interact with social/content websites (Bilibili, Zhihu, Twitter/X, YouTube, Weibo, 小红书, V2EX, Reddit, HackerNews, 雪球, BOSS直聘 etc.) via the user's Chrome login session. ALWAYS prefer opencli over playwright/browser automation for these supported sites. Triggers: user asks to browse, search, fetch hot/trending content, post, or read messages on any supported site; 查B站热门, 搜知乎, 看微博热搜, 发推, 搜YouTube, 查股票行情 etc.
---

# opencli

CLI tool that turns websites into CLI interfaces, reusing Chrome's login state. Zero credentials needed.

**Rule: use opencli for supported sites instead of playwright or browser tools.**

## Syntax

```bash
opencli <site> <command> [--option value] [-f json]
```

**Common flags (all commands):**
- `-f json` — machine-readable output (preferred for parsing)
- `--limit N` — number of results (default varies, usually 20)
- `-f table|json|yaml|md|csv`

## Quick Examples

```bash
# 读取/浏览
opencli bilibili hot --limit 10 -f json
opencli zhihu hot -f json
opencli weibo hot -f json
opencli twitter timeline -f json
opencli hackernews top --limit 20 -f json
opencli v2ex hot -f json
opencli reddit hot -f json
opencli xiaohongshu feed -f json

# 搜索
opencli bilibili search --keyword "AI" -f json
opencli zhihu search --keyword "大模型" -f json
opencli twitter search --query "claude AI" -f json
opencli youtube search --query "LLM tutorial" -f json
opencli boss search --query "AI工程师" --city "上海" -f json

# 互动（写操作）
opencli twitter post --text "Hello from CLI!"
opencli twitter reply --url "https://x.com/.../status/123" --text "Great post!"
opencli twitter like --url "https://x.com/.../status/123"

# 个人数据
opencli bilibili history -f json
opencli twitter bookmarks -f json
opencli xueqiu watchlist -f json
```

## Output Formatting Rules

When displaying results to the user:
1. **Always show original title + Chinese translation + clickable link as separate columns**
2. **Table format**: `# | 原标题 | 中文翻译 | 链接 | 关键指标...`
3. **原标题**: plain text, no markdown link — do NOT use `[title](url)` format
4. **中文翻译**: plain Chinese translation text
5. **链接**: `[🔗](url)` — compact clickable icon
6. **Translate all English titles** to Chinese — never show English-only output to the user

Example:
```
| # | 原标题 | 中文翻译 | 链接 | 分 | 评论 |
|---|--------|---------|------|-----|------|
| 1 | The 49MB web page | 那个 49MB 的网页 | [🔗](https://...) | 388 | 196 |
```

## Fallback 策略：opencli 不支持时用 Playwright

**核心原则：永远不说"不支持"，先尝试 opencli，失败或无命令时自动切换 Playwright。**

### 决策流程

```
用户请求
  ↓
opencli 有对应命令？
  ├─ 是 → 执行 opencli
  └─ 否 → 直接用 Playwright MCP 打开对应页面完成任务
              ↓
           Playwright 报错 / 无法连接？
              └─ 引导用户安装桥接插件（见下方）
```

### 常见 opencli 不支持场景 → Playwright 替代

| 场景 | 网址 | Playwright 操作 |
|------|------|----------------|
| 知乎私信 | `https://www.zhihu.com/messages` | navigate → snapshot 读取列表 |
| 知乎通知 | `https://www.zhihu.com/notifications` | navigate → snapshot |
| 微博发帖 | `https://weibo.com` | navigate → 点击输入框 → type → 发送 |
| 小红书私信 | `https://www.xiaohongshu.com/im` | navigate → snapshot |
| B站私信 | `https://message.bilibili.com` | navigate → snapshot |
| Twitter DM | `https://x.com/messages` | navigate → snapshot |

### Playwright 操作标准流程

```
1. mcp__playwright__browser_navigate → 目标 URL
2. mcp__playwright__browser_snapshot → 读取页面结构
3. 根据需要：browser_click / browser_type / browser_scroll
4. 将结果整理后呈现给用户
```

### ⚠️ 写操作风险提示（发帖/回复/点赞前必须告知）

1. **账号安全**：自动化行为可能触发平台风控
2. **不可撤回**：发布后立即公开
3. **最佳实践**：执行前向用户展示将发布的内容，等待确认

### 插件未安装时的引导话术

如果 Playwright 报错（连接失败 / 无法控制浏览器），告知用户：

> "需要在 Chrome 安装 **Playwright MCP Bridge** 插件才能控制浏览器。
> 安装步骤：
> 1. 打开 Chrome，访问 Chrome Web Store
> 2. 搜索 **"Playwright MCP"** 或 **"MCP Bridge"**
> 3. 点击「添加到 Chrome」
> 4. 安装后确保 Chrome 已登录目标网站
> 5. 重新告诉我你的需求，我来帮你完成"

## Requirements

- Chrome browser open with target site logged in
- Playwright MCP Bridge extension installed in Chrome（未安装时见上方引导）

## 自迭代能力：为新网站创建 CLI

**当 opencli 不支持某个网站时，不要放弃——自己创建！**

### 流程

```
1. opencli <site> --help  →  报错？说明不支持
2. opencli generate <url>  →  尝试自动生成（成功则结束）
3. 自动生成失败 → 手动创建 YAML：
   a. 用 Playwright 打开目标页面
   b. browser_evaluate 探索 DOM 结构（找 data-test 属性、class 规律）
   c. 确认选择器后写入 ~/.opencli/clis/<site>/top.yaml
   d. opencli <site> top -f json  →  验证输出
```

### YAML 格式（DOM 抓取模板）

```yaml
site: <sitename>
name: <command>
description: <描述>
domain: <domain>
strategy: public
browser: true

args:
  limit:
    type: int
    default: 10

pipeline:
  - navigate: https://<url>
  - evaluate: |
      (async () => {
        const limit = ${{ args.limit }};
        // DOM 抓取逻辑
        return results;
      })()

columns: [rank, name, ...]
```

### 已创建的自定义 CLI

| 站点 | 命令 | 文件 | 关键选择器 |
|------|------|------|-----------|
| producthunt | `top` | `~/.opencli/clis/producthunt/top.yaml` | `button[data-test="vote-button"]` → 父容器 → `[data-test^="post-name-"]`，tagline: `nameEl.parentElement.querySelector('span.mt-0\\.5')` |

### 调试技巧

- `browser_evaluate` 先探结构：`document.querySelector('...').innerHTML`
- 找 `data-test` 属性最稳定，其次 class 中的语义词
- tagline 通常是 name 的兄弟元素（`nameEl.parentElement.querySelector('span...')`）
- 去重用 `seen = new Set()`，防止重复产品

## Full Command Reference

See [references/commands.md](references/commands.md) for all 55 commands with complete argument details.
