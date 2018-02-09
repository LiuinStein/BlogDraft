### 0x00 前言

撰写这篇文章的目的是为了指导实验室内使用Visual Studio作为常用IDE的同学对代码进行版本控制，笔者所演示的平台为Visual Studio 2017（英文版，中文版的我看着难受）。文章内如有错误或不妥之处，请批评指正。

### 0x01 基于GitHub的版本控制

#### --- 0x00 GitHub扩展

如果使用Visual Studio的话，在安装的时候，可以选择安装GitHub扩展，如图为Visual Studio 2017的安装界面，可以通过勾选`GitHub Extension`一项使Visual Studio自动安装GitHub扩展：

![0066.png](https://bucket.shaoqunliu.cn/image/0066.png)

#### --- 0x01 登录Git

可以在Team Explorer窗口中点击connect然后输入用户名密码登录来连接到GitHub，如图：

![0067.png](https://bucket.shaoqunliu.cn/image/0067.png)

#### --- 0x02 在新项目中使用Git

我们可以勾选如下选项来在创建新项目的同时创建Git存储仓库：

![0068.png](https://bucket.shaoqunliu.cn/image/0068.png)

#### --- 0x03 将代码托管到GitHub

在上一步的操作中，我们在新建项目的同时创建了一个本地的存储仓库，现在我们来将这个本地的存储仓库上传到GitHub

在Team Explorer的Local Git Repositories（本地Git存储仓库）中找到你刚刚创建的项目名称，然后双击

![0069.png](https://bucket.shaoqunliu.cn/image/0069.png)

然后点击Sync（同步）按钮将项目同步到GitHub

![0070.png](https://bucket.shaoqunliu.cn/image/0070.png)

![0072.png](https://bucket.shaoqunliu.cn/image/0072.png)

完成信息的填写，然后点击Publish按钮就可以将代码上传至GitHub了

上传成功后，Team Explorer中会出现当前项目的GitHub地址和更多的操作选项：

![0073.png](https://bucket.shaoqunliu.cn/image/0073.png)

同时我们也可以在GitHub网站看到我们的源代码：

![0074.png](https://bucket.shaoqunliu.cn/image/0074.png)

#### --- 0x04 提交代码

当完成了某些代码的编写之后，就可以选择向服务器提交代码。

点击Changes按钮

![0075.png](https://bucket.shaoqunliu.cn/image/0075.png)

填写摘要：

![0076.png](https://bucket.shaoqunliu.cn/image/0076.png)

点击Commit All按钮即可将代码提交至本地存储仓库，提交成功后如图：

![0077.png](https://bucket.shaoqunliu.cn/image/0077.png)

如需同步至GitHub可以点击Sync链接打开Synchronization（同步）页面，在Outgoing Commits里面可以看到本地存储库里面的更改，点击红框中的Push按钮即可同步代码至GitHub

![0078.png](https://bucket.shaoqunliu.cn/image/0078.png)

#### --- 0x05 拉取代码

我们可以通过Synchronization（同步）页面的Incoming Commits选项来查找和拉取别人的更改，首先点击Fetch，更新代码的修改状态

![0079.png](https://bucket.shaoqunliu.cn/image/0079.png)

然后我们就可以在下面看到服务器上最新的提交记录，点击Pull按钮将代码拉取至本地

![0080.png](https://bucket.shaoqunliu.cn/image/0080.png)

#### --- 0x06 回滚代码

好了，现在假设我们把代码改坏了，或者因为别的原因需要回滚代码。

我们可以通过Changes页面的Action菜单找到View History（查看历史）选项来查看之前提交的历史记录

![0081.png](https://bucket.shaoqunliu.cn/image/0081.png)

打开View History页面之后，我们可以看到所有的提交记录，选择一个你想要回退到的版本然后右键可以找到Reset（重置）菜单和Revert（还原选项）

##### Reset重置

Reset针对于还没有push到GitHub服务器上的代码，可以用来取消commit（提交）

![0082.png](https://bucket.shaoqunliu.cn/image/0082.png)

* Keep Changes(--mixed)，会保留源码，将版本号(Git commit id)和index信息回退到某个版本


* Delete Changes(--hard)，会删除修改，源代码和版本号都会回退到某个版本

##### Revert反转提交

Revert针对已经push到服务器上的代码，如果你想回退本地代码的同时也同时回退服务器上的代码即可使用这个选项，可以使服务器上的代码与本地的代码保持一致，Revert之后本地代码回滚到历史版本，然后通过push可以将服务器上的代码也一并更新

Revert之后会在Outgoing commits中产生一个未提交记录

![0083.png](https://bucket.shaoqunliu.cn/image/0083.png)

#### --- 0x07 创建分支

先来说说在什么情况下需要创建分支，假设说你现在在开发着一个项目，这个时候，你的项目经理打电话来了，需要在原有的基础上添加一个新功能，这个时候你就需要创建一个分支，如果说不创建分支的话，假设说过了两天现有的程序出了bug需要紧急修复，但是你的新功能还没有开发完成，这样就会产生一个尴尬的局面， 你需要将代码暂时回退到发布程序的那个版本，然后再进行修改，如果说创建一个分支来加入新功能的话，源程序出了bug，你只需要切换到原来的那个分支，进行修改即可。当新功能开发完成之后即可将这两个分支合并，然后继续下面的工作。

首先打开View History界面，然后右键某一个提交记录，点击New Branch（新建分支）按钮，来创建一个分支

![0084.png](https://bucket.shaoqunliu.cn/image/0084.png)

填写分支名：

![0085.png](https://bucket.shaoqunliu.cn/image/0085.png)

然后我们就可以在Change页将新建的这个分支push到GitHub，此时，如果我们打开GitHub的页面就可以看到新建的这个分支：

![0086.png](https://bucket.shaoqunliu.cn/image/0086.png)

同时我们可以在Team Explorer的Branches页面里面看到这两个分支，通过双击分支名可以快速切换代码所处的分支：

![0087.png](https://bucket.shaoqunliu.cn/image/0087.png)

为了做演示，我在Hello分支里面添加了一个变量`a`（作为一个新功能的示例），在master分支里面并没有，我们可以通过切换分支来看出两个分支之间代码的不同：

主分支(master)：

![0088.png](https://bucket.shaoqunliu.cn/image/0088.png)

刚刚新建的Hello分支：

![0089.png](https://bucket.shaoqunliu.cn/image/0089.png)

#### --- 0x08 分支的合并与衍合概念理解

合并和衍合是两个重要的分支操作，这两个操作都是将两个分支的更改合并到一起，只是合并的方式上略微有所不同，先来解释一下合并（merge）和衍合（rebase）的合并原理，然后我们再来讲解怎么操作。下面的图是我用[ProcessOn](https://www.processon.com)画出来的，专门画这些图的目的，是因为我认为[git-scm](https://git-scm.com)上的图画的并不怎么好理解，我做的图都是源于我个人的见解，如有不妥，请多指教。

##### 0x00 分支的合并(Merge)

Merge操作用于合并两个分支，他会将两个分支的最新版本（commit A2及commit M2）以及二者共同的祖先（commit M1）进行三方合并，然后形成一个新的提交对象（commit M3），通过合并一个分支来整合了分叉的历史，如图：

![0096.png](https://bucket.shaoqunliu.cn/image/0096.png)

##### 0x01 分支的衍合(Rebase)

Rebase操作用于将一个分支里面的提交的改变移动到另一个分支里面重放一遍，看图：

![0097.png](https://bucket.shaoqunliu.cn/image/0097.png)

![0098.png](https://bucket.shaoqunliu.cn/image/0098.png)

结束状态的代码，Rebase和Merge产生的结果是一样的，但是二者的提交记录是不一样的，Rebase的原理是回到两个分支最近的共同祖先（上图中的commit M1），然后依据这个分支后序的历次提交对象，生成一系列的补丁文件，然后以主分支最后一个提交记录（上图中commit M2）为新的出发点，然后逐个应用之前生成的补丁文件，然后最终会生成一个合并后的提交对象（上图中commit A2'），然后刚刚创建的新功能分支就成了主分支的直接下游了。

以下摘抄自[3.6 Git 分支 - 分支的衍合](https://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E8%A1%8D%E5%90%88)

```
一般我们使用衍合的目的，是想要得到一个能在远程分支上干净应用的补丁 — 比如某些项目你不是维护者，但想帮点忙的话，最好用衍合：先在自己的一个分支里进行开发，当准备向主项目提交补丁的时候，根据最新的 origin/master 进行一次衍合操作然后再提交，这样维护者就不需要做任何整合工作（译注：实际上是把解决分支补丁同最新主干代码之间冲突的责任，化转为由提交补丁的人来解决。），只需根据你提供的仓库地址作一次快进合并，或者直接采纳你提交的补丁。

请注意，合并结果中最后一次提交所指向的快照，无论是通过衍合，还是三方合并，都会得到相同的快照内容，只不过提交历史不同罢了。衍合是按照每行的修改次序重演一遍修改，而合并是把最终结果合在一起。
```

#### --- 0x09 分支的合并

在上面我们新建了一个Hello分支，然后添加了一个变量`a`用来模拟实际项目中添加的新功能，好了现在我要把这个Hello分支合并到master主分支上去

在Branches页面中有一个Merge按钮，如图：

![0091.png](https://bucket.shaoqunliu.cn/image/0091.png)

点一下Merge按钮，Merge from branch是需要合并的分支，Into current branch是合并到的分支，可以用分支名替换一下上面句子里面的branch这个单词，就好比这个例子，就是Merge from Hello branch into master branch，从Hello分支合并到master分支，虽然不知道这在英语上是不是个病句，但是这样做的确可以方便记忆。

![0090.png](https://bucket.shaoqunliu.cn/image/0090.png)

然后点击Merge按钮即可合并两个分支，如果需要合并的两个分支的代码中同时修改了同一处地方，那么就需要进行人为裁决来解决冲突，如下图所示：

发现冲突：

![0092.png](https://bucket.shaoqunliu.cn/image/0092.png)

查看带有冲突的文件

![0093.png](https://bucket.shaoqunliu.cn/image/0093.png)

可以使用Compare Files（比较文件）的功能来比较两个文件之间有什么不同：

![0094.png](https://bucket.shaoqunliu.cn/image/0094.png)

选择归并后，你就可以设计解决这些冲突的办法，下图是冲突解决办法页面：

![0095.png](https://bucket.shaoqunliu.cn/image/0095.png)

解决了冲突你就可以选择进行归并操作了，合并之后的结果与Result窗口里面预览的结果是一样的。

#### --- 0x0A 分支的衍合

分支的衍合原理在上面已经讲到了，我们可以通过Team Explorer中的Branchs页面中的Rebase来快速完成分支的衍合，处理衍合的情况和处理Merge的操作差不多，如果有冲突的话，系统也是会提醒你处理冲突，在此就不多做赘述

![0099.png](https://bucket.shaoqunliu.cn/image/0099.png)



### 0x02 参考资料

[3.2 Git 分支 - 分支的新建与合并](https://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6)

[3.6 Git 分支 - 分支的衍合](https://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E8%A1%8D%E5%90%88)







