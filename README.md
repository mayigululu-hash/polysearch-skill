# polysearch-skill

**给 AI agent 的搜索路由。让它知道什么活走内置搜索，什么活走搜索服务，什么活必须上浏览器。**

当前版本 v0.1.3 ｜ [English](#english-英文说明) ｜ License MIT

## 为什么需要它

给 agent 接上搜索只是第一步，真用起来你会发现三件事：

1. 内置搜索有免费额度，用着用着就耗尽，agent 当场抓瞎
2. 登录态内容、强反爬站点，任何搜索 API 都够不着，得上真浏览器
3. 核实类请求（「这是真的吗」「信源打架」）需要多方交叉验证，搜一次就下结论必出错

三种活混在一起，agent 要么瞎选通道烧钱，要么卡死在一条断掉的通道上。

polysearch-skill 把选路逻辑写成规则。搜索请求进来，先选通道再执行，核心原则一句话：**免费的先用，特殊的走专门通道，点名了照办，换了通道要说一声。**

## 三个特点

- **省钱序五级阶梯**。用户点名 → 平台专门 skill → 内置 WebSearch → polysearch MCP → 浏览器自动化。免费的先用，贵的兜底
- **交叉核查模式**。「核实、求证、是真的吗、信源打架」触发。polysearch MCP 与浏览器两条异质通道各自跑正反两方查询，再交叉比对，输出一致点、冲突点、可信度倾向三段式，不替用户下最终结论；只查个概念的轻量核实不进此模式，按普通搜索走
- **切换报备**。凡走了非默认通道，回答末尾固定报一句「本次走的 polysearch（内置搜索额度已用完）」，用户心里有账

## 30 秒安装

```bash
git clone https://github.com/mayigululu-hash/polysearch-skill.git
mkdir -p ~/.claude/skills
cp -r polysearch-skill ~/.claude/skills/polysearch
```

- Claude Code：`~/.claude/skills/` 或项目内 `.claude/skills/`
- ZCode：`~/.zcode/skills/`
- 其他宿主：按各自的 skills 目录约定放置

## 路由阶梯

```
用户点名（用豆包搜/用deepseek搜/浏览器搜）→ 照办
平台专门 skill/适配器（X、小红书、公众号……）→ 交给它们
内置 WebSearch → 普通搜索默认
polysearch MCP → 内置挂了/配额尽，或点名豆包/DeepSeek，或交叉核查
浏览器自动化 → 登录态、强反爬、页面交互，或交叉核查，最后兜底
```

每一级怎么判定，SKILL.md 里有完整决策树，agent 拿到就知道怎么走。

## 配套 MCP：把阶梯的第四级点亮

skill 只管调度，搜索服务本身要靠 [polysearch](https://github.com/mayigululu-hash/polysearch) MCP 来接。它把豆包搜索（每月 500 次免费）和 DeepSeek Search（$0.75/千次）聚合成一个工具，配额尽或故障自动切换。**推荐搭配使用**，阶梯完整生效；不装 MCP 也能用，路由自动退化为「内置 → 浏览器」。

## 定制

skill 里没有任何密钥和个人信息。建议改两处让它更像自己的：

1. **分流表**。把你宿主里装的专门 skill 写进「优先级 1」的说明里，SKILL.md 文末附了作者的示例格式
2. **报备话术**。切换报备那句话的格式，按自己的习惯改

---

# English 英文说明

A **search-routing skill** for AI agents. It teaches the agent where each search should go: the host's built-in web search, a hosted search API, or a real browser. One-line principle: **use the free ones first, route special content to dedicated tools, obey explicit requests, and always report when a non-default channel was used.**

## Why

Hooking up search is step one. In real use you will hit three things:

- Built-in web search quotas run out mid-task, leaving the agent stranded
- Login-walled sites and hard anti-bot pages are beyond any search API — they need a real browser
- Fact-check requests ("is this true?", "sources conflict") need multi-angle cross-checking; one search and a verdict is a mistake

Mix all three and the agent either burns money on the wrong channel or stalls on a dead one. This skill encodes the routing as rules: every search picks a channel before it runs.

## Highlights

- **Cheapest-first, five-tier ladder**: user's explicit pick → dedicated platform skills → built-in WebSearch → polysearch MCP → browser automation
- **Cross-check mode**: triggered by fact-check wording; runs pro/contra queries through two heterogeneous channels (polysearch MCP + browser automation) and cross-compares them, reporting agreement / conflict / credibility-lean — never a final verdict on the user's behalf. Light single-fact lookups stay on the normal path
- **Channel reporting**: every non-default channel gets one fixed line at the end of the reply, so the user always knows the bill

## Quick install

```bash
git clone https://github.com/mayigululu-hash/polysearch-skill.git
mkdir -p ~/.claude/skills
cp -r polysearch-skill ~/.claude/skills/polysearch
```

Claude Code: `~/.claude/skills/` or `.claude/skills/` in the project. ZCode: `~/.zcode/skills/`. Other hosts: follow their convention.

## The ladder

```
User names a channel → obey
Dedicated platform skill/adapter → hand off
Built-in WebSearch → default for ordinary searches
polysearch MCP → built-in down / quota spent, user names Doubao/DeepSeek, or cross-check mode
Browser automation → login walls, anti-bot, page interaction, cross-check mode; last resort
```

The full decision tree ships in SKILL.md.

## Companion MCP

The skill routes; the [polysearch MCP](https://github.com/mayigululu-hash/polysearch) connects — Doubao Search (500 free calls/month) + DeepSeek Search ($0.75/1k) in one tool with automatic failover. Recommended together; without the MCP the ladder degrades gracefully to "built-in → browser".

## Customize

No keys, no personal data inside. Two edits make it yours: the platform hand-off list (priority 1 — the author's own table ships as an appendix in SKILL.md) and the channel-report phrasing.

## License

MIT. Built by 花叔 (Huashu).
