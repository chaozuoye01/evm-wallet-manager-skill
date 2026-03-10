# EVM Wallet Manager · OpenClaw Skill

> 适用于 [OpenClaw](https://openclaw.ai) 的 EVM 多地址资产管理 Skill，安装后即可通过对话部署和管理「我的钱包管理」应用。

![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-blue)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 🤖 这是什么

这是一个 OpenClaw Skill 文件，安装到 OpenClaw 后，你可以直接用自然语言：

- 「帮我部署钱包管理工具」
- 「查一下这几个地址的资产」
- 「OKX API 报错了怎么办」
- 「给我新增 Scroll 链」

OpenClaw 会自动调用本 Skill，完成部署、排错、扩展等操作。

---

## 📦 配套项目

本 Skill 对应的应用源码：

👉 [my-wallet-manager](https://github.com/你的用户名/my-wallet-manager) — EVM 多地址资产管理 Web 应用

---

## 🚀 安装方法

### 方式一：下载 .skill 文件安装

1. 在 [Releases](../../releases) 页面下载最新的 `evm-wallet-manager.skill`
2. 将文件放入 OpenClaw 的 skills 目录：
```
~/.openclaw/skills/
```
3. 重启 OpenClaw，Skill 自动加载

### 方式二：克隆仓库

```bash
git clone https://github.com/你的用户名/evm-wallet-manager-skill.git
cd evm-wallet-manager-skill

# 复制到 OpenClaw skills 目录
cp -r evm-wallet-manager ~/.openclaw/skills/
```

---

## 💬 触发关键词

安装后，对 OpenClaw 说以下任意内容都会触发本 Skill：

| 说法 | 触发动作 |
|------|---------|
| 帮我查钱包资产 | 指导查询流程 |
| 批量导入地址 | 提供导入格式和步骤 |
| OKX API 报错 | 排查签名/限速问题 |
| 给钱包工具新增一条链 | 提供扩展代码 |
| 部署钱包管理应用 | 从零部署指引 |

---

## 📁 文件结构

```
evm-wallet-manager/
├── SKILL.md                  # Skill 主文件（触发规则 + 使用说明）
└── references/
    ├── app_code.md           # 完整后端源码（app.py）
    ├── frontend_code.md      # 完整前端源码（index.html）
    └── extensions.md        # 扩展方案（CSV导出/新增链/定时刷新）
```

---

## 📄 License

MIT License — 自由使用、修改、分发。
