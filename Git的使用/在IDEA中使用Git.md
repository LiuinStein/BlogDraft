### 0x00 前言

撰写这篇文章的目的是为了指导实验室内使用IntelliJ IDEA作为常用IDE的同学对代码进行版本控制，笔者所演示的平台为IntelliJ IDEA 2017.1 x64。文章内如有错误或不妥之处，请批评指正。

![0107.png](https://bucket.shaoqunliu.cn/image/0107.png)

此教程同样使用与JetBrains家的其他IDE，比如PhpStorm及PyCharm等

### 0x01 在IDEA中使用GitHub

#### --- 0x00 代码仓库的创建

首先，我没在新建项目的页面找到同时创建Git本地仓库的按钮或选项，不像VS那样，可以通过勾选在创建项目时新建Git本地存储仓库来快速进行代码仓库的创建

通过VCS菜单，里面有一个Import into version control（导入到版本控制），里面有一个Create Git Repository（创建Git仓库），可以创建一个Git仓库

![0108.png](https://bucket.shaoqunliu.cn/image/0108.png)

如果想同步到GitHub的话，可以点击下面的Share Project on GitHub，来将代码同步到GitHub，在这里可以使用Token登录也可以使用用户名和密码登录，演示示例中使用的是用户名密码的登录方式

![0109.png](https://bucket.shaoqunliu.cn/image/0109.png)

下面有一个复选框Save credentials意思是保存登录信息，勾选了之后，下次就可以免登录了。

然后点击Log in按钮之后，就会出现如下的对话框，填写其中的信息，然后点击Share按钮，即可将代码提交至GitHub

![0110.png](https://bucket.shaoqunliu.cn/image/0110.png)

#### --- 0x01 代码的提交

可以看看VCS菜单，或者工具栏的图标按钮，我们可以找到一个绿色的上箭头上面写着VCS，这个按钮就是Commit Changes（提交更改）

![0111.png](https://bucket.shaoqunliu.cn/image/0111.png)

点击之后，可以打开更改提交对话框，填写Commit Message之后点击Commit按钮即可提交

![0112.png](https://bucket.shaoqunliu.cn/image/0112.png)

注意：Commit Message不能为空，在下面的diff里面可以看到两个你要提交的代码与你本地上一个版本之间的不同。

#### --- 0x02 其他功能

![0113.png](https://bucket.shaoqunliu.cn/image/0113.png)

有关Branches（分支），Tag（标记），Merge（合并），Rebase（衍合），Fetch（同步），Pull（拉取），Push（提交），Revert（反转）这些功能，请参考之前写的文章：[在Visual Studio中使用GitHub](https://www.shaoqunliu.cn/wordpress/1159.html)，里面对这些功能及使用场景都有详细的介绍，在此不做演示

### 0x02 在IDEA中使用GitLab

首先依照教程[在Visual Studio中使用GitLab](https://www.shaoqunliu.cn/wordpress/1167.html)学习如何使用GitLab网页来创建一个项目

然后我们先依据上面的教程创建一个本地Git仓库，创建完成之后，点击VCS菜单，找到Git子菜单，然后下面有一个Remote（远端）的菜单项

![0114.png](https://bucket.shaoqunliu.cn/image/0114.png)

点击Remote之后，我们来新建一个Git Remote，点击绿色的加号，然后在弹出对话框的URL文本框中输入项目地址，然后点击OK按钮，即可连接到远端Git服务器。

![0115.png](https://bucket.shaoqunliu.cn/image/0115.png)

如果需要将代码上传到Git服务器上，你需要先commit changes（VCS菜单），然后push（VCS菜单->Git子菜单->push），才能上传到GitLab服务器，上传到GitLab服务器之后，可以在GitLab的项目Files里面，看到你上传的代码，如图：

![0116.png](https://bucket.shaoqunliu.cn/image/0116.png)