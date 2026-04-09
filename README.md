# Qiaomu OpenCLI Skills

> 基于 [jackwener/opencli](https://github.com/jackwener/opencli) 的 Claude Code Skills 集合

[![Upstream](https://img.shields.io/badge/upstream-jackwener%2Fopencli-blue)](https://github.com/jackwener/opencli)
[![Version](https://img.shields.io/badge/version-1.6.9-green)](https://github.com/jackwener/opencli/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)

将 79+ 网站和桌面应用转化为 CLI 接口，让 AI Agent 能够直接操作浏览器、搜索内容、发布推文、下载视频等。

## 🎯 核心能力

- **79+ 网站适配器**：Bilibili、Twitter、Reddit、小红书、知乎、YouTube、HackerNews 等
- **桌面应用控制**：Cursor、Codex、ChatGPT、Notion、微信等 Electron 应用
- **浏览器自动化**：通过 CDP 直接控制 Chrome，复用登录状态
- **智能搜索路由**：自动选择最佳数据源（AI 源 + 专用源）
- **适配器生成**：从任意 URL 自动生成 CLI 适配器
- **自动修复**：损坏的适配器自动诊断和修复

## 📦 Skills 列表

| Skill | 描述 | 使用场景 |
|-------|------|----------|
| **qiaomu-opencli-usage** | 79+ 适配器的完整命令参考 | 查询热门、搜索、发布、下载等日常操作 |
| **qiaomu-opencli-browser** | 浏览器自动化控制 | 需要直接操作浏览器时（点击、输入、截图） |
| **qiaomu-opencli-explorer** | 适配器创建完整指南 | 为新网站创建 CLI 适配器 |
| **qiaomu-opencli-oneshot** | 快速生成单个命令 | 从 URL 快速生成一次性适配器 |
| **qiaomu-opencli-autofix** | 适配器自动修复 | 适配器失效时自动诊断修复 |
| **qiaomu-smart-search** | 智能搜索路由器 | 自动选择最佳搜索源 |

## 🚀 快速开始

### 1. 安装 OpenCLI

```bash
npm install -g @jackwener/opencli
```

### 2. 安装浏览器扩展

1. 从 [Releases](https://github.com/jackwener/opencli/releases) 下载 `opencli-extension.zip`
2. 解压后在 `chrome://extensions` 加载解压后的文件夹
3. 确保 Chrome 已登录目标网站

### 3. 安装 Skills

```bash
# 安装所有 skills
npx skills add joeseesun/qiaomu-opencli-skills

# 或单独安装
npx skills add joeseesun/qiaomu-opencli-skills --skill qiaomu-opencli-usage
npx skills add joeseesun/qiaomu-opencli-skills --skill qiaomu-smart-search
```

### 4. 验证安装

```bash
opencli doctor
opencli list
opencli hackernews top --limit 5
```

## 💡 使用示例

### 热门内容

```bash
# B站热门
opencli bilibili hot --limit 10 -f json

# Twitter 趋势
opencli twitter trending -f json

# HackerNews 头条
opencli hackernews top --limit 20 -f json

# 小红书 feed
opencli xiaohongshu feed -f json
```

### 搜索

```bash
# 搜索 B站
opencli bilibili search --keyword "AI" -f json

# 搜索知乎
opencli zhihu search --keyword "大模型" -f json

# 搜索 YouTube
opencli youtube search --query "LLM tutorial" -f json
```

### 发布内容

```bash
# 发推
opencli twitter post --text "Hello from CLI!"

# 回复推文
opencli twitter reply --url "https://x.com/.../status/123" --text "Great!"
```

### 下载

```bash
# 下载小红书图片
opencli xiaohongshu download --note-id abc123 --output ./xhs

# 下载 B站视频
opencli bilibili download --bvid BV1xxx --output ./bilibili
```

### 桌面应用控制

```bash
# Cursor
opencli cursor send "refactor this function"

# ChatGPT
opencli chatgpt ask "explain quantum computing"

# Notion
opencli notion search --keyword "meeting notes"
```

### 智能搜索

```bash
# 自动路由到最佳数据源
opencli smart-search "最新 AI 论文"
opencli smart-search "React 最佳实践"
```

## 🏗️ 架构

```
qiaomu-opencli-skills/
├── qiaomu-opencli-usage/       # 主要使用技能
│   ├── SKILL.md
│   └── commands.md             # 完整命令参考
├── qiaomu-opencli-browser/     # 浏览器自动化
│   └── SKILL.md
├── qiaomu-opencli-explorer/    # 适配器创建
│   └── SKILL.md
├── qiaomu-opencli-oneshot/     # 快速生成
│   └── SKILL.md
├── qiaomu-opencli-autofix/     # 自动修复
│   └── SKILL.md
└── qiaomu-smart-search/        # 智能搜索
    ├── SKILL.md
    └── references/             # 数据源配置
```

## 🔧 认证体系

OpenCLI 支持 5 级认证：

1. **public** - 公开 API，无需认证
2. **cookie** - 复用浏览器 Cookie（最常用）
3. **header** - 需要 API Token
4. **intercept** - 拦截请求获取 Token
5. **ui** - 桌面应用 CDP 控制

## 📚 支持的网站（部分）

### 社交媒体
Twitter/X, Reddit, 小红书, 知乎, 微博, Instagram, Facebook, Bluesky

### 视频平台
Bilibili, YouTube, TikTok, 抖音

### 技术社区
HackerNews, V2EX, Linux.do, Stack Overflow, GitHub

### AI 工具
Grok, Doubao, ChatGPT, Gemini, Cursor, Codex, NotebookLM

### 金融
雪球, Yahoo Finance, Barchart, 新浪财经, Bloomberg

### 其他
Google, Wikipedia, arXiv, Medium, Substack, BOSS直聘, LinkedIn

## 🤝 贡献

本项目基于 [jackwener/opencli](https://github.com/jackwener/opencli)，主要贡献应提交到上游仓库。

本仓库专注于：
- 为 Claude Code 优化 Skills 文档
- 添加中文使用示例
- 维护 qiaomu 前缀的 Skills 版本

## 📄 许可证

MIT License - 详见 [LICENSE](./LICENSE)

## 📱 关注作者

如果这个项目对你有帮助，欢迎关注我获取更多技术分享：

- **X (Twitter)**: [@vista8](https://x.com/vista8)
- **微信公众号「向阳乔木推荐看」**

## 🔗 相关链接

- [上游项目](https://github.com/jackwener/opencli)
- [OpenCLI 文档](https://github.com/jackwener/opencli#readme)
- [Claude Code Skills](https://github.com/anthropics/claude-code)
