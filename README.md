# 游戏项目工具箱 Skill

本仓库本身就是一个可独立版本管理、跨项目安装的 Codex Skill：`game-project-toolkit`。根目录的 `SKILL.md` 是入口，不再包裹 `skills/game-project-toolkit/`。

按需提供三项能力：

- **Expand UI 适配**：保持参考设计区域，根据屏幕比例扩展逻辑视口。
- **图标 PNG**：从合适的开源图标库获取图标，生成项目内可重复维护的 PNG。
- **透明图集**：源码保留碎图，开发使用碎图，发布自动合图，业务加载代码保持一致。

规范不绑定引擎、图标库或构建工具。接入时先检查目标项目的原生能力和已有实现，再选择实现方式。单色染色、PixiJS、Vite 和 AssetPack 都是可选方案。

## 结构

```text
game-project-skills/
├─ README.md
├─ SKILL.md
├─ agents/openai.yaml
└─ references/
   ├─ ui-expand.md
   ├─ icon-png.md
   ├─ transparent-atlas.md
   └─ ai-gamex-example.md
```

Skill 内部链接均为相对路径，无需访问原参考项目。单个 Skill 放在仓库根目录便于直接安装；只有一个仓库需要容纳多个独立 Skill 时，才需要 `skills/<name>/` 这样的集合结构。

## 安装到 Codex

安装目标是个人技能目录下的 `game-project-toolkit`：默认 `~/.codex/skills/game-project-toolkit/`；配置了 `CODEX_HOME` 时，使用其下的 `skills/game-project-toolkit/`。

### 从 GitHub 地址安装

将仓库发布到 GitHub 后，可以直接向 Codex 提出：

> 使用 $skill-installer，从 https://github.com/OWNER/game-project-toolkit 安装技能。SKILL.md 位于仓库根目录，安装名称为 game-project-toolkit。

这里的 `OWNER/game-project-toolkit` 是示例地址，需要替换为实际仓库。创建本地仓库不等于已经发布到该地址。

Codex 自带的 GitHub 安装器需要选择仓库中的技能路径。根目录使用 `--path .`，并显式指定 `--name game-project-toolkit`，避免把 `.` 当作技能名称。仓库名称可以不同于技能名称。

在安装了系统 `skill-installer` 的 Codex 环境中，PowerShell 示例：

```powershell
$toolkitCodexRoot = if ($env:CODEX_HOME) {
    $env:CODEX_HOME
} else {
    Join-Path $env:USERPROFILE '.codex'
}
$toolkitInstaller = Join-Path $toolkitCodexRoot 'skills\.system\skill-installer\scripts\install-skill-from-github.py'
python $toolkitInstaller --url https://github.com/OWNER/game-project-toolkit --path . --name game-project-toolkit
```

可用 `--ref` 指定已发布的分支、标签或提交。私有仓库需要当前环境已经具备访问权限。该安装器针对 GitHub，不等于任意 Git 托管平台都支持同一 URL 安装流程。

### 从其他 Git 服务安装

GitLab、自建 Git 或其他可克隆地址，可将仓库直接克隆到个人技能目录下，以 `game-project-toolkit` 作为目录名。目标存在时不要覆盖现有安装。

```powershell
$toolkitCodexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
$toolkitSkillRoot = Join-Path $toolkitCodexRoot 'skills'
$toolkitDestination = Join-Path $toolkitSkillRoot 'game-project-toolkit'
if (Test-Path -LiteralPath $toolkitDestination) { throw '目标技能已存在，请先比较差异并安排更新。' }
New-Item -ItemType Directory -Path $toolkitSkillRoot -Force | Out-Null
git clone 'https://你的Git服务/你的账号/game-project-toolkit.git' $toolkitDestination
```

先将示例 URL 替换为实际地址，再运行命令。克隆方式保留 Git 历史，后续可按需更新；通过安装器下载的副本不应假定能直接 `git pull`。

### 从本地仓库安装

以下 PowerShell 示例从本仓库根目录复制技能必需内容，不复制 Git 元数据；已有同名技能时停止：

```powershell
$toolkitCodexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
$toolkitSkillRoot = Join-Path $toolkitCodexRoot 'skills'
$toolkitDestination = Join-Path $toolkitSkillRoot 'game-project-toolkit'
if (Test-Path -LiteralPath $toolkitDestination) {
    throw '目标技能已存在，请先比较差异并安排更新。'
}
New-Item -ItemType Directory -Path $toolkitDestination -Force | Out-Null
Copy-Item -LiteralPath @('.\SKILL.md', '.\agents', '.\references') -Destination $toolkitDestination -Recurse
```

安装完成后，下一个回合检查技能是否出现在可用列表；客户端未刷新时重开会话或应用。安装步骤与创建仓库分开执行。

## 使用

首次接入可以明确指定全部或部分能力：

> 使用 $game-project-toolkit，为当前引擎实现 Expand 适配和透明图集。暂不接入图标流程。

后续也可以只处理一项：

> 使用 $game-project-toolkit，为设置界面从合适的开源图标库选择图标并生成本地 PNG，保留多色。

> 使用 $game-project-toolkit，检查当前项目的原生分辨率设置能否实现 Expand，优先使用原生能力。

技能保持默认的自动匹配能力，描述同时覆盖这三种任务。自动匹配不保证每次发生；明确点名能减少歧义。初建时还不需要的模块不会预装依赖或生成配置。

## 接入后的项目

实现代码、依赖、配置和必要生成脚本都应保存在目标项目，运行和构建不依赖本 Skill 或参考项目。图标来源与生成参数、图集配置等应当可追溯和复现。

默认不生成 `AGENTS.md`，也不强制采用固定文档目录。优先维护项目已有的工程说明，用简短说明或关键注释记录已选方案和易误改的原因。用户希望显式约束后续 AI 开发时，再增加 `AGENTS.md` 指引。

## 维护

仓库是规范与接入流程的版本源；已接入项目保存自己的实现和配置。更新仓库或重新安装 Skill 不自动迁移旧项目，需要调整时针对该项目重新调用技能。

新增引擎案例应标明适用版本、接入边界和实际验证结果，不把案例中的库、目录、缓存策略或图片显示方式升级为通用要求。`ai-gamex` 案例仅保存已提炼的设计说明，不依赖原项目绝对路径。
