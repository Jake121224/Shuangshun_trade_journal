# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

单文件期货交易复盘工具（双顺交易体系）。所有 CSS/JS 内嵌在 `trade-journal.html` 中，数据存 Supabase，页面托管 Netlify。

## 技术架构

- **前端**：单文件 HTML（无框架，无构建工具），支持亮/暗主题跟随系统
- **数据**：Supabase PostgreSQL（`trades` 表，JSONB + 主键 id + created_at）+ localStorage 本地缓存兜底
- **存储**：Supabase Storage（`screenshots` bucket，公开访问）
- **托管**：Netlify → `zumingtradelog.netlify.app`
- **版本管理**：Git → GitHub `Jake121224/Shuangshun_trade_journal`

## Supabase 配置

```
URL:  https://tawovxvjcywlkfawoshg.supabase.co
Key:  sb_publishable_pYcauYpjjqWU6oVXIMhxiw_uWVeNFvf（公开，可放在前端）
```

数据库表 `trades`：id(主键) / trade(JSONB，存完整交易对象) / created_at。RLS 策略全开（`allow_all`，个人项目）。

Storage bucket `screenshots`：公开读写（`public_screenshots` 策略）。

## 部署更新流程

HTML 文件改完后：`git add` → `git commit` → `git push`，然后到 [app.netlify.com/drop](https://app.netlify.com/drop) 拖 `trade-journal.html` 覆盖。

## 关键设计决策

- **离线优先**：页面秒开读 localStorage → 后台从 Supabase 同步 → 合并。断网也能用。
- **双保险**：本地缓存 + 云端同步，网络恢复自动同步。
- **单文件**：不引入框架，方便快速迭代和部署。
- **截图**：粘贴/拖拽到表单，保存时上传 Supabase Storage，URL 存入 trade.screenshots[]。最多 9 张。
- **仓位计算**：`资金 × 风险% ÷ (止损点数 × 每点价值) = 建议手数`，向下取整。
- **盈亏 R**：`(出场价 - 进场价) ÷ |进场价 - 止损价|`，做空反向。
- **分批进出**：支持多笔进场/出场，自动算加权均价和合计盈亏。
- **SVG 图表**：手写 SVG（累计 R 曲线 + 每单柱状图），无外部图表库依赖。

## 用户信息

- 期货交易者，使用双顺交易体系
- 技术背景有限但愿意学习
- 项目知识体系文档：`知识体系.md`（面向初学者的完整解释）
