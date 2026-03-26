---
name: gater-weekly-report
description: 加密行业周报自动化系统。执行时间：每周二北京时间15:00。产出：(1)每周10条深度资讯精选，(2)DefiLlama链上数据三张表+原因分析。触发场景：用户要求生成周报、查询链上数据、整理资讯摘要、追踪DeFi协议TVL变化。技术栈：Python + DefiLlama API + ChainFeeds RSS + PANews + Odaily。支持多种输出：Google Sheets / Notion / Markdown / 直接对话输出。
---

# Gater Weekly Report — 加密周报自动化

## 执行时间
每周二北京时间 15:00（UTC 07:00）

## 两部分产出

### 内容一：每周10条热点资讯
数据窗口：上周三 00:00 → 本周二 23:59

抓取来源（按优先级）：
- ChainFeeds RSS: `https://chainfeeds.substack.com/feed`
- PANews 深度: `https://www.panewslab.com/zh/in-depth`
- Odaily 深度: `https://www.odaily.news/zh-CN/deep`

选题标准见 `references/sample_output.md`

输出格式：标题 + 来源链接 + 核心机制拆解 + 3个要点 + 结构性结论

### 内容二：链上数据三张表
数据窗口：上上周一 00:00 → 上周日 23:59（完整自然周）

**表格一**：各链 DEX 周交易量（Ethereum/Solana/BSC/Base/Hyperliquid/Arbitrum/Polygon/Sui/Avalanche/Tron + Others + Total）

**表格二**：各链 DeFi TVL + 稳定币（动态筛选 TVL > $1B）

**表格三**：前100协议 7d TVL 涨幅 > 10%（按 parentProtocol 合并子协议）

## 输出方式（多种选择）

### 方式A：Google Sheets（推荐，有GCPKey时）
```bash
# 1. 抓链上数据
python3 scripts/fetch_defi_data.py

# 2. 写入 Google Sheet
python3 scripts/write_sheet.py
```
Sheet ID: `1Iaus-JyVbvdD6CENbG66nOp7vl59pyJL-jvtJwHJL1Q`
Tab 命名：`YYYY-MM-DD`（周一日期）
GCP Key 路径：环境变量 `GCP_KEY_PATH` 或修改 `scripts/write_sheet.py` 内的 `KEY_FILE`

### 方式B：Notion（无需GCP）
将数据导入 Notion Database，参见 `references/OUTPUT_OPTIONS.md`

### 方式C：直接输出（无需任何配置）
数据直接以 Markdown 格式输出到对话中，适合没有 API Key 的用户

## 运行方式

```bash
# 1. 抓链上数据
python3 scripts/fetch_defi_data.py

# 2. 抓资讯候选
python3 scripts/fetch_news.py
# 输出 /tmp/news_candidates.json，交由 AI 筛选10条
```

依赖：`pip install -r references/requirements.txt`

## 关键踩坑（必须遵守）

| 踩坑 | 错误做法 | 正确做法 |
|---|---|---|
| DEX 总量 | 各链求和漏 Others | 用全局 `/overview/dexs` 接口 |
| 周变化 | 用 `change_7dover7d`（滚动7天） | 用自然周时间戳汇总 |
| 稳定币总量 | 加表格各行（少 $13B） | 用 `/stablecoinchains` 所有链求和 |
| 协议数据 | 用原始子协议（重复入选） | 用 `parentProtocol` 合并 |
| 传销代币 | 有机含量混计 | 卖单/买单 > 3 且无官网 → 标注 |
| TVL 跳升 | 视为有机增长 | 单日 > 20% → 单笔大额，标注 |
| Bitcoin TVL | 当新增资金 | 全部 Babylon Protocol，反弹非新增 |
| 分析原因 | 引用历史事件 | 只用本周内（7天）事件 |
| DexScreener | 访问主页（403） | 用 `api.dexscreener.com/latest/dex/search` |

## 参考文件

- `references/sample_output.md` — 实际输出示例（含10条资讯 + 三张表数字）
- `references/ai_prompt.md` — 可直接复制给其他 AI 模型的执行 Prompt
- `references/model_adaptation.md` — Tier 1/2/3 模型适配说明
- `references/requirements.txt` — Python 依赖
