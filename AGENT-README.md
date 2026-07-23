# AGENT-README: jcode

> **分析版本**: V1-20260724
> **分析框架**: [ANALYSIS-DIR-CODER-AGENT.md](../../../ANALYSIS-DIR-CODER-AGENT.md) (PV1-20260724)
> **项目路径**: `/Users/weikejia/CODE/my-agent-group/projects/coder-agent/jcode/`

---

## 1. 身份与定位

| 指标 | 内容 |
|------|------|
| **名称** | jcode |
| **Tagline** | "The next generation coding agent harness to raise the skill ceiling." |
| **定位语** | "Possibly the greatest coding agent ever built" — 追求性能极致 + 功能极致 |
| **设计哲学** | 内置一切: 记忆系统、Swarm、侧面板、自修改; 用 Rust 性能解决 Node.js Agent 的瓶颈 |
| **许可证** | MIT |
| **运行时语言** | Rust |
| **构建系统** | Cargo (crates workspace) |
| **上游** | `github:1jehuang/jcode` |
| **发布方式** | curl/sh 安装脚本, 预编译二进制 (macOS/Linux/Windows) |
| **社区** | Discord, 网站 jcode.sh, GitHub Release |

**核心权衡**: Rust 性能优势 vs TypeScript 生态丰富度; 全内置 vs 最小核心; 自修改能力 vs 稳定性

---

## 2. 核心架构

| 指标 | 内容 |
|------|------|
| **运行时** | Rust 原生 (edition 2024); Cargo workspace |
| **Crates 结构** | 30+ crates: `jcode-agent-runtime`, `jcode-app-core`, `jcode-tui`, `jcode-core`, `jcode-swarm-core`, `jcode-plan`, `jcode-storage`, `jcode-embedding`, `jcode-memory-types` 等 |
| **核心包** | `jcode-agent-runtime` (Agent 循环), `jcode-tui` (TUI), `jcode-swarm-core` (多 Agent), `jcode-embedding` (向量嵌入), `jcode-plan` (规划) |
| **Agent 主循环** | 提示词构建 → LLM 调用 (流式) → 工具执行 → 向量记忆嵌入 → 循环 |
| **系统提示词构建** | 包含项目上下文 (CLAUDE.md 等) + 工具定义 + 记忆嵌入结果 |
| **状态管理** | `jcode-app-core` + `jcode-session-types` |
| **运行时架构** | 持久 server + 多 client attach; 原生支持多会话共享 server 进程 |

**架构亮点**: 全 Rust 零 GC 性能; 内存系统作为一等公民 (每轮嵌入 + 语义查询); Server-Client 架构原生支持多会话缩放

---

## 3. Provider 与模型支持

| 指标 | 内容 |
|------|------|
| **原生 Provider 数量** | 15+ (Native + OpenAI 兼容) |
| **OAuth 订阅** | Claude (OAuth), OpenAI/ChatGPT/Codex, GitHub Copilot, Google Gemini, Azure OpenAI, Alibaba Cloud Coding Plan |
| **API Key 直连** | Fireworks, MiniMax, 以及其他 OpenAI 兼容端点 |
| **自定义 Provider** | `jcode login --provider openai-compatible` + 配置 TOML 文件 (`~/.jcode/config.toml`) |
| **本地模型** | Ollama, LM Studio (都是内置 profile); 本地 vLLM 端点 |
| **Provider 特定优化** | `extra_body` 注入 (如 NVIDIA NIM thinking), `JCODE_STREAM_IDLE_TIMEOUT_SECS`, per-model `context_window` |
| **多账户切换** | `/account` 命令快速切换不同订阅账号 |

**Provider 丰富度**: ★★★★★ (内置 Native + OAuth + OpenAI 兼容 + 本地, 脚本化配置完善)

---

## 4. 工具系统

| 指标 | 内容 |
|------|------|
| **内置工具** | 30+ (来自 `xai-grok-tools` 代码移植 + 自研工具) |
| **工具分类** | 文件操作, Shell 执行, Agent 系统, 搜索 (agent-grep 增强), 浏览器控制, 记忆工具, MCP, 等 |
| **工具注册机制** | 编译时注册 + 运行时动态加载 |
| **MCP 支持** | 完整 MCP 客户端; 兼容 Claude Code MCP 配置 (`.mcp.json`, `~/.claude.json`, `.claude/mcp.json`) |
| **工具权限模型** | 逐次确认 / 自动; 三种权限模式 (fallback: 客户端 > 配置 > 环境变量) |
| **Agent-Grep** | 自研增强 grep: 返回文件结构 (函数列表/位移), 自适应截断未读内容 |
| **第三方工具** | MCP Server (stdio/HTTP-SSE, 暂不支持 HTTP); Skills 系统; 可通过 MCP 连接任意工具 |

