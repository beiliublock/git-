### Git(中)

#### 1.branch分支的创建和切换
<font color=green size=5>git branch/ git checkout/ git checkout -b/ git branch -b</font>

    git branch 新分支的名称   //创建分支
这样创建完新的分支，并不会自动切换到新的分支上

    git checkout 分支名  //切换分支
以上两部分可以合并

    git checkout -b 新的分支名  //创建并切换到新的分支

![](https://i.loli.net/2019/01/01/5c2b869ae9539.png)
<br/>切换到新的分支后，HEAD也会跟着过去，当当前新分支有新的commit时，HEAD会指向这个分支最新的commit，master
会停留在它之前对应的commit记录那里，因为那是属于master分支最新的commit记录

    git branch -d 分支名  //删除分支
<br/>需要注意，HEAD指向的分支无法删除，也就是我们所在的分支，需要先checkout切换到别的分支，再去删除之前的分支

<br/>我们删除了一个分支后，并不会删除这个分支上的提交记录，其实branch这个分支的概念，更确切地说是一个引用，是
一个指向，指向一串提交记录，我们删除了分支之后只是删除了对这个分支包含地提交记录地一个引用，虽然说我们没有删除
它们，但是Git地回收机制会定期清理那些不在任何分支上地commit记录

#### 2.merge 合并冲突
<font color=green size=5>git merge/ git merge --abort</font>

<br/>merge意思为合并，把目标分支合并到当前分支，一般在我们分支协作开发，某一分支地开发完成可以合并到主流程地
时候，这样去操作

<br/>实际行为是，从当前分支和要合并地目标分支地分叉点开始，将目标分支路径上的所有commit内容应用到当前的commit，
并且生成一个新的commit

<br/>我们在本地master分支上执行

    git merge v1.0
将v1.0分支上的两个commit记录4和5与本地分支上最新的commit3合并成一个了一个新的commit,如图中的6

![](https://ww1.sinaimg.cn/large/007iUjdigy1fyrj01qg74j30jk0ec0t1.jpg)

<br/>**冲突解决**
<br/>合并操作时，通常情况下git会帮我们自动合并两个分支上的改动，包括不同文件或者同一文件的不同位置的
修改，但既然是分支分开开发，免不了同时修改了相同的文件的同一内容，这时候就需要冲突合并

<br/>例如我从上图2的位置新建了分支v1.0，commit4和修改了test.txt文件，同时在master分支的commit3中也修改
了test.txt文件，这时我在master上运行merge远端的分支v1.0；test.txt出现了"merge conflict"合并冲突，自动
合并失败，要求"fix conflicts and then commit the result"把冲突解决后再提交

![](https://ae01.alicdn.com/kf/HTB1pFS1asfrK1RjSszc760GGFXaP.png)

<br/>**手动解决**
<br/>我们可以选择手动解决一下，打开文件(test.txt，即出现冲突的文件)，把不需要的分支的内容删掉(包括===》这些符号)，然后重新提交
这个冲突文件即可

![](https://ae01.alicdn.com/kf/HTB1Dz3hajLuK1Rjy0Fh760pdFXa7.png)

<br/>**放弃合并，撤消merge操作**

    git merge --abort  //取消
执行之后回去看冲突文件，就变成了merge之前的状态

<br/>如果当前分支的所有提交另一个分支都包含，就是在上图2的位置合并v1.0的内容，这时其实commit记录也就还是
一条线，1245，所以把本地的HEAD和分支(例子中是master)也指向v1.0的最新commit记录5就可以了

<br/>在遇到冲突的时候，git并不会自动为我们生成一个commit记录，而是我们修改完之后自己去提交这个记录

#### 3.rebase 避免出现分支的合并
<font color=green size=5>涉及到的命令：git rebase</font>

<br/>通过merge来协作开发，历史记录会出现很多分支，如果想避免这样导致混乱，可以采用rebase命令

    git rebase 目标分支
假设我们现在需要将v1.0的记录合并到master，并且丢弃现在有的分叉，执行：

    git checkout v1.0   //先切换到需要被合并的分支v1.0上
    git rebase master //向要合并进去的分支master发出rebase命令
实际上是我们需要被合并的分支v1.0，将其分叉点2重新设置为要被合并的目标分支master的最新commit3上，
4和5的基础点从2变成了3，同时我们所在的分支最新一条记录和HEAD都对应到合并后的最新的commit记录7上

![](https://i.loli.net/2019/01/02/5c2c5267a88a5.png)

![](https://i.loli.net/2019/01/02/5c2c5312661cc.png)
<br/>4和5因为没有分支引用指向它，之后会被Git回收机制清除

<br/>**为什么不在master上执行rebase**

<br/>在我们分支开发的时候，通常都是以master以核心的分支，如果我们在master上对v1.0执行rebase，
那么3和6就会指出新的接在5后面，3和6这两个commit在我们核心分支包含的路径中不存在了，现在master是124567，
这样协作开发的其余同事在push代码时，因为他们本地有3和6而远端master没有3和6，就是提交失败(具体原因和readme/.gitingore同理)

<br/>关于rebase，只要记住，它是修改需要被合并的分支的基础点，同时与merge相反，需要在被合并的分支上操作的指令

#### 4.修改被rebase分支的历史记录
<font color=green size=5>涉及到的命令：rebase -i/ git rebase --continue/ git rebase --abort</font>

<br/>如果我们想在rebase的过程中对一部分提交(commit)进行修改，可以在"git rebase"命令中加入`-i`或`-interactive`
参数去调用交互模式

<br/>假设我们要将v1.0上的commit记录的基础点重设为master分支的最后一条，同时希望修改我们接到master后面的v1.0
上提交信息

<br/>看下v1.0上的commit记录，最后一条是master上的提交，那次提交便是v1.0在master上的基础点：

![](https://i.loli.net/2019/01/02/5c2c6798ea253.png)

<br/>我们在v1.0上执行：`git rebase -i master`

![](https://i.loli.net/2019/01/02/5c2c68138ebeb.png)

<br/>接着我们进入了编辑页面，顶部列出了将要[被 rebase]的所有commit记录，也就是我们从master分支checkout出
v1.0分支后的两条提交记录。这个排列是正序的，和log显示的顺序相反
<br/>`pick`的意思是直接应用，我们如果要修改某次的提交信息，需要把提交信息改成edit，这样在应用到这条commit记录
时，Git会停下来让我们改正，假设我们要对这两条commit提交信息分别修改，在vim下将pick改成edit，然后输入`:wq!`或者
`ZZ`保存并退出

![](https://i.loli.net/2019/01/02/5c2c696e0e464.png)
<br/>这里Git在执行到"first commit"便停了下来，提示我们可以通过amend来修改这条commit记录，amend就是用来修改HEAD
所指向的这条最新记录，这个下面会讲。我们输入git commit --amend，然后进入编辑页面修改上条commit信息，保存

    git rebase --continue  //继续执行rebase
    git rebase --abort  //退出rebase过程
执行成功之后，我们log看下commit记录：

![](https://i.loli.net/2019/01/02/5c2c6b5fccf6e.png)
<br/>修改成功

<br/>**修改当前分支的历史记录**

<br/>对历史commit记录修改的功能，不仅适用在需要合分支的时候，我们也可以在当前分支进行原地操作，
直接对当前分支历史错误的提交进行修改

![](https://i.loli.net/2019/01/02/5c2c6c9541028.png)
<br/>假设我们要对这条commit记录进行更改，执行：

    git rebase -i HEAD^^  //也可以是HEAD~3
Git种有两个【偏移符号】：^和~
<br/>**a.^的用法**：表示对当前指针指向的commit记录向前偏移，偏移的数量就是^的数量，例如：HEAD^^，
表示的是HEAD所指向的那个commit往前2个的那条commit记录，也就是图中圈出来我们要修改的那个commit，
即可以修改的部分是HEAD到偏移2位中间的所有commit
<br/>**b.~的用法**：同样是当前指针的基础上往回偏移，偏移数量就是~后跟着的数字，效果与HEAD^^一样，
修改的方法与上述提到的修改方式一样

<br/>记得我们文章上说过rebase吗，它其实是对分支重设基础点的一个操作，在对别的分支操作时，
会找被rebase的分支和要rebase到的分支两个分支的交点，也就是被rebase的分支的一个基础点，
分叉点，然后对从基础点分叉出来的提交重新设置为要rebase到的分支最新一条记录
<br/>所以这里git rebase -i HEAD^^^，rebase后面跟着的是一个自己分支的某个提交记录，
实际上就是对rebase -i 后面跟着的那条记录开始（不包括开始点）往后的所有commit重新设置基础点，
把这些commit重新生成一遍再接在这个新的基础点后面，对于文件历史变化来说，这个其实就是空操作

#### 5.替换最近一条commit信息
<font color=green size=5>涉及到的命令：git commit --amend</font>

<br/>git commit --amend是对上一条commit命令进行修正。当我们执行这条命令时，Git会把暂存区的内容
和这次commit的新内容合并创建一个新的commit，把我们当前HEAD指向的最新的commit替换掉。例如我们当前
最新一条commit记录中，我们输入错了提交信息，想要修改，又或者我们提交错了一点东西，又不想生成一个新的
commit记录，我们都可以使用这个命令

<br/>在我们修改了错误文件test.txt后，执行：

    git add test.txt
然后我们commit这条修改：

    git commit --amend
接着我们又一次进入commit的提交信息编辑页面，修改完后保存退出，我们也可以用git log查看提交记录

<br/>amend用于修改上一条commit信息时，实际上并不是对一条commit修改，而是生成新的对它进行替换。我们看
我们操作的那条commit修改之前，和我们修改后生成的新的commit，id是完全不一样的（上面介绍过生成
方式），是两个不同的commit
<br/>所以这个时候如果我们对已经push到远端的commit记录在本地仓库进行--amend操作之后，直接push到远端
仓库是不会成功的，因为本地丢失了远端仓库的那个我们替换的commit
<br/>当然如果我们什么都不改直接保存，那就相当于空操作，老的commit就不会被替换了，还是它本身

#### 资料来源与阅读推荐
+ [原文链接](https://mp.weixin.qq.com/s?__biz=MzU0OTExNzYwNg==&mid=2247484483&idx=1&sn=db181ee210f9490379af3e4a74f0cde8&chksm=fbb58f8accc2069cbe9afda4fcf0222791aee3eefa56e3471d05f4ca7390464ea2ab66e472a0&token=942885247&lang=zh_CN&rd2werd=1#wechat_redirect)
+ [git merge](https://blog.csdn.net/andyzhaojianhui/article/details/78072143)
+ [本地对远程仓库的操作](https://blog.zengrong.net/post/1746.html)
