### Git(上)
 **分布式版本控制系统：Git、BitKeeper**
 <br/>**集中式版本控制系统：CVS、SVN**
 <br/>**差别：**


   |  |集中式（SVN）|分布式（Git）|
   |---| ---| --- |
   | 代码保存 |项目要开发完推送中央服务器|开发人员在本地仓库存储提交代码修改的历史|
   | 网络| 必须要联网才能工作，文件过大或者网速不好会很慢甚至失败|没有网络的情况下也可以在本地仓库执行commit、查看版本提交记录、以及分支操作，在有网络的情况下执行push到Remote Repository远端仓库|
   |文件存储格式|按照原始文件存储，体积较大|按照元数据方式存储，体积很小|
   |版本号|有|无|
   |分支操作的影响|创建新的分支则所有人都会拥有和你一样的分支，本质上是因为都在中央仓库上操作|分支操作不会影响其他开发人员|
   |提交|提交的文件会直接记录到中央版本库|提交是本地操作，需要执行push操作才会到远端仓库|

#### clone远程仓库、创建本地仓库
 <font color=green size=5>涉及到的命令：git clone / git init / git remote add origin</font>

   ![](https://api.superbed.cn/pic/5c2b258f9dc6d672c31ed93e)

    git clone https://github.com/beiliublock/data-structure-and-algorithm.git
  clone项目到本地之后，我们可以看到目录下有一个叫.git的隐藏文件

   ![](https://ww1.sinaimg.cn/large/007iUjdigy1fyr6ejo5rlj309a03bjr7.jpg)
    <br/>&emsp;这个`.git`文件夹就是我们的本地仓库(Local Repository),而.git所在的根目录就是我们的工作目录(Working Directory)
    <br/>&emsp;clone瞎来的项目就可以正常开放了，当然我们也可以在本地直接创建一个本地仓库，之后再与远程仓库进行关联，先执行

    git init
   &emsp;命令执行的目录下就有了一个`.git`文件夹，我们在创建了一个本地仓库，接着与远程仓库进行关联：

    git remote add orign https://github.com/beiliublock/data-structure-and-algorithm.git

#### 工作区 暂存区 版本库 远程仓库
 <font color=green size=5>涉及到的命令：git diff</font>
 <br/>**工作目录(Working directory)** 就是我们创建的项目文件夹，我们开发项目的地方，.git所在的根目录

    新创建的文件要添加到暂存区 -- git add
 <br/>**暂存区(index/staging area)** 是指存储了所有待提交的改动的地方，只在暂存区存在的文件，本地仓库才会追踪到它的变化
 <br/>暂存区对应在.git文件夹中的index中，是一个二进制文件，可以理解为一个索引，内容包括根据文件名，文件模式和元数据进行排序的文件路径列表，
 每个路径都有权限以及Blob类型的SHA-1标识符（就是我们平时提交记录对应的那一串编码）

    把文件从暂存区提交到本地的版本库中 -- git commit
 <br/>**版本库/本地仓库(Repository)** 可以理解为就是我们分布式版本控制中提到的我们本地的代码版本库，这里有我们所有提交版本的数据
 <br/>对应.git中的HEAD，实质上是一个指针，指向最新放入仓库的版本，默认情况下.git为我们自动生成了一个分支master，head就指向这个分支

 <br/>**远程仓库(Remote)** 托管代码的服务器，上面介绍分布式版本控制的远程中央仓库，可以理解为一台专用于协作开发时数据交换的电脑

 ![](https://i.loli.net/2019/01/01/5c2b56124abbe.png)

 ![](https://i.loli.net/2019/01/01/5c2b56400081c.png)

 <font color=green>git diff</font>

    git diff  比较的是工作区和暂存区的差别
    git diff HEAD   可以查看工作区和版本库的差别
    git diff --cached(===staged)  比较的是暂存区和版本库的差别

#### 一个简单的流程
<font color=green size=5>涉及到的命令：git status/ git add/ git commit/ git log/ git pull/ git push </font>

<br/>**git status**
<br/>创建一个文件夹，添加新的文件

    git status  //查看工作目录和暂存区的状态
Untracked files表示未追踪的文件，就是最新创建的src文件夹，未追踪的意思就是当前本地git仓库对它没有任何记录，
对本地仓库来说是不存在的，在我们提交代码的时候也不会提交上去，这时就用到了add命令

<br/>**git add**

    git add <file>  //将新创建的文件添加到暂存区  如：git add README.md
新添加的文件进入暂存区，从Untracked未追踪状态变为stage已暂存状态。接着文件进入暂存区之后，我们的修改就可以被暂存区追踪到，
我们修改一下新增的README.md文件

这次文件左边的状态从New file(新建)变成了modified(修改)，上面的提示不再是untracked(不在追踪范围)而是not staged for commit(还不在待提交的暂存区)，
意思是，本地仓库现在已经认识了这个文件，它被修改了，还没到储存待提交信息的暂存区中，还是使用add添加到暂存区

    git add <file>  //把工作目录改动添加到暂存区
注意，通过add添加进暂存区的不是文件名，而是具体的文件改动内容(上面创建文件那里说添加文件是方便理解)，我们把在执行add的改动都添加进了暂存区，在add
之后的新改动并不会自动被添加进暂存区，所以对README.md执行了add之后如果再修改README.md，那么工作目录和暂存区都会有这个目录

<br/>**git commit**
接着按照提示，可以commit提交了：

    git commit  //将暂存区文件提交到本地版本库中
输入`git commit`回车之后,因为commit需要编辑提交信息，所以会默认进入vim命令编辑模式，这时我们输入小写的`i`可以切换到插入模式，然后输入这次提交的备注信息，
输入完后，按ESC返回命令模式，输入ZZ或者`:wq`保存，一次commit就完成了

<br/>**git log**

    git log  //查看提交历史
    git log -p  //查看每个commit的每一行改动
    git log --stat  //查看文件修改，不展示具体内部修改细节

<br/>**git show**

    git show commit的编码  //查看commit的具体改动
要看最新commit，直接输入`git show`；要看指定commit，输入git show commit的引用(例如HEAD)或它的SHA-1编码，可以配合`git log`使用

<br/>**git push**

    git push  //将本地仓库推送到远程仓库

![](https://i.loli.net/2019/01/01/5c2b64fb1855e.png)
<br/>直接使用push会有这样的错误提示。意思是当前分支没有和上游远程分支做关联，git不知道你要推送到远程仓库的哪个分支上，
我们想要和远程的master分支关联，按照提示输入：`git push --set-upstream origin master`

origin是远程仓库的代指，master是远程仓库上的分支名，这里的origin/master，即远程仓库的master分支，就是我们实验项目的远程仓库。我们把关联的远程分支设置成了
origin/master，之后直接执行`git push`默认就会推送到远程的master下，当然我们不省略传入远程的分支名就会推送到对应的分支上

特别注意，如果你使用的不是clone而是直接本地init一个仓库与远程仓库关联的方式，第一次推送可能会遇到这样的情况：

![](https://i.loli.net/2019/01/01/5c2b679274771.png)
<br/>这是因为在远程创建新的项目时勾选了自动生成readme/.gitignore，它们就作为远程仓库的第一次commit，而我们本地git init的仓库没有远程仓库的
readme/.gitignore文件，所以无法提交成功。所以要先拉取一下远端的代码同步到本地，再把我们的改动提交上去，先pull再push。当然如果你创建远程仓库
的时候不勾选readme/.gitignore就可以直接push上去

但是光执行pull也是不够的，远程仓库有一个提交，我们本地仓库也有一个提交，直接拉取远端的代码，这两个提交谁先谁后呢？没有操作相同文件时可能
无所谓，但是一旦修改了同一个文件，就涉及到哪次提交在后，覆盖的问题，所以要执行：`git pull --rebase origin master`,这条指令的意思是把远程库
中的更新合并到本地库中，`rebase`的作用是取消掉本地库中刚刚的commit，并把它们接到更新后的版本库之中，rebase具体下面会讲到，先看下结果：

![](https://ww1.sinaimg.cn/large/007iUjdigy1fyreqcybmrj30kp0ch3zn.jpg)
<br/>看下提交记录

![](https://ww1.sinaimg.cn/large/007iUjdigy1fyrf9ojudhj30kp0chaaw.jpg)
<br/>远程仓库的initial commit是第一条记录，是我们刚提交在后面的

#### commit信息历史
<font color=green size=5>涉及到的命令：git log </font>

<br/>**commit的id**
<br/>每一个commit对应一个唯一id，是40位的字符串由数字和字符组成，是属于每一个commit的一个id，一个SHA-1校验和

![](https://ww1.sinaimg.cn/large/007iUjdigy1fyrfkoezv7j30g603kaa5.jpg)
<br/>第一行，tree和对应的hash值，根据这个hash值我们可以得到本次提交的整个目录和对应的hash值

    git cat-file -p hash值  //以更优雅的方式展现对象内容
里面的每一个文件都可以根据hash值继续展开，直到叶子节点

<br/>回到head信息组成这里，第二行parent，是当前查看的commit的上一条commit的id；第三行作者信息以及提交时的时间戳；
第四行提交者的信息以及提交时间的时间戳

<br/>根据head对应的提交信息生成其SHA-1值的命令如下：

    (printf "commit %s\0" $(git cat-file commit HEAD | wc -c); git cat-file commit HEAD) | shasum

![](https://api.superbed.cn/pic/5c2b75639dc6d672c31ed962)

[原文链接](https://mp.weixin.qq.com/s?__biz=MzU0OTExNzYwNg==&mid=2247484483&idx=1&sn=db181ee210f9490379af3e4a74f0cde8&chksm=fbb58f8accc2069cbe9afda4fcf0222791aee3eefa56e3471d05f4ca7390464ea2ab66e472a0&token=942885247&lang=zh_CN&rd2werd=1#wechat_redirect)






