# Git WorkTree 协作演示  曲晓

文档版本：v0.2
适用对象：演示参与者
目的：用少量 Markdown 文件演示 WorkTree 创建、并行开发、推送、合并、拉取和冲突处理。

## 演示内容

仓库用“活动策划”作为示例。Alice、Bob、Carol 分别在自己的分支中创建一个独立文件，再把三个分支合并到 `main`。独立文件让首次演示容易成功；冲突练习放在最后，可按需进行。

活动状态：筹备中

| 演示者 | 分支 | 文件 | 内容 |
|---|---|---|---|
| Alice | `feature/brief` | `content/brief.md` | 活动主题与目标，写两三句话 |
| Bob | `feature/timeline` | `content/timeline.md` | 列出三条活动流程 |
| Carol | `feature/tasks` | `content/tasks.md` | 列出三项待办和负责人 |

## 初始化远端

先在本地创建并提交本 README。若当前分支不是 `main`，先将它改名：

```bash
git add README.md                         # 将 README.md 加入暂存区
git commit -m "Add WorkTree demo guide"   # 将暂存内容保存为一次提交
git branch -M main                        # 将当前分支重命名为 main
```

在 GitHub 创建一个空仓库后，将命令中的 `USERNAME/REPOSITORY` 替换为你的 GitHub 用户名和仓库名。若 `origin` 已存在，跳过添加远端这一步。

```bash
git remote add origin https://github.com/USERNAME/REPOSITORY.git  # 将 GitHub 仓库地址添加为 origin 远端
git push -u origin main                   # 推送 main，并设置后续推送默认关联
```

## 创建 WorkTree

以下命令示例使用 Git Bash。在主工作目录中执行这些命令；每条 WorkTree 命令会创建一个新目录，并在其中创建对应分支：

```bash
git fetch origin                                                   # 获取 origin 上的最新分支和提交信息
git worktree add ../wt-alice -b feature/brief origin/main         # 从 origin/main 创建 Alice 的分支和工作目录
git worktree add ../wt-bob -b feature/timeline origin/main        # 从 origin/main 创建 Bob 的分支和工作目录
git worktree add ../wt-carol -b feature/tasks origin/main         # 从 origin/main 创建 Carol 的分支和工作目录
git worktree list                                                  # 查看当前仓库已登记的 WorkTree
```

Alice 进入自己的 `wt-alice` 工作目录，在其中新建 `content` 目录和 `brief.md` 文件。文件写上活动主题与目标，用两三句话即可。Bob 和 Carol 分别进入自己的工作目录，新建 `content` 目录，并按表格创建 `timeline.md` 或 `tasks.md`，填写三条活动流程或三项待办与负责人。可以使用任何熟悉的文本编辑器创建和编辑这些文件。

## 提交、推送与合并

每位演示者在自己的 WorkTree 中提交并推送分支。以下是 Alice 的示例：

```bash
git add content/brief.md                         # 将 Alice 创建的活动简介加入暂存区
git commit -m "Add activity brief"               # 提交活动简介文件
git push -u origin feature/brief                 # 推送 Alice 的分支并设置默认关联
```

其他人替换为自己的文件名、提交说明和分支名。随后每个人在 GitHub 上创建一个指向 `main` 的 Pull Request；依次合并三个 PR，展示多人分支汇入主分支的过程。

合并后，在主工作目录更新本地 `main`：

```bash
git switch main                                  # 切换到本地 main 分支
git pull --ff-only origin main                   # 拉取 origin/main 更新，仅允许快进合并
```

## 可选：演示合并冲突

从最新的 `origin/main` 创建两个冲突练习分支：

```bash
git worktree add ../wt-conflict-alice -b conflict/alice origin/main  # 从 origin/main 创建 Alice 的冲突练习工作目录
git worktree add ../wt-conflict-bob -b conflict/bob origin/main      # 从 origin/main 创建 Bob 的冲突练习工作目录
```

两个人都修改 `README.md` 中同一行，例如把“活动状态：筹备中”分别改成不同内容，然后各自提交并推送。先合并一个分支，再合并另一个分支，第二个 PR 会产生冲突。解决冲突后提交并推送更新，再完成合并。

## WorkTree 与多人协作

WorkTree 是同一个本地 Git 仓库中的多个工作目录，目录之间使用不同分支。单机演示时，可以像上面这样用一个克隆创建多个 WorkTree；这适合展示分支和文件合并，但这些目录共用同一个本地仓库和 GitHub 登录凭据。

真实多人协作时，每个人应使用自己的 GitHub 账号克隆仓库，再在自己的克隆中创建 WorkTree。这样每个人独立提交和推送，也能按账号管理访问权限。

## 私有 GitHub 仓库权限

私有仓库只有获授权的账号可以访问。仓库所有者可以邀请协作者，或在组织仓库中授予团队权限：

- `Read`：可以克隆和拉取，不能推送。
- `Write` 或更高权限：通常可以推送分支；受保护分支仍可能要求通过 Pull Request。

每位参与者都应使用自己的账号和凭据，不要共用密码、令牌或 SSH 私钥。

## 清理演示 WorkTree

演示结束后，在主工作目录移除不再需要的 WorkTree。先确认其中没有未提交内容：

```bash
git worktree list                       # 查看 WorkTree 路径及其关联分支
git worktree remove ../wt-alice         # 移除 Alice 的 WorkTree 工作目录
git worktree remove ../wt-bob           # 移除 Bob 的 WorkTree 工作目录
git worktree remove ../wt-carol         # 移除 Carol 的 WorkTree 工作目录
git worktree prune                      # 清理已失效的 WorkTree 登记信息
```

冲突练习目录也可用同样方式移除。

## 变更记录

| 版本 | 变更 |
|---|---|
| v0.1 | 创建 WorkTree 多人协作演示说明 |
| v0.2 | 将示例文件创建步骤改为自然语言，并为 Git 命令补充作用注释 |
