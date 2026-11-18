---
name: ai-content-index
description: 索引 mySkills 聚合仓库中不含 SKILL.md 的 AI 提示类内容：84 份品牌 / 站点 DESIGN.md 设计系统，以及 diagram-design 的 prompts 与 commands 提示文件。当需要某种视觉风格的设计语言与设计令牌、按某网站风格生成 UI，或需要现成的图表生成提示文本时使用。
license: MIT
---

# AI 内容索引

本 skill 不存放内容本身，只做**索引**：把 `mySkills` 仓库里没有 `SKILL.md`、但属于 AI 提示 / 设计参考的目录集中列出，方便按需定位与引用。下文路径均相对于 `mySkills` 仓库根目录。

## 内容集合

| 集合 | 路径 | 规模 | 用途 |
| --- | --- | --- | --- |
| 品牌设计系统 | `voltagent_awesome_design_md/design-md/<站点>/DESIGN.md` | 84 份 | Google Stitch 规范的纯文本设计系统，含色彩、字体、间距等令牌与设计规则 |
| 图表提示文件 | `cathrynlavery_diagram_design/prompts/` | 6 个 | 图表导入 / 导出 / 配置类的可复用提示文本 |
| 图表命令文件 | `cathrynlavery_diagram_design/commands/` | 7 个 | 上述提示的斜杠命令版本，额外含 `import-drawio.md` |

### 图表提示文件

| 文件 | 作用 |
| --- | --- |
| `prompts/import-mermaid.md` | 导入 Mermaid 图表 |
| `prompts/import-plantuml.md` | 导入 PlantUML 图表 |
| `prompts/import-excalidraw.md` | 导入 Excalidraw 图表 |
| `prompts/export-diagram.md` | 导出图表 |
| `prompts/profile.md` | 生成与管理样式配置（profile） |
| `prompts/doctor.md` | 环境与配置自检 |

## 用法

### 按站点风格生成 UI

1. 在 [`references/design-md-catalog.md`](references/design-md-catalog.md) 中按站点目录名找到目标（含设计名称与一句话描述）；
2. 读取 `voltagent_awesome_design_md/design-md/<站点>/DESIGN.md`；
3. 将其复制到目标项目根目录，或直接把内容交给 agent，再让 agent 依此设计系统生成页面。

DESIGN.md 是纯 Markdown，上游定位是「拷进项目后对 agent 说『按这个风格做页面』」，无需 Figma、JSON schema 或额外工具。

### 复用图表提示

读取对应的 `prompts/*.md` 作为提示模板；需要命令形式时改用 `commands/` 下的同名文件。

## 说明

- 只维护索引、不复制内容，上游更新后内容随子模块自动同步。
- 仓库位置变动无需改本文件，按上表相对路径在新根目录下查找即可。
- 内容与许可证由各上游仓库负责（均为 MIT）。
