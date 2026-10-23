# Issue tracker: GitHub

本仓库的问题和规格保存在 eric03742/netrunner-table 的 GitHub Issues。
使用 gh CLI 操作；在仓库内由 git remote 推断目标仓库，
在其他目录运行时显式传入 --repo eric03742/netrunner-table。

## 常用操作

- 创建：gh issue create --title "标题" --body-file <正文文件>
- 读取：gh issue view <编号> --json number,title,body,labels,comments
- 列出：gh issue list --state open --json number,title,body,labels,comments
  按需添加 --label 和 --state 筛选。
- 更新正文：gh issue edit <编号> --body-file <正文文件>
- 评论：gh issue comment <编号> --body-file <评论文件>
- 添加或移除标签：gh issue edit <编号> --add-label "标签" 或 --remove-label "标签"
- 关闭：gh issue close <编号>

多行正文先写入临时文件，再通过 --body-file 传入，保留原始换行。

技能要求“发布到问题跟踪器”时，创建 GitHub issue；
要求“获取相关工单”时，读取对应 issue。

## Pull requests as a triage surface

**PRs as a request surface: no.**

GitHub 的 issues 和 PRs 共用编号空间。
编号存在歧义时，先用 gh pr view <编号> 判断，再回退到 gh issue view。

## 父子关系与依赖

- 创建子 issue：gh issue create --parent <父编号> --title "标题" --body-file <正文文件>
- 关联现有子 issue：gh issue edit <父编号> --add-sub-issue <子编号>
- 添加阻塞关系：gh issue edit <被阻塞编号> --add-blocked-by <阻塞方编号>
- 无法使用原生父子关系时，在子 issue 正文顶部写 Part of #<父编号>，
  并在父 issue 的任务列表中记录它。
- 无法使用原生依赖时，在正文顶部写 Blocked by: #<编号>。
  所有阻塞方关闭后，工单才解除阻塞。

## Wayfinder 约定

- Map 是一个带 wayfinder:map 标签的 issue，
  正文包含 Notes、Decisions-so-far、Fog。
- 子工单关联到 Map，使用 wayfinder:<type> 标签；
  type 为 research、prototype、grilling 或 task。
- 按 Map 中的顺序选择未关闭、无负责人、没有未解决阻塞的子工单。
  原生依赖的 issue_dependencies_summary.blocked_by 为 0 表示无未解决阻塞；
  回退文本中的阻塞编号则逐项检查。
- 领取：gh issue edit <编号> --add-assignee @me。
- 完成：评论记录结论，关闭子工单，
  再向 Map 的 Decisions-so-far 追加摘要和链接。
