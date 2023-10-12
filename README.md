# BitComputerCommunication

# 提交规范
## 1.Git 规范必要性

> 有效高速的管理从小到大的项目版本管理

### 1.1 分支管理

*   代码提交在对应的分支中
> 这里指的是不同的项目有不同的分支可以在自己的分支中提交代码，然后进行 code review 之后进行合并分支，删除单独分支
*   随时切换到线上的稳定版本
*   多个版本开发工作同时进行

### 1.2 提交记录的可读性

*   进行准确的提交描述，具备可检索性

    > 比如 git commit 你现在完成了一个功能比如是完成了一个接口/Register
    > git commit -m "完成了/register 接口的 xxx 功能"

*   合理的提交范围，避免一个功能就一笔提交

*   分支间的合并保有提交历史，且合并后结果清晰

### 1.3 团队协作

*   明确每个分支的功用，做到对应的分支执行对应的操作
*   合理的提交，每次提交有明确的改动范围和规范的提交信息
*   使用 Git 管理版本迭代、紧急线上 bug fix、功能开发等任务

## 2.分支管理规范

### 2.1 分支说明和操作

*   **master 分支**

    *   主分支，永远处于稳定状态，对应当前线上版本
    *   以 tag 标记一个版本，因此在 master 分支上看到的每一个 tag 都应该对应一个线上版本
    *   不允许在该分支直接提交代码

*   **develop 分支**

    *   开发分支，包含了项目最新的功能和代码，所有开发都依赖 develop 分支进行

    *   小的改动可以直接在 develop 分支进行，改动较多时切出新的 feature 分支进行
    >**注：** 更好的做法是 develop 分支作为开发的主分支，也不允许直接提交代码。小改动也应该以 feature 分支提 merge request 合并，目的是保证每个改动都经过了强制代码 review，降低代码风险。

*   **feature 分支**

    *   功能分支，开发新功能的分支
    *   开发新的功能或者改动较大的调整，从 develop 分支切换出 feature 分支，分支名称为 `feature/xxx`
    *   开发完成后合并回 develop 分支并且删除该 feature/xxx 分支

*   **release 分支**

    *   发布分支，新功能合并到 develop 分支，准备发布新版本时使用的分支
    *   当 develop 分支完成功能合并和部分 bug fix，准备发布新版本时，切出一个 release 分支，来做发布前的准备，分支名约定为`release/xxx`
    *   发布之前发现的 bug 就直接在这个分支上修复，确定准备发版本就合并到 master 分支，完成发布，同时合并到 develop 分支

*   **hotfix 分支**

    *   紧急修复线上 bug 分支
    *   当线上版本出现 bug 时，从 master 分支切出一个 `hotfix/xxx` 分支，完成 bug 修复，然后将 `hotfix/xxx` 合并到 master 和 develop 分支(如果此时存在 release 分支，则应该合并到 release 分支)，合并完成后删除该 `hotfix/xxx` 分支

details 总结

*   master 分支: 线上稳定版本分支
*   develop 分支: 开发分支，衍生出 feature 分支和 release 分支
*   release 分支: 发布分支，准备待发布版本的分支，存在多个，版本发布之后删除
*   feature 分支: 功能分支，完成特定功能开发的分支，存在多个，功能合并之后删除
*   hotfix 分支: 紧急热修复分支，存在多个，紧急版本发布之后删除

### 2.2 分支间操作注意事项

*   同一分支`git pull`使用 **rebase**

    > `git pull` 默认为 merge 行为，当更新代码时，如果本地存在未推送到远程的提交，则会产生这样的 merge 的提交记录因此在同一个分支上更新代码时推荐使用 `git pull --rebase`。

    默认的 `git pull` 行为是 merge，可以进行如下设置修改默认的 `git pull` 行为：

```powershell
# 为某个分支单独设置，这里是设置 dev 分支
git config branch.dev.rebase true
# 全局设置，所有的分支 git pull 均使用 --rebase
git config --global pull.rebase true
git config --global branch.autoSetupRebase always
```

> 具体可以参考 ： [在开发过程中使用 git rebase 还是 git merge，优缺点分别是什么？](https://www.zhihu.com/question/36509119)

*   **分支合并使用 `--no-ff`**

    ```powershell
      # 例如当前在 develop 分支，需要合并 feature/xxx 分支
      git merge --no-ff feature/xxx
    ```

    首先理解 Git 中的`fast-forward`,比如在开发的过程中我们一直在推进`develop`分支上面的工作，此时有一个新功能需要开发，新建一个`feature/a`分支，并在其上进行一系列的提交开发，当完成开发的时候，此时需要 merge 到`develop`分支，此时就需要在`develop` 分支在创建`feature/a`分支后没有产生任何 commit，此时的合并就叫`fast-forward`
    fast-forward 合并的结果如下图所示，这种 merge 的结果就是一条直线了，无法明确看到切出一个新的 feature 分支，并完成了一个新的功能开发，因此此时比较推荐使用 `git merge --no-ff `，得到的结果就很明确知道，新的一系列提交是完成了一个新的功能，如果需要对这个功能进行 code review，那么只需要检视叉的那条线上的提交即可。
![git_merge_diff.svg](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab9a5c6174f846e9859ced862b0a3bee~tplv-k3u1fbpfcp-image.image#?w=600&h=400&s=9713&e=svg&a=1&b=9a9793)

## 3.提交信息规范

`git commit` 格式 如下：

```bash
<type>(<scope>)：<subject>
```

部分说明:

*   **type** 类型，提交的类别
    *   **feat**：新功能
    *   **fix**：修复 bug
    *   **docs**：文档变动
    *   **style**：格式调整，对代码实际运行没有改动，例如添加空行、格式化等
    *   **refactor**：bug 修复和添加新功能之外的代码改动
    *   **perf**：提升性能的改动
    *   **test**：添加或修正测试代码
    *   **chore**：构建过程或辅助工具和库（如文档生成）的更改

*   **scope**修改范围
    主要是这次修改涉及到的部分，简单概括，例如 login、train-order

*   subject 修改的描述
    具体的修改描述信息

*   实例

    ```bash
    feat(detail)：详情页修改样式
    fix(login)：登录页面错误处理
    test(list)：列表页添加测试代码
    ```


# 前端： 前端文档()

# 后端: 后端文档()

TODO:
1. 项目架构设计和项目初始化设计
