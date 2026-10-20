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

### 一行命令

在 **创造模式**（Creator mode）下开一个新任务，跟 Agent 说：

```
用这个地址安装 agent preset：
git+https://github.com/Red-empty-potato/qianji-agent.git
```

装好后，**新建任务**，在输入框底部的模式选择条里就能看到「千机」。

> **预设是在任务开始时选定的**，已经开始的对话不能切换。要选「千机」，必须开新任务。

### 设为默认（可选）

**设置 → Agent 预设 →「千机」卡片 → 设为新任务默认**。之后新建的任务默认就用它。

## 跨机器迁移

本仓库是公开的，**新机器上拉取不需要任何凭证**，不需要配 PAT，也不需要配 SSH key。

完整流程就三步：

```
1. 装好 DSH 并用 DeepSeek 账号登录
2. 创造模式下说：安装 git+https://github.com/Red-empty-potato/qianji-agent.git
3. 新建任务，选择「千机」
```

**不需要**复制任何本地文件，**不需要**手动改配置，**不需要**配 GitHub 凭证。

## 修改人设

编辑 `cordis.patch.yml` 里 `persona.prefix` 那段文本，然后提交推送：

```bash
git add -A
git commit -m "调整千机人设：xxx"
git push
```

改完需要在**每台机器上重新安装一次**（同一条安装命令）才能生效。因为 profile 的 `pnpm-lock.yaml` 把依赖锁定到了具体提交，`pnpm` 不会自动跟进新提交。

> 也可以把依赖写成 `git+https://...git#main` 让它跟踪分支，但那样每次安装都可能拉到未完成的状态，**建议保持显式更新**。

`persona` 支持以下字段：

| 字段 | 默认值 | 作用 |
|---|---|---|
| `prefix` | 必填 | 人设正文，渲染进系统提示词的 persona 段 |
| `suffix` | 空 | 追加在 persona 之后 |
| `complete` | `false` | 设为 `true` 则**只用** prefix 作为系统提示词，忽略 DSH 默认的其余部分 |

## 包结构说明

**为什么包放在仓库根目录？**

`git+https://...` 安装的是**仓库根**。如果 `package.json` 放在子目录（例如 `qianji-preset/`），安装时会找不到包定义而失败。

**为什么不用 `link:` 本地路径安装？**

`link:` 是绝对路径（例如 `link:D:/harness-work-test`），换机器立刻失效，而且源目录一旦移动或删除，安装就断了。git 安装把这个包复制进 `node_modules`，不依赖任何外部目录。

**为什么 `package.json` 里是 `private: true`？**

`private: true` 表示**不发布到 npm**，和 GitHub 仓库是否公开无关。git 安装不检查这个字段。

**为什么包名带 `@local/` 前缀？**

作用域名只决定本地安装目录（`node_modules/@local/...`），对 git 安装没有影响。

**为什么有 `.gitattributes`？**

Windows 上 `core.autocrlf=true` 会把 YAML 的 LF 改写成 CRLF 再提交，可能影响 YAML 解析。这里固定了 `eol=lf`，保证仓库内换行符统一。

**文件编码**

`cordis.patch.yml` 是 **UTF-8（无 BOM）**。用任何现代编辑器正常编辑保存即可。

> 注意：在中文 Windows 的 PowerShell 5.1 里用 `Get-Content` 预览这个文件，中文可能显示为乱码——那是控制台代码页（936/GBK）的渲染问题，**不代表文件本身是 GBK**。用 VS Code 打开是正常的。

## License

MIT
