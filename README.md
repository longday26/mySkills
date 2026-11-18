# mySkills

个人 AI Agent Skills 聚合仓库：本身**不含 skill 源码**，用 **Git Submodule** 汇聚上游优秀 skills，再通过**目录链接**（Windows Junction / macOS 软链接）挂载到本地 SKILL 空间，实现统一拉取、统一更新、自动同步。

- 主仓库：<https://github.com/3417993939/mySkills.git>
- 许可证：[MIT](./LICENSE)
- 暂不引入的仓库：[一些不适合拉取项目的github项目.md](./一些不适合拉取项目的github项目.md)

## 子模块

| 目录 | 上游仓库 | skills | 说明 |
| --- | --- | --- | --- |
| `anthropics_skills` | anthropics/skills | 19 | Anthropic 官方：docx / pptx / pdf / xlsx / skill-creator 等 |
| `mattpocock_skills` | mattpocock/skills | 39 | 工程实践（TDD、code-review、diagnosing-bugs 等）、写作、效率 |
| `vercel_labs_agent_skills` | vercel-labs/agent-skills | 9 | React / Next.js 最佳实践、Vercel 部署、设计规范 |
| `gpui-kit` | longbridge/gpui-kit | 2 | GPUI Kit 桌面应用开发与设计规范 |
| `longbridge_nsh` | longbridge/nsh | 2 | nsh 框架开发与设计规范 |
| `googlechrome_modern_web_guidance` | GoogleChrome/modern-web-guidance | 2 | 现代 Web 指南、Chrome 扩展 |
| `agents365_ai_drawio_skill` | Agents365-ai/drawio-skill | 1 | Draw.io 绘图 |
| `cathrynlavery_diagram_design` | cathrynlavery/diagram-design | 1 | 架构图 / 流程图等图形设计 |
| `voltagent_awesome_design_md` | VoltAgent/awesome-design-md | — | 设计参考（167 份按品牌 / 站点整理的 Markdown），**不含 `SKILL.md`**，不参与链接 |

共 75 个可挂载 skill。判断标准：**目录内有 `SKILL.md` 即为一个 skill，目录名即 skill 名**；上游可能带二级分类（如 `mattpocock_skills/skills/engineering/<skill>`），扫描 2 层足够。

```text
mySkills/
├── .github/workflows/
│   ├── update-submodules.yml     # 每月 1 号自动更新子模块并提交
│   ├── add-submodule.yml         # 手动触发：按仓库地址新增子模块
│   └── tmp-task.yml              # 一次性修复脚本（历史遗留）
├── <子模块>/skills/[<分类>/]<skill>/SKILL.md
├── .gitmodules
└── README.md
```

## 克隆

```bash
git clone --recurse-submodules https://github.com/3417993939/mySkills.git
```

已克隆但子模块为空：`git submodule update --init --recursive`

## 更新

**自动**：`update-submodules.yml` 每月 1 号执行 `git submodule update --remote --merge`，有变化就提交 `chore: auto-update submodules [skip ci]`。本地执行 `git pull` 即可拿到最新子模块指针。

**手动**：

```bash
git pull
git submodule update --remote --merge --recursive
```

`--remote` 取上游最新提交（而非主仓库记录的固定提交），`--merge` 把更新合并进子模块的 `main` 分支，避免游离 HEAD。

个别子模块因网络原因拉取失败时，重复执行上述命令即可；也可只更新指定子模块：

```bash
git submodule update --remote --merge <子模块目录>
```

更新后子模块指针会变化（`git status` 显示 `modified: <子模块>`），通常由 CI 提交；需本地提交时只 add 实际变化的子模块：

```bash
git add <子模块目录>
git commit -m "chore: 更新子模块"
git push
```

**新增子模块**：优先用 Actions 的 `add-submodule`（填仓库地址与分支，自动生成 `owner_repo` 目录名并做重复检查）。本地等价写法：

```bash
git submodule add -b main https://github.com/<owner>/<repo>.git <本地目录名>
```

## 链接到 SKILL 空间

| 工具 | Windows | macOS / Linux |
| --- | --- | --- |
| CodeBuddy | `%USERPROFILE%\.codebuddy\skills` | `~/.codebuddy/skills` |
| Claude Code | `%USERPROFILE%\.claude\skills` | `~/.claude/skills` |

用链接而非复制，上游更新后 skill 自动同步。Windows 建 Junction 需开启「系统 → 开发者模式」或以管理员身份运行。

### Windows PowerShell

在 mySkills 仓库根目录执行：

```powershell
$repo   = (Get-Location).Path
$target = Join-Path $env:USERPROFILE ".codebuddy\skills"
New-Item -ItemType Directory -Force -Path $target | Out-Null

$roots = git -C $repo config -f .gitmodules --get-regexp '\.path$' |
  ForEach-Object { Join-Path $repo ((($_ -split '\s+')[1]) + "\skills") } |
  Where-Object { Test-Path $_ }

Get-ChildItem $roots -Directory -Recurse -Depth 2 |
  Where-Object { Test-Path (Join-Path $_.FullName "SKILL.md") } |
  ForEach-Object {
    $link = Join-Path $target $_.Name
    if (Test-Path $link) { "跳过（已存在）: $($_.Name)"; return }
    New-Item -ItemType Junction -Path $link -Target $_.FullName | Out-Null
    "已链接: $($_.Name) -> $($_.FullName)"
  }
```

### macOS / Linux

```bash
REPO="/path/to/mySkills"; TARGET="$HOME/.codebuddy/skills"
mkdir -p "$TARGET"
git -C "$REPO" config -f .gitmodules --get-regexp '\.path$' | awk '{print $2}' | while read -r p; do
  root="$REPO/$p/skills"; [ -d "$root" ] || continue
  find "$root" -maxdepth 2 -type d | while read -r d; do
    [ -f "$d/SKILL.md" ] || continue
    name="$(basename "$d")"
    if [ -e "$TARGET/$name" ]; then echo "跳过（已存在）: $name"
    else ln -s "$d" "$TARGET/$name"; echo "已链接: $name"; fi
  done
done
```

### 清理链接

> 只删除链接本身，命令都不要带递归。`Remove-Item -Recurse`、`rd /s`、`rm -rf` 以及资源管理器里直接删 Junction，都可能**穿透删除源目录里的真实文件**。

在 mySkills 仓库根目录执行：

```powershell
$repo = (Get-Location).Path
Get-ChildItem "$env:USERPROFILE\.codebuddy\skills" |
  Where-Object { $_.LinkType -eq "Junction" -and $_.Target -like "$repo\*" } |
  Remove-Item
```

## 约定

- 子模块**只读同步**：不在本地改子模块内容，需改动请向上游提 PR；因此子模块工作区始终干净，可放心用 `--remote --merge`。
- 主仓库保持精简：只提交 `.gitmodules` 与子模块指针，不要把根目录临时文件一起 `git add`。
- `.gitmodules` 变化时同步更新本文档的子模块表。

## 排查

```bash
git submodule status --recursive                  # - 未初始化，+ 指针与记录不一致
git submodule foreach --recursive git status      # 逐个查看子模块工作区
git config -f .gitmodules --get-regexp '\.path$'  # 查看子模块路径
```
