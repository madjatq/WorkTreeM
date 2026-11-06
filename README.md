# Git 分支与 WorkTree 多人协作演示

文档版本：v0.5
适用对象：Git 分支、WorkTree、提交推送和合并冲突演示。

本项目提供 `main`、`develop` 和 5 个成员任务分支。每个成员分支都有自己的文档，同时修改一个共同文件，用来演示独立开发和合并冲突。

团队日常开发流程请阅读：[小组开发规范简版：组员与组长](docs/team-development-short.md)；需要完整说明时阅读：[小组开发规范：VS Code + Codex，本地验证版](docs/team-development-standard.md)。本项目以本地检查为主，不配置 GitHub Actions。

## 当前分支和 WorkTree

以下路径是当前电脑上的目录。打开 VS Code 时，选择对应目录并在单独窗口中打开。

| 用途 | 分支 | 当前电脑上的 WorkTree 路径 | 目录中的演示内容 |
|---|---|---|---|
| 稳定基线和规范文档 | `main` | `D:\AI\WorkTreeTest\WorkTreeM` | README、开发规范；不用于本次成员任务合并 |
| 集成和冲突演示基线 | `develop` | `D:\AI\WorkTreeTest\WorkTreeM-develop` | `demo/merge-conflict.md` 的共同初始版本；PR 的目标分支 |
| 成员 A | `feature/member-a` | `D:\AI\WorkTreeTest\WorkTreeM-member-a` | `demo/member-a.md` 和冲突演示文件的 A 版本 |
| 成员 B | `feature/member-b` | `D:\AI\WorkTreeTest\WorkTreeM-member-b` | `demo/member-b.md` 和冲突演示文件的 B 版本 |
| 成员 C | `feature/member-c` | `D:\AI\WorkTreeTest\WorkTreeM-member-c` | `demo/member-c.md` 和冲突演示文件的 C 版本 |
| 成员 D | `feature/member-d` | `D:\AI\WorkTreeTest\WorkTreeM-member-d` | `demo/member-d.md` 和冲突演示文件的 D 版本 |
| 成员 E | `feature/member-e` | `D:\AI\WorkTreeTest\WorkTreeM-member-e` | `demo/member-e.md` 和冲突演示文件的 E 版本 |

这些 WorkTree 共用本机同一个 Git 克隆，但每个目录检出不同分支。WorkTree 是本机工作目录，不能从 GitHub 拉取；推送到 GitHub 的是分支和提交。其他成员应使用自己的 GitHub 账号克隆仓库，再获取自己负责的远端分支。

## 成员如何开始

在 VS Code 中克隆仓库后，打开终端并运行以下命令。把示例分支名替换为自己负责的分支：

```powershell
git fetch origin                                      # 获取 GitHub 上的最新分支和提交信息
git switch --track origin/feature/member-a            # 创建并切换到跟踪远端 member-a 的本地分支
```

如果本地分支已经存在，直接在 VS Code 左下角分支菜单中切换到对应的 `feature/member-*` 分支。确认分支名称正确后，编辑自己分支的 `demo/member-*.md`。成员不要修改其他人的独立文档。

完成后，在 VS Code 源代码管理面板检查变更、提交并推送自己的分支，再创建 Pull Request，目标分支选择 `develop`。推送会更新 GitHub 上的分支，不会自动把内容合并进 `develop`。

## 演示独立开发

1. 在 VS Code 中分别打开成员 A–E 对应的 WorkTree 目录，每个目录使用一个窗口。
2. 检查每个窗口左下角的分支名，确认与上表匹配。
3. 查看各自的 `demo/member-*.md`，确认每个分支有独立文档。
4. 查看 `demo/merge-conflict.md`，可以看到五个分支对此公共文件分别写入了不同的状态文字。

也可以让每位成员在自己的目录中补充文档，再分别提交和推送，以练习独立工作流程。修改提交后，需要重新推送分支，PR 才会显示最新内容。

## 演示合并冲突

五个成员分支都从同一份 `develop` 基线创建，并修改了 `demo/merge-conflict.md` 的同一行。可以按下面顺序演示：

1. 创建 `feature/member-a` 指向 `develop` 的 PR，并先合并它。
2. 再创建或检查 `feature/member-b` 指向 `develop` 的 PR。由于 B 分支仍从合并前的基线出发，GitHub 会提示同一行存在冲突。
3. 在成员 B 的任务分支解决冲突，保留双方需要的内容；提交、推送并重新检查后再合并。
4. C、D、E 分支也改过同一行，合并时可能需要按同样方式处理冲突。

不要在 `develop` 的 WorkTree 中直接修复某位成员的冲突。应在发生冲突的成员分支中解决并推送，这样解决结果会进入该成员的 PR，也便于重新检查。

## 查看分支和 WorkTree

在任意一个 WorkTree 的终端中运行：

```powershell
git branch -a -vv                 # 查看本地分支、远端分支和跟踪关系
git worktree list                 # 查看本机所有 WorkTree 目录及其当前分支
git status --short --branch       # 查看当前目录的分支和未提交修改
```

## 清理本机 WorkTree

确认目标目录没有未提交或需要保留的工作后，才能移除对应 WorkTree。以下示例移除成员 A 的本机目录，不会删除 GitHub 上的远端分支：

```powershell
git worktree remove ..\WorkTreeM-member-a    # 移除成员 A 的本机 WorkTree 目录
git worktree list                             # 确认剩余的 WorkTree 登记信息
```

成员分支合并后是否删除，由团队决定；不要把移除 WorkTree 目录和删除远端分支混为一谈。

## 私有仓库访问

私有 GitHub 仓库只有获授权的账号可以访问。仓库所有者应邀请每位成员使用自己的账号加入。`Read` 权限可克隆和拉取；推送自己的任务分支通常需要 `Write` 或更高权限。不要共用密码、访问令牌或 SSH 私钥。

## 变更记录

| 版本 | 变更 |
|---|---|
| v0.1 | 创建 WorkTree 多人协作演示说明 |
| v0.2 | 将示例文件创建步骤改为自然语言，并为 Git 命令补充作用注释 |
| v0.3 | 增加小组日常开发规范入口，明确直接合入 main 只用于演示，并提醒不要在已有项目克隆中重命名分支 |
| v0.4 | 增加只面向组员和组长的简版日常开发流程入口 |
| v0.5 | 按当前仓库实际分支和 WorkTree 更新说明，增加五个成员目录、远端分支获取和冲突演示流程 |
