# 输出方式选项

本文档介绍 `gater-weekly-report` 支持的多种输出方式。

---

## 方式A：Google Sheets（需 GCP Service Account）

适合：有 GCP Service Account JSON Key 的用户

### 配置
1. 获取 GCP Service Account JSON Key
2. 设置环境变量或修改脚本路径：
   ```bash
   export GCP_KEY_PATH="/path/to/your/service-account-key.json"
   ```

### 使用
```bash
python3 scripts/fetch_defi_data.py
python3 scripts/write_sheet.py
```

Sheet 会在 `https://docs.google.com/spreadsheets/d/1Iaus-JyVbvdD6CENbG66nOp7vl59pyJL-jvtJwHJL1Q` 自动创建新 Tab

---

## 方式B：Notion Database

适合：有 Notion API Key 且建立了 Database 的用户

### 配置
1. 创建 Notion Integration 并获取 API Key
2. 建立 Database，包含以下属性：
   - 日期（title）
   - 全链TVL（number）
   - 全链稳定币（number）
   - 备注（text）
3. 修改 `scripts/write_notion.py`（需自行创建）中的 `NOTION_TOKEN` 和 `DATABASE_ID`

### 使用
```bash
python3 scripts/fetch_defi_data.py
python3 scripts/write_notion.py
```

---

## 方式C：直接 Markdown 输出

适合：无任何 API Key 的用户，直接运行脚本后 AI 将数据以 Markdown 格式输出到对话

### 使用
```bash
python3 scripts/fetch_defi_data.py
# 输出 /tmp/defi_data.json
```

然后告诉 AI："请读取 `/tmp/defi_data.json` 并按照 SKILL.md 的分析框架输出周报"

---

## 方式D：CSV 文件

适合：需要本地存档或用 Excel 进一步分析的用户

`fetch_defi_data.py` 已输出 `/tmp/defi_data.json`，可自行转换为 CSV：
```bash
python3 -c "import json,csv; d=json.load(open('/tmp/defi_data.json')); ..."
```
