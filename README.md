# 千机Agent

一个 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)（DSH）的 Agent 预设（preset）。

装上之后，新建任务时可以在模式选择器里选「千机」，就会以这个人设工作。

## 千机是什么

通用全栈开发助理。核心特点：

- **项目自适应**：进入任何项目先读 `package.json` / `pom.xml` / `AGENTS.md` / `.cursor/rules/` 等，摸清技术栈和约定再动手，不臆造规则
- **需求先确认**：拆解验收标准 → 出开发方案 → 用户确认后才写代码
- **固定交付物**：代码 + 交叉验证 + 代码流程文档 + 测试文档，四件套齐了才算完成
- **交叉验证机制**：核心链路改动会输出「五段式审查指令」，让你换一个模型独立审查。指令自带完整背景，明确要求审查方不沿用历史结论
- **越用越懂你**：发现你的纠正、偏好、反复出现的约定，会沉淀成长期记忆
- **硬约束**：新功能不影响原有功能；数据库只读验证；git 只 commit 不自动 push

完整人设见 [`cordis.patch.yml`](./cordis.patch.yml) 里的 `persona.prefix`。

## 目录结构

```
.
├── package.json      # 包声明，dsh.bundle.patch 指向下面的 patch 文件
├── cordis.patch.yml  # 预设声明：id / 名称 / 描述 / persona 提示词
└── README.md
```

**没有代码。** 这是一个纯声明式的 bundle，只由 YAML 组成，依赖的 `@deepseek-ai/dsh-agent-preset` 和 `@deepseek-ai/dsh-persona` 都随 DSH 发行版自带，不需要额外安装任何 npm 包。

> 正因为没有代码，它在任何装了 DSH 的机器上表现完全一致，不存在构建失败、依赖冲突这类问题。

## 安装

### 前置条件

- 已安装 DSH（桌面版或 CLI 均可）
- 能访问 GitHub

### 步骤

在**创造模式**（Creator mode）下开一个新任务，跟 Agent 说：

```
把这个仓库安装成我的 agent preset：
https://github.com/<你的用户名>/qianji-agent
```

或者直接在 DSH 的插件管理里操作，安装目标填：

```
git+https://github.com/<你的用户名>/qianji-agent.git
```

装好后，**新建任务**，在输入框底部的模式选择条里就能看到「千机」。

> 注意：预设是**在任务开始时选定**的，已经开始的对话不能切换。要选「千机」，必须开新任务。

## 跨机器迁移

这个仓库就是为「换电脑还能用」设计的。新机器上的完整流程：

```bash
# 1. 装好 DSH 并登录
# 2. 在创造模式下安装
#    安装目标：git+https://github.com/<你的用户名>/qianji-agent.git
# 3. 新建任务，选择「千机」模式
```

**不需要**复制任何本地文件，**不需要**手动改配置。这也是为什么本仓库刻意只放声明、不放代码。

## 修改人设

直接编辑 `cordis.patch.yml` 里 `persona.prefix` 的那段文本，提交推送。然后：

- **本机已装**：重新安装一次（`install_bundle` 指向仓库地址）
- **只想本机临时试**：改本地已安装的那份，重启 DSH

`persona` 支持三个字段：

| 字段 | 默认值 | 作用 |
|---|---|---|
| `prefix` | 必填 | 人设正文，渲染进系统提示词的 persona 段 |
| `suffix` | 空 | 追加在 persona 之后 |
| `complete` | `false` | 设为 `true` 则**只用** prefix 作为系统提示词，忽略 DSH 默认的其余部分 |

## 设计取舍

**为什么不用 `link:` 本地路径安装？**

本地开发时用 `link:` 很方便，但它是**绝对路径**（例如 `link:D:/harness-work-test/qianji-preset`），换机器就失效。git 安装是这个包唯一正确的分发方式。

**为什么 `private: true`？**

它是私有的、不需要发布到 npm。git 安装**不要求** `private: false`。

**为什么 `@local/` 前缀？**

作用域名只影响本地安装时的目录位置（`node_modules/@local/...`），对 git 安装没有影响。保持原样是为了和你本机已验证可用的安装保持一致。

## License

MIT
