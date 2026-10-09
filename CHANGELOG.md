# 更新日志 / Changelog

## 1.1.0

首个公开版本。First public release.

**实时数据 / Live data**

- `tweet`：读推文，可带作者的整串（`--thread`）、回复区（`--replies`，标注直接回复 / 楼中楼 / 作者本人）、引用（`--quotes`）、翻译（`--translate`）、作者完整资料（`--about`）；X 长文（Article）给全文。
- `user`：账号资料，含 X 判定的所在地（及是否准确）、改名次数。
- `tweets`：时间线，默认只算本人的原创和引用，可选回复、转推、只看媒体，`--since` 按时间截止。
- `search`：X 高级搜索语法，`--since` / `--until` 精确到秒，`--min-likes`，按点赞 / 浏览等排序。
- `following` / `followers`、`translate`。

**X 中文区统计 / Chinese X statistics**（lanbuzhu.org）

- `stats`、`rank`、`champions`、`daily`、`feed`、`hot`、`topics`、`rumors`、`rumor`。

**通用 / General**

- 只用 Python 标准库，Python 3.8+；macOS / Linux / Windows。
- 输出 JSON；列表带 `more`；出错也是 JSON，退出码 0 / 1 / 2 / 4 / 5。
- 自动使用系统代理和 `HTTPS_PROXY`；证书问题时退回 curl。
- 支持 x.com / twitter.com / 常见镜像域名、t.co 短链、全角符号；去掉文本中看不见的控制字符。
