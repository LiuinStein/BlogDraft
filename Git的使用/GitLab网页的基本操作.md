### 0x00 前言

撰写这篇文章的目的是为了指导实验室内的成员使用我们自建的GitLab服务器。文章内如有错误或不妥之处，请批评指正。

### 0x01 GitLab网页版的使用

#### --- 0x00 创建项目

首先登陆你的GitLab，在首页就有一个绿色的New project（创建新项目）按钮，点他一下，然后如下图所示，填写信息：

![0100.png](https://bucket.shaoqunliu.cn/image/0100.png)

填写完信息之后，就可以看到如下的界面了：

![0101.png](https://bucket.shaoqunliu.cn/image/0101.png)

这样一个新项目就算创建完成了

#### --- 0x01 项目设置（Edit Project）

我们可以在主页右上角的齿轮处找到项目设置（Edit Project）

![0117.png](https://bucket.shaoqunliu.cn/image/0117.png)

里面有很多的设置选项，英文好的话，这些选项都不是问题，我在这里不一一翻译，只说一些比较重要的设置

##### 0x00 权限设置（Sharing & Permissions）

* 项目的可见性（Project Visibility）：Private是仅指定的人可见，Internal是注册用户可见，Public是对全世界公开
* Repository（代码仓库），Snippets（代码片），Issues（问题）：Only term members（仅组内成员）

##### 0x01 项目改名（Rename repository）

可以在Rename repository选项中对项目改名，注意改名可以保持原有的项目URL

##### 0x02 移交项目（Transfer project）

假设，我作为A项目的负责人，现在我要退出实验室了，当我走了之后A项目由张三来接管项目负责人的位子，那我就可以使用Transfer project功能将项目所有权移交至张三

在Namespace下拉框中选择你需要移交到的对象，然后点击Transfer project即可

##### 0x03 删除项目（Remove project）

删除项目可以点击红色的Remove project按钮，然后在文本框中输入项目的名称，即可删除项目

#### --- 0x02 项目成员管理（Members）

我们可以在主页右上角的齿轮处找到成员管理（Members）

![0118.png](https://bucket.shaoqunliu.cn/image/0118.png)

第一个功能就是可以对当前项目添加新成员，可以为新成员指定一个身份，可供选择的身份如下：

* Guest（看客）
* Reporter（报告人员，请允许我这样翻译）
* Developer（开发者）
* Master（高级开发人员，意味着可以进行更多的操作）
* Owner（所有者，可进行所有操作）

具体的权限配置可以参考[GitLab权限文档](http://git.opencil.cn/help/user/permissions)

如果为成员配置了过期时间，那么超过那个指定的时间之后，该成员将会从这个项目中踢出去

#### --- 0x03 跨组分享（Share project with other groups）

我们要与其他组来分享代码或技术交流，就可以使用这个功能，在主页右上角的齿轮处成员管理（Members）里面

![0119.png](https://bucket.shaoqunliu.cn/image/0119.png)

#### --- 0x04 提交问题（Issues）

任何人（包括Guest）都可以提交一个Issues，Issues可以是一个bug或者一个新的创意或者对代码的建议。

![0120.png](https://bucket.shaoqunliu.cn/image/0120.png)

在填写了上述的选项之后，即可提交Issues

#### --- 0x05 帮助文档（Wiki）

可以使用Wiki来为你的项目撰写帮助文档