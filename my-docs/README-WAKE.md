# jcode — AI Coding Agent Harness

| 维度 | 内容 |
|------|------|
| **用途** | 下一代编码 Agent 框架，多 session 工作流、无限可定制、高性能 |
| **应用场景** | AI 辅助编程（CLI + TUI），多 Agent 协同编程，MCP 工具集成 |
| **标签** | Rust, AI-Coding-Agent, TUI, MCP, Multi-Model, Swarm |
| **技术栈** | Rust (edition 2024), tokio, ratatui, MCP |
| **内部版本** | V1-20260723 |
| **关联** | 上游: `1jehuang/jcode` (MIT, 10.8k★)<br>Fork: `weikejia123/jcode`<br>Gitea: `localhost:3000/dzsoft/jcode.git`<br>官网: https://jcode.sh |

---

## 目录结构

```
projects/coder-agent/jcode/
├── .cargo/          # Cargo 配置
├── .jcode/          # jcode 自身配置
├── assets/          # 静态资源
├── changelog/       # 变更日志
├── crates/          # Rust crate 工作区（核心代码）
├── docs/            # 文档
├── examples/        # 示例
├── ios/             # iOS 支持
├── packaging/       # 打包脚本
├── scripts/         # 构建/安装脚本
├── src/             # 主入口
├── telemetry-worker/ # 遥测 worker
├── tests/           # 测试
└── my-docs/         # (本项目的本地文档)
```

## 分支策略

| 分支 | 来源 | 说明 |
|------|------|------|
| `master` | `upstream/master` | 跟踪上游，纯净无本地修改 |
| `wkj-dev` | `upstream/master` | 二开分支（当前） |

## 远程仓库

| 远程 | URL | 用途 |
|------|-----|------|
| `origin` | `github.com/weikejia123/jcode.git` | 个人 GitHub fork |
| `upstream` | `github.com/1jehuang/jcode.git` | 上游官方仓库 |
| `gitea` | `localhost:3000/dzsoft/jcode.git` | 内网备份 |

## Fork 策略

Add-only（默认）。新增 my-docs/ 等本地文档，不修改上游代码文件。
