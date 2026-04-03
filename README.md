# Claude Skills · 实验工作流

一套用于科研实验管理的 Claude Code Skills，帮助你系统化地完成从想法到复盘的完整实验流程。

## 设计理念

- **AI 辅助推进，用户始终主导**：AI 不替你做决定，每个关键节点都需要你确认
- **多轮交互，而非填模板**：通过对话逐步澄清，不要求一次性提供完整信息
- **每阶段有明确的入场条件和退出条件**：不会不知道该做什么、该进哪一步

## 包含的 Skills

| Skill | 命令 | 用途 |
|-------|------|------|
| exp-help | `/exp-help` | 工作流导航，不知道从哪开始时用这个 |
| exp-start | `/exp-start` | 阶段1·需求定义，把模糊想法澄清为结构化需求 |
| exp-design | `/exp-design` | 阶段2·方案设计，逐项讨论设计实验方案 |
| exp-review | `/exp-review` | 阶段3·方案评审，逐条挑毛病查漏洞 |
| exp-plan | `/exp-plan` | 阶段4·规划拆解，拆节点建参数表 |
| exp-log | `/exp-log` | 阶段5·执行记录，记录结果或处理异常 |
| exp-retro | `/exp-retro` | 阶段6·复盘归档，分析结论生成复盘报告 |

## 安装方法

### 方法一：手动复制

将 `skills/` 目录下的各文件夹复制到你本地的 `~/.claude/skills/` 目录：

```bash
cp -r skills/exp-* ~/.claude/skills/
```

### 方法二：clone 后复制

```bash
git clone https://github.com/Rain0530/claude-skills.git
cp -r claude-skills/skills/exp-* ~/.claude/skills/
```

安装完成后，在 Claude Code 中输入 `/exp-help` 验证是否生效。

## 工作流概览

```
/exp-start   →   /exp-design   →   /exp-review   →   /exp-plan   →   /exp-log   →   /exp-retro
  需求定义         方案设计          方案评审          规划拆解        执行记录         复盘归档
```

每个阶段结束前需要用户确认，不会自动跳入下一阶段。

## 标准文件结构

每次实验建议维护以下文件：

```
实验名称/
├── 需求文档.md          ← exp-start 产出
├── 实验方案.md          ← exp-design / exp-review 产出
├── 实验手册.md          ← exp-plan 产出
├── 实验结果记录表.md    ← exp-log 实时填写
└── 复盘报告.md          ← exp-retro 产出
```

## 适用场景

- 机器学习 / 深度学习实验
- 地学 / 空间数据分析实验
- 任何需要系统化管理的科研实验

## License

MIT
