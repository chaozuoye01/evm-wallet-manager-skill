---
name: evm-wallet-manager
description: |
  部署和维护 EVM 多地址资产管理系统。当用户提到「钱包管理」「查资产」「多链余额」「EVM 地址管理」「批量查钱包」「OKX API 查余额」「资产分布」等需求时，立刻使用本 skill。

  本 skill 能做到：
  - 从零部署一套本地 Web 应用（Flask + OKX Wallet API）
  - 帮用户添加/删除/批量导入 EVM 钱包地址
  - 查询指定地址在 ETH/ARB/BASE/zkSync/Linea 等 12 条链上的所有资产
  - 调整 API 限速配置（请求间隔、重试次数）
  - 排查部署报错、API 签名失败、网络代理等问题
  - 修改或扩展功能（增加链、修改 UI、新增导出功能等）
---

# EVM 多地址资产管理系统 Skill

## 概览

这是一个本地运行的 Web 应用，基于 OKX Wallet API 构建，专门解决加密用户管理多个 EVM 钱包资产分散的问题。

**核心能力：**
- 批量管理钱包地址（支持备注、链选择）
- 查询 12 条 EVM 链的所有 Token 余额和 USD 价值
- SSE 流式实时进度推送（批量查询透明可见）
- 限速管理面板（防止 API 429 错误，支持自动重试）

**技术栈：** Python + Flask + OKX Wallet API v5

---

## 快速部署

### 前提条件
- Python 3.8+（Windows 用 `py`，Mac/Linux 用 `python3`）
- 网络能访问 `web3.okx.com`
- OKX Build 平台 API Key（见下方获取方式）

### 步骤

```bash
# 1. 安装依赖
pip install flask requests
# Windows 如果 pip 不可用：
py -m pip install flask requests

# 2. 进入项目目录
cd evm-wallet-manager

# 3. 启动
python3 app.py
# Windows:
py app.py

# 4. 浏览器打开
# http://localhost:5000
```

> ⚠️ 如果用了 VPN/代理，浏览器访问 `127.0.0.1:5000` 可能超时，改用 `localhost:5000`；或在 Windows 代理设置「例外」中加入 `127.0.0.1;localhost`

---

## 获取 OKX API Key

1. 访问 https://web3.okx.com/build
2. 注册/登录 → 创建项目
3. 获取以下四项凭证：
   - **API Key**
   - **Secret Key**
   - **Passphrase**
   - **Project ID**
4. 在应用右上角「⚙ API 配置」填入并保存

---

## 使用流程

```
配置 API Key → 批量导入地址 → 点击「查询所有钱包」→ 查看资产总览
```

### 批量导入格式（右上角「📥 批量导入」）

```
0xAbCd...1234
0xAbCd...1234, 主钱包
0xAbCd...1234 撸毛钱包01
```

支持三种格式混用，自动识别、自动去重。

---

## 限速管理

点击顶部导航右侧的 **绿色徽章（req/s）** 进入限速面板：

| 参数 | 默认值 | 范围 | 说明 |
|------|--------|------|------|
| 请求间隔 | 1.0s | 0.2s ~ 5s | OKX 限制 1req/s，建议 ≥ 1s |
| 最大重试 | 3次 | 1 ~ 10次 | 遇到 429 自动退避重试 |

配置自动保存到 `rate_config.json`，重启后生效。

---

## 支持的 EVM 链

| Chain ID | 链名 | Chain ID | 链名 |
|----------|------|----------|------|
| 1 | Ethereum | 8453 | Base |
| 56 | BNB Chain | 324 | zkSync Era |
| 137 | Polygon | 59144 | Linea |
| 42161 | Arbitrum | 250 | Fantom |
| 10 | Optimism | 25 | Cronos |
| 43114 | Avalanche | 100 | Gnosis |

---

## 项目文件结构

```
evm-wallet-manager/
├── app.py              # Flask 后端 + OKX API + 限速管理器
├── templates/
│   └── index.html      # 前端 UI（纯 HTML/CSS/JS）
├── requirements.txt    # 依赖：flask, requests
├── wallets.json        # 钱包地址存储（自动生成）
├── api_config.json     # API 凭证（自动生成，勿上传 GitHub）
└── rate_config.json    # 限速配置（自动生成）
```

详细代码见 `references/app_code.md` 和 `references/frontend_code.md`

---

## 常见问题排查

### ❌ `pip` 不是内部命令
```bash
py -m pip install flask requests
```

### ❌ `IndentationError`
`app.py` 末尾缩进混乱。确保最后几行格式如下（`app.run` 前面 4 个空格）：
```python
if __name__ == "__main__":
    print("🚀 EVM 钱包管理系统启动中...")
    app.run(debug=False, host="0.0.0.0", port=5000)
```

### ❌ 浏览器超时 `ERR_CONNECTION_TIMED_OUT`
VPN 代理拦截了本地请求。三种解法：
1. 暂时关闭 VPN 再访问
2. Windows → 运行 `inetcpl.cpl` → 连接 → 局域网设置 → 高级 → 例外填入 `127.0.0.1;localhost`
3. `app.py` 末尾改为 `host="0.0.0.0"`，浏览器访问 `localhost:5000`

### ❌ API 返回 `50011` 或 `429`
触发限速。进入限速面板，将请求间隔调大（建议 1.5s ~ 2s）。

### ❌ API 返回签名错误
检查 API 配置中的四项凭证是否复制完整，注意 Passphrase 区分大小写。

---

## 扩展开发

### 新增链
在 `app.py` 的 `EVM_CHAINS` 字典中添加：
```python
"链ID": {"name": "链名称", "symbol": "原生币", "color": "#颜色"},
```

### 修改最低显示金额
`app.py` 第 `process_assets` 函数中修改：
```python
if usd_value < 0.01:  # 改成你想要的阈值
```

### 导出 CSV 功能
参考 `references/extensions.md` 中的扩展方案。

---

## 完整源码

- 后端逻辑：查看 `references/app_code.md`
- 前端 UI：查看 `references/frontend_code.md`
