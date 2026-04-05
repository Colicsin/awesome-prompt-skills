# awesome-prompt-skills

一套为 Claude Code 设计的 Skills 集合，涵盖科研实验管理和学术论文引用。

## 包含的 Skills

- **exp-*** — 实验工作流（需求定义 → 方案设计 → 方案评审 → 规划拆解 → 执行记录 → 复盘归档）
- **paper-citation** — 学术论文智能引用检索与 Tier 分级推荐

## 快速开始

```bash
# 克隆仓库
git clone https://github.com/Rain0530/awesome-prompt-skills.git

# 复制 skills 到本地
cp -r awesome-prompt-skills/skills/* ~/.claude/skills/
```

安装完成后，在 Claude Code 中输入 `/exp-help` 或 `/paper-citation` 验证是否生效。

## 详细文档

- [实验工作流 Skills](./skills/README.md)
- [论文引用 Skill](./skills/paper-citation/SKILL.md)
