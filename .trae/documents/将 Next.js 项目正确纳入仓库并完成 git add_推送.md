## 背景与现状
- 当前仓库目录：`d:\AllCode\traemake\supabase_google`，其中包含应用目录：`auth-supabase-google/`。
- 远程已设置为：`https://github.com/anonymc0der/auth-supabase-google.git`，且已首次推送。
- 之前推送日志出现“adding embedded git repository: auth-supabase-google”，说明曾将内层目录作为嵌套仓库（子模块指针）添加，导致外层仓库未跟踪其具体文件。

## 目标
- 将 `auth-supabase-google/` 的源码文件作为普通文件纳入当前仓库进行版本控制，而非子模块指针。
- 执行 `git add .` 完成阶段、提交并推送，使 GitHub 仓库显示完整项目结构与文件。

## 实施步骤
1. 检查是否仍存在内层 `.git`：
   - PowerShell：`Test-Path auth-supabase-google\.git`
   - 若存在，继续第 2 步；若不存在，跳到第 3 步。
2. 清理嵌套仓库状态：
   - 备份或删除内层仓库元数据（保留历史可将 `.git` 重命名备份）：
     - 备份：`Rename-Item auth-supabase-google\.git .git.bak`
     - 或删除：`Remove-Item -Recurse -Force auth-supabase-google\.git`
   - 从外层索引移除子模块指针（不删除工作区文件）：`git rm --cached auth-supabase-google`
3. 设置/更新忽略文件：
   - 确保外层仓库有 `.gitignore`（如已存在则合并），忽略：`node_modules/`、`.next/`、`.env*`、`*.log` 等 Next.js 常见产物。
4. 阶段与提交：
   - `git add .`
   - `git commit -m "Import Next.js app into repo"`
5. 推送到远程：
   - `git push -u origin main`

## 验证
- `git status` 应无待提交更改；`git ls-files` 应显示 `auth-supabase-google/` 下的源码文件。
- 远程仓库页面应显示完整的 Next.js 项目结构，而非子模块提示。
- 再次执行提交时不应出现 “adding embedded git repository” 的提示。

## 风险与回滚
- 若误操作导致问题：
  - 通过还原备份的 `.git.bak` 恢复内层仓库：`Rename-Item auth-supabase-google\.git.bak .git`
  - 或使用 `git reset --hard` 回退到提交点。

## 下一步动作
- 按上述步骤执行；如确认保留子模块模式而非合并为普通文件，可改为使用：`git submodule add <url> auth-supabase-google`，但当前远程即为该仓库，建议合并为普通文件以避免循环依赖。