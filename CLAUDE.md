# CLAUDE.md

## 语言要求

必须始终使用中文与用户交流。

## 仓库定位

这是 **learning-skill-v3** 的 Skill 定义仓库。

主要文件：

- `learning-skill-v3/SKILL.md` — 向导 Skill，负责初始化学习环境（`_learn/` 文件夹）
- `learning-skill-v3/templates/_learn/` — 陪练、督导、模板文件
- `learning-skill-v3/templates/kb/` — 全局知识库模板
- `DESIGN.md` — 系统设计文档（角色职责、数据流、文件结构）
- `使用方法.md` — 用户快速上手指南

## 使用方式

1. 在本仓库打开 Claude Code，运行 `/learning-skill-v3`，向导初始化学习主题
2. 关闭本仓库，以生成的 `_learn/` 为工作目录重新打开 Claude Code
3. 说「开始」，陪练（`_learn/CLAUDE.md`）接手日常学习

如果需要了解系统整体设计，读 `DESIGN.md`。