**工具生态成熟度**: ★★★★☆ (丰富的内置工具 + MCP 兼容, agent-grep 独特创新)

---

## 5. 用户界面与交互

| 指标 | 内容 |
|------|------|
| **TUI 技术栈** | 自研 Rust TUI — 1000+ fps 渲染; 自研 mermaid 渲染库 (1800x faster); 自研终端 Handterm (native scroll API) |
| **交互模式** | 交互式 TUI / Headless (`jcode run`) / Server mode (`jcode serve`) / Client mode (`jcode connect`) |
| **侧面板** | 实时文件查看 / 差异对比 / Mermaid 图表; 负空间 widget 系统 |
| **编辑器功能** | Shift+Enter 排队发送 (KV cache 安全); 标准编辑; `!command` |
| **快捷键系统** | Alt+C 居中切换; 自定义绑定 |
| **主题系统** | 自定义主题 |
| **跨 Agent 会话恢复** | 可恢复 Claude Code / Codex / OpenCode / pi 的会话文件 |
| **Dictation** | `jcode dictate` (外部 STT 命令, 无内置) |

**TUI 亮点**: 性能领先 (14ms 首次帧); 侧面板 + widget 系统独有; mermaid 内联渲染独有; 但部分终端功能需自研终端才能完全发挥

---

## 6. 会话管理

| 指标 | 内容 |
|------|------|
| **存储格式** | 待确认 (内部格式) |
| **存储位置** | `~/.jcode/` |
| **分支能力** | Server-Client 架构原生多会话; `build.ts` 支持版本回退 |
| **Fork/Clone** | `--resume` 回忆式会话名恢复; 跨 agent 会话恢复 (claude/codex/opencode/pi) |
| **Compaction** | 上下文压缩 (自动); 缓存冷启动警告 (Claude 5 分钟缓存超时) |
| **Session Search** | 基于嵌入的会话间 RAG 搜索 (记忆系统的一部分) |

**会话管理成熟度**: ★★★★☆ (Server-Client 多会话, 跨 Agent 恢复独特, 嵌入搜索)

---

## 7. 定制化与生态

| 指标 | 内容 |
|------|------|
| **Skills** | 嵌入匹配自动激活 (同记忆系统); `/skill:name` 手动调用 |
| **Prompt Templates** | 待确认具体支持 |
| **Extensions/Plugins** | 无传统插件系统 — 替代为"自修改能力" (Self-Dev) |
| **Themes** | 支持 |
| **包管理系统** | 无传统包管理; 通过 MCP 连接外部工具 |
| **自修改能力** | ✅ **核心差异化** — Self-Dev 模式: Agent 修改自身 Rust 源码 → `cargo build` → 热重载 → 继续 |

**生态成熟度**: ★★★★☆ (Self-Dev 模式独特, Skills + MCP 丰富; 但无传统扩展/包管理系统)

---

## 8. 记忆与上下文

| 指标 | 内容 |
|------|------|
| **长期记忆** | ✅ **核心功能**: 每轮嵌入为语义向量; 余弦相似度查询记忆图谱; 自动提取/存储/合并 (ambient mode) |
| **记忆架构** | 每个 turn 嵌入 → 图数据库 → 余弦检索 → 自动注入或 sideagent 验证注入 |
| **触发提取** | 语义漂移检测 / K 轮后 / 会话结束时 |
| **自动合并** | 定期 ambient 模式整理: 重组/检查陈旧/冲突 |
| **项目上下文** | CLAUDE.md / AGENTS.md; 层级查找 |
| **会话间复用** | 记忆图谱自动跨会话召回; 显式记忆工具 (agent 主动搜索/存储) |
| **上下文窗口管理** | 自适应工具输出截断; 缓存冷启动警告 |

**记忆能力**: ★★★★★ (行业内最完善的 Agent 记忆系统之一 — 向量嵌入 + 图谱 + 自动提取 + sideagent 验证)

---

## 9. 差异化功能

