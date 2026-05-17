# file-tidy

文件系统整理师 —— Claude Code skill。按 YAML 规则归类散乱文件、识别可删除的垃圾文件、输出直观的 before/after 树。支持干跑模式，独立使用或被 [weaver-evolve](https://github.com/2021291696/weaver-evolve) 全局整理时自动调用。

## 安装

```bash
mkdir -p ~/.claude/skills/file-tidy
cp SKILL.md ~/.claude/skills/file-tidy/
cp -r references ~/.claude/skills/file-tidy/
```

## 触发词

`整理文件` / `整理目录` / `文件归类` / `tidy` / `cleanup`

## 使用

### 独立使用

```
整理文件                     # 干跑模式，扫描当前目录
整理文件 --confirm           # 执行整理
整理目录 ./downloads         # 指定目录
```

### 项目规则

在项目根目录创建 `.tidy-rules.yaml` 自定义整理规则（格式见 [references/rules-schema.md](references/rules-schema.md)）。无此文件时使用内置默认规则。

### 被 weaver 调用

[weaver-自我迭代](https://github.com/2021291696/weaver-evolve) 在全局整理时会调用 file-tidy：
1. 干跑扫描 → 输出 before/after 树 → 嵌入 weaver 摘要
2. 用户确认 → weaver 调用 file-tidy 执行整理

## 关联项目

- [weaver-evolve](https://github.com/2021291696/weaver-evolve) — 跨项目全局整理 skill，在文件系统整理步骤调用本 skill
- [skill-bootstrapper](https://github.com/2021291696/skill-bootstrapper) — 本 skill 通过其快速通道创建
