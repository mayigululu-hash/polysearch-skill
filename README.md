# polysearch-skill

**当前版本 v1.0.0** ｜ Current version: v1.0.0 ｜ [English](#english-英文说明)

配套 MCP：[mayigululu-hash/polysearch](https://github.com/mayigululu-hash/polysearch)（豆包搜索 + DeepSeek Search 聚合，配额尽自动切换）

一个给 AI agent 的**搜索路由 skill**。它不负责搜索本身，负责调度：什么活走内置搜索，什么活走搜索服务，什么活必须上浏览器。核心原则一句话：**免费的先用，特殊的走专门通道，点名了照办，换了通道要说一声。**

## 它解决什么问题

给 agent 接了搜索之后，真实使用里马上会遇到三个问题：

1. **内置搜索有免费额度**，用着用着就配额耗尽，agent 当场抓瞎
2. **登录态内容、强反爬站点**，搜索 API 根本搜不到，得动浏览器
3. **核实类请求**（「这是真的吗」「信源打架」）需要多方交叉验证，不能只搜一次

这个 skill 把这些全部写成路由规则，agent 每次搜索先选路再执行。MCP 还没装也能用：路由自动退化为「内置 → 浏览器」，装上 polysearch MCP 后自动升级为五级阶梯。

## 路由阶梯（省钱序）

```
用户点名（用豆包搜/用deepseek搜/浏览器搜）→ 照办
平台专门 skill/适配器（X、小红书、公众号……）→ 交给它们
内置 WebSearch → 普通搜索默认
polysearch MCP → 内置挂了/配额尽，或点名豆包/DeepSeek
浏览器自动化 → 登录态、强反爬、页面交互，最后兜底
```

两个设计值得单独说：

- **交叉核查模式**：触发词「核实、求证、是真的吗、信源打架」。把问题拆成正反两方分头搜，输出一致点、冲突点、可信度倾向三段式，不替用户下最终结论
- **切换报备**：凡走了非默认通道，回答末尾固定报一句「本次走的 polysearch（内置搜索额度已用完）」，让用户心里有账

## 安装

把 `SKILL.md` 所在目录拷进你的 skills 目录即可：

```bash
git clone https://github.com/mayigululu-hash/polysearch-skill.git
mkdir -p ~/.claude/skills
cp -r polysearch-skill ~/.claude/skills/polysearch
```

- Claude Code：`~/.claude/skills/` 或项目内 `.claude/skills/`
- ZCode：`~/.zcode/skills/`
- 其他宿主：按各自的 skills 目录约定放置

推荐搭配 [mayigululu-hash/polysearch](https://github.com/mayigululu-hash/polysearch) MCP 一起装，路由阶梯完整生效；不装也能用（退化为内置 + 浏览器）。

## 定制

skill 里没有任何密钥和个人信息。建议你改两处让它更像自己的：

1. **分流表**：把你宿主里装的专门 skill 写进「优先级 1」的说明里（文末附录有作者的示例格式）
2. **报备话术**：切换报备那句话的格式可以按自己习惯改

---

# English 英文说明

A **search-routing skill** for AI agents. It does not search anything itself — it decides where each search should go: built-in web search, a hosted search API, or a real browser. One-line principle: **use the free ones first, route special content to dedicated tools, obey explicit requests, and always report when a non-default channel was used.**

## What problem it solves

Once your agent can search, three problems show up immediately:

1. Built-in web search has free quotas that run out mid-task
2. Login-walled sites and hard anti-bot pages can't be searched by any API — you need a browser
3. Fact-check style requests ("is this true?", "sources conflict") need multi-angle cross-checking, not a single query

This skill encodes all of it as routing rules. It degrades gracefully: without the [polysearch MCP](https://github.com/mayigululu-hash/polysearch), the ladder falls back to "built-in → browser"; install the MCP and the full five-tier ladder lights up.

## The ladder (cheapest first)

```
User names a channel ("search with doubao/deepseek/the browser") → obey
Dedicated platform skill/adapter (X, Xiaohongshu, WeChat articles...) → hand off
Built-in WebSearch → default for ordinary searches
polysearch MCP → built-in down / quota spent, or user names Doubao/DeepSeek
Browser automation → login walls, anti-bot, page interaction; last resort
```

Two designs worth noting:

- **Cross-check mode**: triggered by fact-check wording. Splits the question into pro/contra queries, then reports agreement / conflict / credibility-lean — never a final verdict on the user's behalf
- **Channel reporting**: whenever a non-default channel was used, the reply ends with one fixed line, e.g. "This ran via polysearch (built-in search quota exhausted)", so the user always knows the bill

## Install

Copy the folder into your host's skills directory:

```bash
git clone https://github.com/mayigululu-hash/polysearch-skill.git
mkdir -p ~/.claude/skills
cp -r polysearch-skill ~/.claude/skills/polysearch
```

Claude Code: `~/.claude/skills/` or `.claude/skills/` in the project. ZCode: `~/.zcode/skills/`. Other hosts: follow their convention. Works standalone; pairs with the [polysearch MCP](https://github.com/mayigululu-hash/polysearch) for the full ladder.

## Customize

No keys, no personal data inside. Two things you should edit to make it yours: the platform hand-off list (priority 1), and the channel-report phrasing. See the appendix in SKILL.md for the author's own hand-off table as a template.

## License

MIT. Built by 花叔 (Huashu).
