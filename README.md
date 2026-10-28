# 游戏项目 Skill 仓库

一个可独立版本管理、跨项目安装的 Codex Skill：`game-project-toolkit`。

按需提供三项能力：

- **Expand UI 适配**：保持参考设计区域，根据屏幕比例扩展逻辑视口。
- **图标 PNG**：从合适的开源图标库获取图标，生成项目内可重复维护的 PNG。
- **透明图集**：源码保留碎图，开发使用碎图，发布自动合图，业务加载代码保持一致。

规范不绑定引擎、图标库或构建工具。接入时先检查目标项目的原生能力和已有实现，再选择实现方式。单色染色、PixiJS、Vite 和 AssetPack 都是可选方案。

## 结构

```text
game-project-skills/
├─ README.md
└─ skills/
   └─ game-project-toolkit/
      ├─ SKILL.md
      ├─ agents/openai.yaml
      └─ references/
         ├─ ui-expand.md
         ├─ icon-png.md
         ├─ transparent-atlas.md
         └─ ai-gamex-example.md
```

安装单元是 `skills/game-project-toolkit` 整个文件夹。Skill 内部链接均为相对路径，无需访问原参考项目。

## 安装到 Codex

将上述文件夹复制到个人技能目录：默认 `~/.codex/skills/`；配置了 `CODEX_HOME` 时，使用其下的 `skills/`。后续在新会话中检查该技能是否出现在可用列表；客户端未刷新时重开会话或应用。

以下 PowerShell 示例用于**从本仓库根目录首次安装**，已有同名技能时停止，避免意外覆盖：

```powershell
$toolkitSkillRoot = if ($env:CODEX_HOME) {
    Join-Path $env:CODEX_HOME 'skills'
} else {
    Join-Path $env:USERPROFILE '.codex\skills'
}
$toolkitDestination = Join-Path $toolkitSkillRoot 'game-project-toolkit'
if (Test-Path -LiteralPath $toolkitDestination) {
    throw '目标技能已存在，请先比较差异并安排更新。'
}
New-Item -ItemType Directory -Path $toolkitSkillRoot -Force | Out-Null
Copy-Item -LiteralPath '.\skills\game-project-toolkit' -Destination $toolkitDestination -Recurse
```

安装步骤与创建仓库分开执行。本仓库本身不修改任何其他项目或个人设置。

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
