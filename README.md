# RA2039-MAYA3.0

> 本机个人 AI 助手平台 · 四层本地架构 · 拟开源项目
>
> **声明：本仓库当前仅发布功能与架构描述（Docs-first），核心源码尚未上传，正在整理开源化。**

MAYA 3.0 是一个面向单用户（本地 `owner_id=local`）的个人 AI 助手平台。它不是一个多用户云产品，而是跑在你自己电脑上的一整套「Agent 大脑 + 会话管家 + 语音对话 + 外设感知」系统，支持 Web / Qt / CLI 多种入口同时接入同一会话。

---

## ✨ 核心功能

### 1. L1 · Agent 大脑（推理 / 工具 / 记忆）
- **LLM 推理运行时**：多后端适配（默认 Cursor SDK / 可切 OpenAI），支持流式输出与长任务超时管理。
- **工具调用（MCP 体系）**：内置 File / Terminal / Git / Docker / SQLite / Weather / Time 等 MCP 工具，可扩展自定义工具服务。
- **八域状态机（State）**：Agent 会话级状态分八个域管理，可快照 / 恢复，供多端共享与观测。
- **权限分级（Level / Mode）**：命令执行前权限裁决，支持提升 / 受限 / 需确认等模式，危险操作需用户确认。
- **情景记忆（Episodic Memory）**：基于 SQLite FTS5，按 session / workspace / global 三级作用域写入与检索，敏感字段脱敏，回合后自动沉淀。

### 2. L2 · 会话管家 CM（Conversation Manager）
- **会话生命周期 + 对话状态机**：Idle → Wake → Listening → Thinking → Speaking → Listening。
- **打断 / 播报控制**：说话中强插（Barge-in）、TTS 播报取消与续听。
- **三端统一接入**：一套 CM API 同时服务 Web / Qt5 / CLI，会话与持久化共享（SQLite）。
- **三端 UI 资产**：
  - Web 主控台：Vue 3 + Three.js CSS3D 会话画廊（TABLE/SPHERE/HELIX/GRID 切换）、Aura 风格状态 HUD、会话卡片与时间线。
  - Stage 主交互面（1920×1080）：Agent 的数字人 / HCI / MCP 观测一体的沉浸主页。
  - Aug Lab：UI 效果实验室（3D 场景、布局调试、BOM 工作台选型）。

### 3. L3 · 语音层 Voice
- **云端 ASR / TTS**：默认阿里云，支持本地 Fake 后端用于测试与无声卡环境。
- **本地 VAD / 唤醒 / AEC**：WebRTC 静音检测、能量唤醒、回音消除（透传）。
- **多轮语音会话**：唤醒词 → 拾音 → 识别 → 触发 CM turn → TTS 播报 → 续听。

### 4. L4 · 外设与硬件
- 音频采集 / 播放能力抽象（PortAudio/ALSA 与 Fake 后端切换），设备枚举与切换。
- 设计上与 ROS 2 Humble 兼容可共存，但 MAYA 主链路不依赖 ROS。

### 5. 数据感知与扩展（RA2039 子体系）
- **BioSense 生物体征网关**：接收 Galaxy Watch（WebSocket 遥测）与 iPhone Health 批量上报，SQLite 持久化；身体记忆可写入 Agent 情景记忆，供后续会话「记得你的身体状态」。
- **IoT 空间全息（Three.js 3D）**：在 Stage / Lab 中展示智能家居 3D 场景与设备交互（Home Assistant 生态规划中）。
- **Time Stone / 极坐标跳环**：极简时钟交互组件实验。

---

## 🏗 系统架构

```
浏览器 (Stage / Aug Lab / CM Web)        -- 只连 CM :8100，同源免 CORS
        │
        ▼
┌── CM :8100 ── 会话状态机 · 三端 UI 托管 · 同源代理桥 ──┐
│   /api/agent-bridge/*     → Agent   :8000              │
│   /api/voice-bridge/*     → Voice   :8200              │
│   /api/biosense-bridge/*  → BioSense :8210             │
└───────┬───────────────────────┬────────────────────────┘
        │                        │
   Agent :8000               Voice :8200
   推理 / MCP / State / 记忆      ASR/TTS · VAD/唤醒 · 多轮
   EventBus (Redis)           (轮询 CM outbound 队列)
        │
   BioSense :8210 ── Watch WS / iPhone Health 上报
```

- **端口设计**：`8000` Agent · `8100` CM（浏览器唯一入口）· `8200` Voice · `8210` BioSense 网关 · `6379` Redis（Agent 事件总线）。
- **层间契约**：HTTP / WS / 事件名冻结为文档，各层单向依赖（Agent ← CM ← Voice，数据由 BioSense 网关侧拉）。
- **技术栈**：Python 3.10+ · FastAPI · Uvicorn · pydantic-settings · SQLite (FTS5) · Redis · Three.js r17x · Vue 3（CDN）· Qt5（可选）。

---

## 🔓 开源声明（Open-Source Statement）

本项目 **MAYA 3.0 / RA2039-MAYA3.0** 计划以 **Apache-2.0** 许可证开源。

- 现阶段仓库处于 **Docs-first** 过渡期：先公开「功能 + 架构」以征集意见、打磨 README 与社区路线。
- 核心源码（`agent/`、`cm/`、`voice/` 等）正在做 **脱敏与去私密化**（移除本地密钥、用户数据、第三方素材）后分批公开。
- 我们希望开放：**代码可读、可审计、可自托管**，鼓励二次开发与商用（Apache-2.0 允许商用与修改，需保留版权与许可声明）。
- 仓库欢迎 **Issue / Feature Request / 讨论**，当前不接受外部 PR 合入敏感代码。

> ⚠️ 若你 fork / 二次分发本仓库，请遵守 `LICENSE` 条款；仓库内出现的第三方素材（视频 / 字体 / 模型等）版权归原作者，**未包含**在本仓库中。

---

## 🧭 规划中的开源路线

| 阶段 | 内容 |
|------|------|
| v0.1（当前） | 公开 README 功能 / 架构描述 + Apache-2.0 LICENSE |
| v0.2 | 公开核心契约文档（层间 HTTP/事件契约）与目录白皮书 |
| v0.3 | 分批公开可运行源码（先 Agent 运行时，再 CM / Voice） |
| v1.0 | 完善安装脚本、示例配置、无密钥一键起服务 |

---

## 📌 说明

- 环境基线：Ubuntu 22.04 · Python 3.10+ · ROS2 Humble 可共存（非必需）。
- 本仓库为个人开发项目发布页；详细功能演进见各阶段 Release Notes。

**License**：[Apache-2.0](./LICENSE) © RA2039
