# 快装 Kuaizhuang

> 多语言开发环境一键配置工具 · 一条命令装好 Python / Java / Node / Go / Rust 的整套开发环境

还在为了配一个环境折腾一整天？快装帮你把「下载 → 装版本 → 配镜像 → 设环境变量 → 建虚拟环境」这一整套流程压成一条命令。

---

## 为什么做这个

现有的环境配置工具大多只解决 Python 深度学习那一块，而且必须装客户端、按周付费、代码闭源。快装想解决的是更普遍的问题：

- **不止 Python** — Java / Maven / Node / Go / Rust 一起管
- **不用手敲一堆命令** — `kz install java@21 maven node@22`
- **自动匹配版本** — 读项目文件（pom.xml / package.json / pyproject.toml / go.mod）推荐兼容版本
- **国内镜像加速** — 默认走国内源，下载不用等
- **环境隔离** — 每个项目独立，互不干扰

## 快速开始

`bash
kz detect                  # 探测电脑上已经装了什么
kz list java               # 看有哪些可装版本
kz install java@21 maven   # 一键装好一套 Java 开发环境
kz setup                   # 按当前项目自动配环境
kz remove java@21          # 卸载干净
`

## 支持的语言

| 语言 / 工具 | 状态 | 说明 |
| --- | --- | --- |
| JDK (Temurin OpenJDK) | 规划中 | 支持 8 / 11 / 17 / 21 / 25 |
| Maven / Gradle | 规划中 | 自动配 settings.xml 与国内仓库 |
| Python | 规划中 | 内置 uv，虚拟环境一键建 |
| Node.js | 规划中 | 多版本切换，自带 npm 镜像配置 |
| Go | 规划中 | 自动配 GOPROXY |
| Rust | 规划中 | rustup 工具链管理 |

## 技术栈

- **TypeScript + Node.js** — CLI 核心，跨平台
- **Recipe 驱动** — 每个语言一份 YAML 配方，加新语言只要写一个文件
- **Tauri 2** — 第二阶段做图形界面，体积小、启动快

技术选型理由见 [docs/tech-stack.md](docs/tech-stack.md)。

## 路线图

- [x] 立项，确定技术栈
- [ ] v0.1 — 环境探测 + JDK / Maven 一键安装
- [ ] v0.2 — Python（uv）与 Node 支持
- [ ] v0.3 — 按项目自动配环境、国内镜像一键切换
- [ ] v0.4 — Go / Rust 支持
- [ ] v1.0 — Tauri 图形界面

## 开发

`bash
npm install
npm run dev -- detect
`

## 说明

本项目为独立开发，与 codetou.com（抠头助手）无任何关联。