| 功能 | 支持情况 | 说明 |
|------|---------|------|
| **Swarm 多 Agent** | ✅ 原生 | Server 自动管理协作; 文件修改冲突通知; DM/广播/Repo 频道; Agent 自主 spawn 队友 |
| **目标驱动工作流** | ⚠️ 基本 | 通过 swarm 编排实现; 无独立 Goal 引擎 |
| **Computer Use** | ❌ 无 | 无内置 |
| **浏览器自动化** | ✅ Firefox | 内置 `browser` 工具: 10+ actions (open/click/type/screenshot/eval/scroll) |
| **语音模式** | ⚠️ 外部 | `jcode dictate` 支持外部 STT 命令 |
| **远程控制** | ⚠️ Server 模式 | `jcode serve` + `jcode connect` (LAN) |
| **制品托管** | ❌ 无 | 无 Artifact 系统 |
| **监控** | ❌ 无 | 无内置 |
| **性能对比表** | ✅ 自维护 | README 中有与其他所有 Agent 的详细性能对比数据 |
| **iOS 应用** | 🚧 开发中 | 原生 iOS App + Tailscale 远程环境控制 |

**差异化定位**: 性能之王 + 记忆之王 + Swarm; 用 Rust 实现其他 Agent 无法企及的启动速度和内存效率

---

## 10. 性能

| 指标 | 数据 | 备注 |
|------|------|------|
| **启动耗时 (首次帧)** | ~14.0 ms (Range: 10.1–19.3 ms) | **所有对比 Agent 中最快** |
| **启动耗时 (首次输入)** | ~48.7 ms (Range: 30.3–62.7 ms) | **最快** |
| **内存 (1 会话, 无嵌入)** | ~27.8 MB PSS | **最低 (无嵌入模式)** |
| **内存 (1 会话, 有嵌入)** | ~167.1 MB PSS | 嵌入模型占用 ~139 MB |
| **内存 (10 会话)** | ~260.8 MB PSS (增量 ~10.4 MB/会话) | **多会话缩放极佳** |
| **构建方式** | Cargo 增量 ~1 min / 目标 ~5-20s | 持续优化中 |
| **二进制体积** | 单二进制 (捆绑静态链接) | — |

**性能评级**: ★★★★★ (所有指标碾压同级, 最接近"立即响应"的 TUI Agent)

---

## 11. 安全

| 指标 | 内容 |
|------|------|
| **权限模型** | 逐次确认 / 自动; 权限模式透传; 无 root/sandbox 环境自动 bypass |
| **沙箱支持** | 依赖外部容器化 (无内置) |
| **供应链安全** | Cargo.lock; Rust 天生内存安全; 但无特别供应链安全措施 |
| **配置安全** | 密钥存储于 app config 目录; 支持 `--api-key-stdin` 安全输入 |

**安全评级**: ★★★☆☆ (标准 Rust 安全, 无特别强化)

---

## 12. 开发与社区

| 指标 | 内容 |
|------|------|
| **测试框架** | Cargo test (Rust 原生) |
| **测试策略** | 按 crate 测试 (`cargo test -p <crate>`); clippy + rustfmt |
| **CI/CD** | GitHub Actions (需进一步确认) |
| **社区模式** | 开放 — MIT 许可, 活跃 Discord |
| **代码质量** | Rust edition 2024; clippy.toml; rustfmt; 严格 lint |
| **发布工程** | GitHub Release + curl/sh 安装脚本; SemVer |

**社区活跃度**: ★★★★☆ (新兴但高质量的 Rust Agent, 性能数据自我公开)

---

## 版本演进

### V1-20260724 — 初始分析
- **分析范围**: 基于 jcode README (详尽) + 源码结构的全面分析
- **数据来源**: README.md (2220+ 行), Cargo.toml (crate 列表), 公开发布数据
- **关键发现**: 性能 + 记忆 + Swarm 三核心优势; Self-Dev 是最独特的差异化能力
- **分析框架**: 12 维度, PV1

---

## 横向对比摘要

| 核心维度 | jcode |
|---------|:-----:|
| 运行时 | Rust (原生) |
| 哲学 | 性能极致 + 内置一切 |
| Provider 数 | 15+ |
| 内置工具类型 | 30+ |
| MCP | ✅ 完整 |
| 记忆系统 | ✅ ✅ 最完善 |
| Swarm | ✅ 原生 |
| 启动耗时 | ~14ms (最快) |
| 内存(1会话) | ~28 MB (最低) |
| 生态成熟度 | ★★★★☆ |
| 安全性 | ★★★☆☆ |
| 代码质量 | ★★★★★ |
