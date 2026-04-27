# Spec-Kit Commands

Claude Code 全局指令集——一套结构化的需求→设计→开发→验收工作流。

## 指令总览

| 指令 | 阶段 | 用途 |
|------|------|------|
| `/prepare` | Step 0 | 评估 brief 范围，决定是否拆分 |
| `/specify` | Step 1 | 从 brief 生成结构化 spec.md |
| `/clarify` | Step 1.5 | 提取待澄清问题，生成 clarify.md |
| `/design` | Step 2 | 从 design/ 产物抽象出 design.md 索引 |
| `/plan` | Step 2.5 | 基于 spec + clarify + design 生成技术方案 plan.md |
| `/tasks` | Step 3 | 把 plan 拆解为 tasks.md + analyze.md |
| `/analyze` | Step 3.5 | 检查 tasks 对照 spec/plan 的机械性遗漏 |
| `/finish` | Step 4 | 收口本轮循环，生成 finish.md 作为下一轮基线 |

## 工作流

```
prepare → specify → clarify → design → plan → tasks → analyze → finish
                                                                  ↓
                                                          finish.md 作为下一轮 specify 的输入
```

每个指令接受一个参数：feature 目录路径。例：

```bash
/prepare specs/s010-anti-scrape
/specify specs/s010-anti-scrape
/clarify specs/s010-anti-scrape
```

## 项目适配

指令本身是**项目无关**的。项目特有的规则通过以下文件注入：

| 文件 | 位置 | 用途 |
|------|------|------|
| `constitution.md` | 项目根目录 | 项目宪法，指令运行时自动读取（如存在） |
| `CLAUDE.md` | 项目根目录 | Claude Code 项目级指令（架构提醒等） |
| `brief.md` | feature 目录 | 每个 feature 的需求描述 |
| `finish.md` | feature 目录 | 上一轮的完成报告，作为下一轮基线 |

## 安装

### 首次安装

```bash
# 1. Clone 本仓库
git clone git@github.com:lwang47utas/spec-kit-commands.git ~/spec-kit-commands

# 2. 创建软链接到 Claude Code 全局 commands 目录
ln -s ~/spec-kit-commands ~/.claude/commands

# 3. 验证
ls ~/.claude/commands/
# 应该看到: analyze.md clarify.md design.md finish.md plan.md prepare.md specify.md tasks.md
```

### 更新指令

```bash
cd ~/spec-kit-commands && git pull
```

所有项目立刻生效，无需重启 Claude Code。

### 卸载

```bash
rm ~/.claude/commands    # 只删软链接，不删仓库
```

## 团队协作

1. 每位同事按「首次安装」操作一次
2. 指令更新后，发个消息让大家 `git pull`
3. 各项目的 `constitution.md` / `CLAUDE.md` 继续放在各自 repo，不受影响
4. 如果某个项目需要**覆盖**全局指令，在该项目的 `.claude/commands/` 下放同名文件即可（项目级优先于全局）

## 注意事项

- 指令运行时会自动检测项目根目录的 `constitution.md`，不同项目的宪法互不影响
- 全局 commands 目录 `~/.claude/commands/` 是 symlink，实际内容在 `~/spec-kit-commands/`
- 不要直接修改 `~/.claude/commands/` 下的文件——改 `~/spec-kit-commands/` 然后 commit + push
- 如果 `~/.claude/commands/` 已有其他全局指令，不要用 symlink 覆盖整个目录，改用逐文件 symlink：
  ```bash
  mkdir -p ~/.claude/commands
  for f in ~/spec-kit-commands/*.md; do
    ln -sf "$f" ~/.claude/commands/
  done
  ```
