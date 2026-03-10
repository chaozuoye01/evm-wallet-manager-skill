# 扩展开发参考

## 1. 导出 CSV 功能

在 `app.py` 中新增路由：

```python
import csv
import io
from flask import make_response

@app.route("/api/export-csv")
def export_csv():
    wallets = load_wallets()
    config = load_api_config()
    output = io.StringIO()
    writer = csv.writer(output)
    writer.writerow(["备注", "地址", "链", "Token", "余额", "USD价值"])
    
    for wallet in wallets:
        chains = wallet.get("chains", list(EVM_CHAINS.keys()))
        try:
            assets = fetch_balances(wallet["address"], chains, config)
            result = process_assets(assets)
            for chain_id, chain_data in result["by_chain"].items():
                for token in chain_data["tokens"]:
                    writer.writerow([
                        wallet["label"], wallet["address"],
                        chain_data["chain_name"], token["symbol"],
                        token["balance"], token["usd_display"]
                    ])
        except:
            pass
    
    resp = make_response(output.getvalue())
    resp.headers["Content-Disposition"] = "attachment; filename=wallets_export.csv"
    resp.headers["Content-type"] = "text/csv; charset=utf-8-sig"
    return resp
```

前端添加按钮：
```html
<button onclick="window.open('/api/export-csv')">📊 导出 CSV</button>
```

---

## 2. 新增链

在 `app.py` 的 `EVM_CHAINS` 字典添加：

```python
"534352": {"name": "Scroll",    "symbol": "ETH", "color": "#FFDBB0"},
"1101":   {"name": "Polygon zkEVM", "symbol": "ETH", "color": "#7B3FE4"},
"5000":   {"name": "Mantle",    "symbol": "MNT", "color": "#1A1A1A"},
```

OKX 支持的完整链 ID 列表参考：https://web3.okx.com/build/docs/waas/okx-waas-supported-networks

---

## 3. 定时自动刷新

使用 APScheduler 定时刷新所有钱包（需 `pip install apscheduler`）：

```python
from apscheduler.schedulers.background import BackgroundScheduler

scheduler = BackgroundScheduler()

def auto_refresh():
    config = load_api_config()
    if not config.get("api_key"):
        return
    wallets = load_wallets()
    for wallet in wallets:
        try:
            chains = wallet.get("chains", list(EVM_CHAINS.keys()))
            fetch_balances(wallet["address"], chains, config)
        except:
            pass

# 每 30 分钟刷新一次
scheduler.add_job(auto_refresh, 'interval', minutes=30)
scheduler.start()
```

---

## 4. 钱包分组标签

在 `wallets.json` 数据结构中增加 `tags` 字段，实现分组筛选：

```json
{
  "address": "0x...",
  "label": "主钱包",
  "tags": ["主力", "长持"],
  "chains": ["1", "42161"]
}
```

---

## 5. 总价值历史记录

每次查询完保存快照到 `history.json`，实现资产变化趋势图（前端用 Chart.js）：

```python
def save_snapshot(total_usd):
    history = []
    if os.path.exists("history.json"):
        with open("history.json") as f:
            history = json.load(f)
    history.append({"time": datetime.now().isoformat(), "total": total_usd})
    history = history[-100:]  # 保留最近100条
    with open("history.json", "w") as f:
        json.dump(history, f)
```
