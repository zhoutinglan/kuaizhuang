# 快装技术选型说明

这份文档解释快装为什么选这些技术，以及在什么情况下会换方案。

## 一句话结论

CLI 用 **TypeScript + Node.js**，安装逻辑用 **YAML 配方（recipe）** 描述，图形界面放在第二阶段用 **Tauri 2** 做。

## 为什么核心用 TypeScript + Node.js

**跨平台不用改代码。** 快装要同时跑 Windows / macOS / Linux。Node 在这三个系统上都能直接跑，路径、环境变量、进程调用各有一套成熟库，不用为每个系统写一份实现。

**工具链最省事。** 只要装了 Node 就能 `npm i -g kuaizhuang` 直接使用，不需要用户额外装编译器。对新手来说这一步越短越好，装完 JDK 还要先装 Go 才能装 JDK 就本末倒置了。

**开发速度快。** 环境配置工具的本质是「下载 → 解压 → 写配置 → 改 PATH」，大量工作是文件与进程操作，属于 IO 密集型，不是计算密集型。这种活用 JS/TS 写效率最高，性能完全够用。

**生态齐。** 解压（tar / unzip）、进度条、交互式选择、彩色输出、配置解析这些都有现成库，不用自己造。

## 为什么不用别的语言

| 方案 | 优点 | 为什么暂时不选 |
| --- | --- | --- |
| Go | 单文件二进制，分发漂亮 | 用户没装 Go 时需要先装 Go 才能跑快装，鸡生蛋问题；开发时要交叉编译多平台 |
| Rust | 性能最好，二进制最小 | 开发速度慢，环境配置这类 IO 活发挥不出优势，学习成本高 |
| Python | 生态丰富，写起来快 | 用户装环境配置工具，本身就是为了装 Python，用 Python 写会形成依赖循环；且打包体积大 |

**关于「单文件二进制」**：如果后面确实需要，可以用 `bun build --compile` 或 Node SEA 把 TypeScript 版本打成一个可执行文件，不用换语言。

## 为什么用 YAML 配方驱动

每种语言、每个工具的安装步骤都写成一份 YAML 文件，放在 `src/recipes/` 下：

```yaml
# src/recipes/java.yaml
name: java
aliases: [jdk, java]
versions: "8, 11, 17, 21, 25"
steps:
  - action: fetch
    url: https://mirror.example.com/temurin/{{version}}/jdk-{{os}}-{{arch}}.tar.gz
    sha256: {{checksum}}
  - action: extract
    to: ~/.kuaizhuang/java/{{version}}
  - action: env
    JAVA_HOME: ~/.kuaizhuang/java/{{version}}
    PATH_APPEND: $JAVA_HOME/bin
```

这样做的好处：

- **加新语言不用改核心代码**，写一个 YAML 文件就行，普通人也能贡献
- **失败可回滚**，每一步都是声明式的，能明确知道卡在哪一步
- **国内镜像可换**，镜像地址写在配方里，切换源不用改逻辑

## 为什么 GUI 用 Tauri 2 而不是 Electron

| 对比项 | Tauri 2 | Electron |
| --- | --- | --- |
| 安装包体积 | 约 5-10 MB | 约 80-150 MB |
| 内存占用 | 低（用系统 WebView） | 高（自带 Chromium） |
| 复用 CLI 逻辑 | 直接调用 Rust 侧命令 | 可以直接复用 Node 代码 |
| 跨平台 | Windows / macOS / Linux | 同左 |

快装是一个「装环境」的工具，用户本来就对安装包体积敏感，一个 100 MB 的 Electron 客户端去帮用户省环境配置时间，说服力不足。Tauri 体积小、启动快，更符合工具定位。

GUI 放在 v1.0 才做，前期专心把 CLI 打磨好。

## 目录结构规划

```
kuaizhuang/
  src/
    index.ts          # 命令入口，解析 detect / install / list / setup / remove
    detect.ts         # 扫描系统，识别已装的语言与版本
    recipe.ts         # 加载并执行 YAML 配方
    mirror.ts         # 国内镜像地址管理
    recipes/          # 各语言的安装配方（YAML）
      java.yaml
      maven.yaml
      python.yaml
      node.yaml
      go.yaml
      rust.yaml
  docs/
  package.json
```

## 待定问题

- 是否需要独立的环境隔离（每个项目一套环境），还是只做全局多版本切换
- 下载校验方式：只校验 sha256，还是同时校验 GPG 签名
- 是否要支持离线安装包
全部
