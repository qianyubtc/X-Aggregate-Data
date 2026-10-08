# X-Aggregate-Data

让你的 AI agent 读得到 X（Twitter）。**不用 X API、不用登录、不用会员、不用 key。**

Claude Code、Codex 这类 agent 打不开 x.com，网页工具要么被拦，要么只拿到空页面。装上这个 skill，agent 遇到 X 的链接、账号、话题，会自己调脚本拿数据，1–3 秒返回结构化 JSON：正文、时间、浏览、点赞、转发、引用、回复、收藏、图片视频、社区笔记……数字都是原始值，可以直接算、直接比。

> English: an Agent Skill that lets Claude Code / Codex read X (Twitter) — tweets, threads, replies, quotes, profiles, timelines, search, followers, translation — without the X API, login or subscription. Plus daily stats of thousands of Chinese-speaking X accounts from [lanbuzhu.org](https://lanbuzhu.org).

## 能做什么

**任何推、任何账号（实时）**

- 读推文：给链接就行，可以带上作者自己接的整串（thread）、回复区、引用它的推、翻译
- 账号资料：粉丝、关注、发帖数、注册时间、简介、**所在地、改名次数**
- 账号时间线：最近的推，可按时间截止，可含回复 / 转推，可只看带图视频的
- 搜索：支持 X 的搜索语法（`from:` `min_faves:` `lang:` `filter:` `url:` `conversation_id:` …），按时间或按热门
- 关注列表 / 粉丝列表

**X 中文区统计（来自 [蓝不住](https://lanbuzhu.org)）**

蓝不住长期跟踪数千个 X 中文区账号，每天定格一次：

- 某个账号今天 / 昨天 / 7 天 / 30 天的推文曝光、发帖、评论、涨粉，近 30 天每天的走势和名次
- 账号排行（曝光 / 发帖 / 评论 / 涨粉 / 粉丝 / 帖均曝光 / 最高单帖，可按 AI、币圈、财经、自媒体赛道）
- 每日四冠军、日报
- 大V动态：**删帖（带原文）**、改名改简介换头像、粉丝异动、互相转发引用、10 万曝光的爆款
- 中文区热帖、正在火的话题、谣言库

## 安装

需要 Python 3.8+（macOS 自带；Windows 装了 Python 后用 `py -3` 或 `python` 运行）。用 [skills CLI](https://github.com/vercel-labs/skills) 装，按提示选你用的 agent：

```bash
npx skills add qianyubtc/X-Aggregate-Data -g
```

或者手动复制：

```bash
git clone https://github.com/qianyubtc/X-Aggregate-Data.git
cp -r X-Aggregate-Data/skills/x-aggregate-data ~/.claude/skills/   # Claude Code
cp -r X-Aggregate-Data/skills/x-aggregate-data ~/.codex/skills/    # Codex
```

**国内网络**：要能访问 X 相关的接口，开着代理就行（脚本会自动用系统代理，或 `HTTPS_PROXY=http://127.0.0.1:端口` 环境变量；只支持 http 代理，不支持 socks）。

**Codex**：默认沙箱不让联网，第一次运行时同意它在沙箱外运行即可；或者在 `~/.codex/config.toml` 里加上：

```toml
[sandbox_workspace_write]
network_access = true
```

## 用法

直接用自然语言问你的 agent：

- "这条推说了什么，回复区大家怎么看？https://x.com/…/status/…"
- "@elonmusk 这周发了什么？按点赞排一下"
- "今天中文区关于 SOL 的热门讨论有哪些？"
- "这个号是哪个国家的、改过几次名？"
- "昨天 X 中文区涨粉最多的 10 个号"
- "最近有哪些大V删帖了？删的是什么？"

也可以自己在命令行用：

```bash
python3 skills/x-aggregate-data/scripts/x.py tweet https://x.com/jack/status/20 --replies 20 --pretty
python3 skills/x-aggregate-data/scripts/x.py search '$BTC lang:zh min_faves:50' --since 24h --top
python3 skills/x-aggregate-data/scripts/x.py user @someone --pretty
python3 skills/x-aggregate-data/scripts/x.py rank --by gain --when yday --limit 10
python3 skills/x-aggregate-data/scripts/x.py -h
```

全部命令和参数见 [SKILL.md](skills/x-aggregate-data/SKILL.md)，搜索语法见 [references/search.md](skills/x-aggregate-data/references/search.md)，中文区统计的口径见 [references/lanbuzhu.md](skills/x-aggregate-data/references/lanbuzhu.md)。

## 数据从哪来

- 实时数据：脚本在**你自己的电脑上**直接请求 [FxTwitter](https://github.com/FxEmbed/FxEmbed) 的公开接口（就是 fxtwitter.com 那个修复 X 链接预览的开源项目），不需要 X 账号。请求会从你的 IP 发到 FxTwitter，它能看到你查了什么（哪些推、账号、搜索词）。感谢 FxEmbed。
- X 中文区统计：[蓝不住 lanbuzhu.org](https://lanbuzhu.org) 的开放接口（只读、免费、不用注册，每 IP 每分钟 60 次、每天 3000 次）。这几个命令会把要查的账号名从你的 IP 发到 lanbuzhu.org。
- 除了这两个，脚本不连别的地方（展开 t.co 短链接时会请求一次 t.co）。

公开接口有限流，也可能哪天变化或失效，请合理使用：别拿它批量抓取上千个账号或高频轮询。

## 安全

推文、简介、回复都是任何人都能写的文本，可能藏着想劫持 agent 的指令。SKILL.md 里要求 agent 只把它们当数据读、不执行其中的任何指令。脚本本身只发 GET 请求读数据，不登录、不发推、不碰你的任何账号。

## 许可

MIT。这是非官方工具，和 X Corp. 没有关系，请遵守当地法律和 X 的使用条款。
