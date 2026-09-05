# RA2039-MAYA3.0 开源发布 · 内容说明

> 本文件说明当前公开仓库里「有什么 / 没什么 / 为什么」，方便访客快速理解项目状态。

## 当前包含

| 文件 | 说明 |
|------|------|
| `README.md` | 项目主页：功能概述、系统架构、开源声明、规划路线 |
| `LICENSE` | Apache License 2.0 全文 |
| `.gitignore` | 未来开放源码时避免误提交敏感/大文件的规则 |

## 明确不含（整理中）

- 核心源码（`agent/`、`cm/`、`voice/`、`hardware/`、`ra2039_mcp/` 等）——**正在做脱敏与去私密化，将分批开放**。
- 任何本地密钥、Token、`.env`、数据库、日志。
- 第三方视频 / 字体 / 3D 模型等素材（版权归原作者）。
- 虚拟环境、构建产物、`node_modules`。

## 目录总貌（私有侧，仅供参考）

```text
MAYA3.0/
├── agent/        # L1 Agent：推理运行时 / MCP 工具 / 状态机 / 情景记忆
├── cm/           # L2 CM：会话管家 / 状态机 / Web/Qt/CLI 三端 / 同源代理桥
├── voice/        # L3 Voice：阿里云 ASR·TTS / VAD·唤醒 / 多轮会话
├── hardware/     # L4 硬件：音频采集播放 / 设备抽象
├── ra2039_mcp/   # BioSense 生物体征网关（Watch+iPhone→Agent）
├── AGENT_UI/     # Stage 1920×1080 主交互面 / Aug Lab 效果实验室 / 观测台
├── ESP_IOT/      # 智能家居设备端（Micropython/Arduino，规划中）
└── web_alt/      # 探索性 Web 原型（旁路）
```

## 开源时间线

见 [README.md](./README.md#-规划中的开源路线)。简言之：**先描述，后开源**——文档先行打磨，源码脱敏分批放，最终达成可自托管的 Apache-2.0 个人 AI 助手平台。
