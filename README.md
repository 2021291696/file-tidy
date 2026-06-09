<!-- BADGE BAR -->
[![License](https://img.shields.io/badge/license-MIT-blue)](./LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-orange)](https://claude.ai)

# file-tidy

> **文件系统整理师。** 结构感知归位——帮跑错地方的文件回到该去的子目录。支持干跑模式和自定义 YAML 辅助配置。

[English](#english)

---

## 为什么需要 file-tidy？

- **文件跑错地方了。** 脚本放在了根目录、文档散落在 src/、配置文件到处流浪。
- **手动归类太费劲。** 几十个文件，每个都要判断"该去哪"。
- **别人不敢动你的目录结构。** 怕误删、怕放错。file-tidy 用干跑模式先展示再执行。

## 核心能力

- 🗂️ **结构感知** — 根据项目目录约定判断文件归属，不只是按扩展名
- 🔍 **干跑模式** — 先展示 before/after 树，确认后再执行
- 📋 **YAML 辅助** — 自定义归类规则，覆盖默认判断
- 🤝 **被 weaver-evolve 调用** — 全局整理时自动触发

## 快速开始

### 触发

| 你说 | 它做什么 |
|------|---------|
| `整理文件` / `归类文件` | 扫描并归位散乱文件 |
| `/file-tidy` | 同上 |

## 设计哲学

**不要破坏子项目边界。** 子项目内部的文件布局是该项目的设计决策。file-tidy 只整理根目录的散乱文件，不深入子项目重组——尊重边界是长期协作的基础。

**先展示，再执行。** 干跑模式不是"锦上添花"，是核心交互。用户看到移动计划、确认无误、然后执行——这不只是安全，是信任。

## 相关项目

| 项目 | 关系 |
|------|------|
| [weaver-evolve](https://github.com/2021291696/weaver-evolve) | 上游 — 全局整理时调用我 |
| [memory-keeper](https://github.com/2021291696/memory-keeper) | 兄弟 — 文件归位 vs 记忆归位 |

## License

MIT

---

## English

# file-tidy

> **Filesystem organizer.** Structure-aware file placement — moves misplaced files back to their proper directories. Supports dry-run mode and custom YAML rules.

### Design Philosophy

**Don't break subproject boundaries.** Internal file layouts are design decisions of those subprojects. file-tidy only organizes root-level strays — respecting boundaries is the foundation of long-term collaboration.

**Show first, then execute.** Dry-run mode isn't a nice-to-have — it's the core interaction. See the plan, confirm, then execute. This isn't just safety, it's trust.
