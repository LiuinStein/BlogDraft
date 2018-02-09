### 0x00 前言

撰写这篇文章的目的是为了指导实验室内使用Visual Studio作为常用IDE的同学对代码进行版本控制，笔者所演示的平台为Visual Studio 2017（英文版，中文版的我看着难受）。文章内如有错误或不妥之处，请批评指正。

### 0x01基于GitLab的版本控制 

#### --- 0x00 创建项目

首先登陆你的GitLab，在首页就有一个绿色的New project（创建新项目）按钮，点他一下，然后如下图所示，填写信息：

![0100.png](https://bucket.shaoqunliu.cn/image/0100.png)

填写完信息之后，就可以看到如下的界面了：

![0101.png](https://bucket.shaoqunliu.cn/image/0101.png)

这样一个新项目就算创建完成了

#### --- 0x01 在Visual Studio中使用GitLab

首先，让我们新建一个VS项目，然后在新建VS项目的时候，选中创建新的Git存储库那一项，这一点在之前写过的教程[在Visual Studio中使用GitHub](https://www.shaoqunliu.cn/wordpress/1159.html)都有提到过，而且里面有详细的操作步骤

接着打开Team Explorer窗口，我们来连接GitLab

![0102.png](https://bucket.shaoqunliu.cn/image/0102.png)

首先点击上图中红框框出来的那个绿色的小插头，进入连接管理页面，最下面有一个Push to Remote Repository将项目发布到远程服务器， 然后将项目的地址输入进那个框中，点击Publish按钮后会弹出一个身份验证框，在里面输入你的GitLab用户名和密码然后点击确定

![0103.png](https://bucket.shaoqunliu.cn/image/0103.png)

经过上述步骤即可将项目发布到GitLab，这个时候，我们可以登录自己的GitLab，然后就可以看到我们刚刚发布的项目代码文件，如下图：

![0104.png](https://bucket.shaoqunliu.cn/image/0104.png)

然后，我们回到Visual Studio的Team Explorer页面的Home页，即可进行提交代码、分支创建等操作：

![0105.png](https://bucket.shaoqunliu.cn/image/0105.png)

#### --- 0x02 其他操作

其他操作请参考上一篇blog： [在Visual Studio中使用GitHub](https://www.shaoqunliu.cn/wordpress/1159.html)

我在里面都已经写得很详细了



