## Git(下)

### 1.丢失最新的提交
<font color=green size=5>涉及到的命令：git reset</font>
  1. 最新一次的commit的内容有问题，想要丢弃这次提交，先log看下提交记录：

  &emsp;&ensp;![](https://ae01.alicdn.com/kf/HTB1kmK8asfrK1RkSnb4760HRFXaw.png)
  <br/>如果想要恢复到HEAD前面的那次commit记录，也就是first commit 2，执行：

    git reset HEAD~1  //或者使用 git reset HEAD^ 前面说过它们的效果是一样的
  执行看下结果，log看下提交记录：

  &emsp;&ensp;![](https://i.loli.net/2019/01/02/5c2c92615c004.png)
  <br/>这里需要注意的是，被撤消的那条提交并没有消失，只是log不再展现出来，因为已被丢弃。如果在撤消
  它之前记下了它的SHA-1码，那么还可以通过这个编码找到它，执行如下：

  &emsp;&ensp;![](https://i.loli.net/2019/01/02/5c2c97d722dd5.png)
  <br/>这个时候，我们查看一下本地仓库的状态，发现"nothing to commit",不再是"Untracked files"的状态了

  2. <font color=green size=5>参数：--hard &ensp;  --soft &ensp; --mixed</font>
  <br/>回顾一下，我们先前讲的内容：工作目录(working area)，暂存区(index)和本地版本库(HEAD)的区别，
  这里reset后面跟的参数影响的正是这三者内部的数据状态

      1. <font color=green size=4>git reset --soft</font>
      <br/>执行这句命令时，实际上我们只是把本地版本库，指向了我们要指向的那个commit，而暂存区和本地
      工作目录一致，保留着我们的文件修改，操作看下：

      &emsp;&ensp;&ensp;![](https://i.loli.net/2019/01/02/5c2c9ba349754.png)
      <br/>执行完，status看下工作区状态，我们可以看到现在我们的暂存区有一个待commit的文件，证明现在
      本地版本库和暂存区是不一致的，而这个不一致刚好是我们丢弃的那次commit修改的内容，同时我们并没有
      看到有文件是"changes not staged for commit"，说明当前我们的工作目录和暂存区文件状态是一致的
      <br/>**总结如下**：HEAD(本地版本库) != INDEX(暂存区文件内容)；INDEX=WORKING AREA(本地工作目录)

      2. <font color=green size=4>git reset --hard</font>
      <br/>执行这句命令时，不仅本地版本库会指向我们指定的commit记录，同时暂存区和本地工作目录也会同步
      变化成我们制定的commit记录状态，期间所有的更改全部丢失，操作看下：

      &emsp;&ensp;&ensp;![](https://i.loli.net/2019/01/02/5c2c9fd435a35.png)
      <br/>执行完我们看到，暂存区和工作目录都没有文件记录
      <br/>**总结如下**：HEAD(本地版本库) == INDEX(暂存区文件内容) == WORKING AREA(本地工作目录)

      3. <font color=green size=4>git reset --mixed(default)</font>
      <br/>--mixed是reset的默认参数，也就是当我们不指定任何参数时的参数。它将我们本地版本库指向我们
      制定的commit记录，同时暂存区也同步变化，而本地工作目录并不变化，所有我们丢弃的commit记录涉及的
      文件更改都会保存在本地工作目录working area中，所以数据不会丢失，但是所有改动都未被添加进暂存区，
      方便我们检查修改，操作看下：

      &emsp;&ensp;&ensp;![](https://i.loli.net/2019/01/02/5c2ca681178ad.png)
      <br/>执行完我们看到，在工作目录中有文件修改，而暂存区和本地版本库与我们指定的commit记录保持一致
      <br/>**总结如下**：HEAD(本地版本库) == INDEX(暂存区文件内容)；HEAD(本地版本库) != WORKING AREA
      (本地工作目录)

### 2.丢弃历史中某一条提交
<font color=green size=5>涉及到的命令：git rebase -i/ git rebase --onto</font>

<br/>上面我们说到reset可以让我们回归到历史的某条commit记录，但是我们从那条记录之后的记录就被丢弃，如果我们
只想丢弃历史记录中的某一条而不影响其之后的记录要怎么做呢？
<br/>还是通过git rebase。这里不放在rebase的部分一起说是因为rebase的这个用法，在reset之后来讲会方便理解

   1. <font color=green size=4>git rebase -i</font>

   &emsp;&ensp;&ensp;![](https://i.loli.net/2019/01/02/5c2cb039f0cb2.png)
   <br/>假设这里我们想要撤掉圈出来的second commit 2，回想一下上面说的交互式rebase -i，我们把基础点设置
   成我们丢弃的commit前面的commit(实际上只要设置成包含我们删除的记录们的前面的任意一条都可以)

    git rebase -i HEAD~2  //-i后跟着的是我们要删除的记录前面的任意记录  设为基础点
   &emsp;&ensp;&ensp;![](https://i.loli.net/2019/01/02/5c2cb2229b785.png)
   <br/>进入编辑页后，i进入插入模式，我们之前修改commit是将pick(直接应用)修改为edit，这次要撤消某条记录，
   我们直接把该记录删除

   &emsp;&ensp;&ensp;![](https://i.loli.net/2019/01/02/5c2cb32f1221b.png)
   <br/>保存后退出，我们会看到成功的提示，也可以用git log查看提交历史，同时工作目录和本地版本库也会删除该
   文件

   &emsp;&ensp;&ensp;![](https://i.loli.net/2019/01/02/5c2cb69450baa.png)

   2. <font color=green size=4>git rebase --onto</font>
   <br/>我们之前在对分支执行rebase时，是选择所在分支与目标的交叉点作为起点，把所在分支从这个起点到最新的
   commit记录接到目标分支的结尾
   <br/>而rebase --onto可以帮助我们指定这个起点，从新起点到所在分支最新记录之前的commit记录才接到目标分支上

   <br/>假设我们在1245这条分支上，对123(master)执行rebase不带任何参数，默认我们所在分支的起点是2，2后面de4
   和5(v1.0)会复制出来一个6和7接在3后面
   <br/>如果我们只想把5提交到3上，不想附带上4，那么我们就可以执行：

    git rebase --onto 3 4 5 //345分别是commit记录的代指
   --onto参数后面有三个附加参数：目标commit(要接在哪次记录后面)、起点commit(起点排除在外，从起点之后
   的记录)、终点commit。所以上面这行指令就会从4(不包括4)往下数，一直数到5，把中间涵盖的所有commit记录，重新
   提交到3上去
   <br/>假设我们要丢失当前分支倒数第二个提交，HEAD^对应的那个，那么我们只要执行：

    git rebase --onto HEAD^^ HEAD^ HEAD
  这句的意思是，以倒数第三个新的目标点，从倒数第二个不包括倒数第二个的commit记录开始，到HEAD之间的(本例中只有
  HEAD一个)接到新的目标点之后，所有倒数第二个就被跳过，直接最新的接在倒数第三给后面

### 3.生成一条新提交的撤销操作
<font color=green size=5>涉及到的命令：git revert</font>

<br/>在我们已经push到远端仓库后发现有一条commit记录对应的修改应该被删除时，我们可以再用上面的操作方式在本地
仓库操作删除那条记录，再推送到远端，再推送到远端，但是注意，因为我们是删除了一条记录，所以在我们推送远端仓库
的时候，会因为我们本地没有远端对应的那条记录而提示push失败

<br/>这时，如果你本来就希望用本地的内容覆盖掉中央仓库的内容，并且有足够的把握不会影响别的同事的代码，那么就不
要按照提示先pull再push了，而是选择[强行]push:

    git push origin 分支名 -f //-f  force 强制
但是在我们分支协作开发时，在向master分支提交代码时，是不应该用`-f`的，因为这样很容易让我们本地的内容覆盖
或者影响同事们提交上去的代码。那这个时候如果我们想要撤销某次提交对应的改动要怎么办呢？

<br/>生成一条与我们要撤销的那条记录相反的新的commit记录：

    git revert 要撤销的改动对应的commit记录
git revert会生成一个与后面对应的commit记录相反的一次文件提交，从而将那次修改抵消，达到撤销的效果
<br/>执行revert后再push到远端，我们文件内容就恢复了。revert的方式并不会造成某条记录在历史记录中消失，而
只是生成一个新的与我们要撤销的记录相反的文件提交而已

### 4.分支与HEAD分离
<font color=green size=5>涉及到的命令：git checkout</font>

<br/>上面我们讲到，在我们执行`git checkout branchName`的时候，HEAD会指向这个分支，同时两者都指向这个分支
最新的那条commit记录。其实我们操作的这些分支，就是我们的一种理解，本质上它也是一个指针，对应着commit记录，
所以checkout后面也可以直接指定某一条commit记录
<br/>但是不一样的是，在我们checkout到某一条特定的commit记录时，HEAD和分支就脱离了，我们就只是让HEAD指向了
我们指定的记录，而所在的分支的指针并没有同步过来

<br/>checkout本质上的功能其实是迁出到指定的commit记录的那一种操作

![](https://i.loli.net/2019/01/02/5c2cb69450baa.png)
<br/>可以看到我们所在的“分支”也变成了hEAD指向的commit记录的id

    git checkout --detach
<br/>执行这行代码，Git就会把HEAD和branch原地脱离，直接指向当前commit

### 5.临时存放工作目录的改动
<font color=green size=5>涉及到的命令：git stash</font>

<br/>工作中可能经常遇到我们现在某个分支开发，突然需要切换到master发个包或者到别的分支做些修复，但是我们本地改了
一半的东西又不想先提交(例如：可能会有改了一半的bug，推上去的话搞得一起在这个分支的同时拉下来项目都跑不起来)，为了
防止带到别的分支同时不用提交到远端分支，又不丢弃自己现在的改动，我们可以借用stash

    git stash  //临时保存工作区的改动
stash指令可以帮你把工作目录的内容全部放在你本地的一个独立的地方，不是暂存区，它不会被提交，也不会被删除，同时你的
工作目录已经清理干净，可以随时切换分支，等之后需要的时候，再回到这个分支把这部分改动取出来

    git stash pop  //取出工作区的改动
这里注意，我们untracked的文件，是不在本地仓库追踪记录里面的，自然stash的时候会忽略它们，这时如果想要
stash一起保存这些未追踪的文件，我们可以

    git stash -u  //--include-untracked 的简写，将untracked的文件一并临时存储

### 6.操作记录恢复
<font color=green size=5>涉及到的命令：git reflog</font>

<br/>它是Git仓库中引用的移动记录，如果不指定引用，git log默认展示HEAD所指向的一个顺序的提交列表。它不是本地仓库
的一部分，它单独存储，而且push，fetch或者clone时都不包括它，它只是本地的一个操作记录

![](https://i.loli.net/2019/01/02/5c2ccda12ffc7.png)
<br/>每行都由版本号，HEAD值和操作描述三部分组成
+ 版本号：这次操作的一个id
+ HEAD值：同样用来标识版本，但是不同于版本号的时，HEAD值是相对的。括号里的值越小，表示版本越新
+ 描述：操作行为的描述，包括我们commit的信息或者分支的切换等

<br/>reflog为每一条操作显示其对应的id版本号，这个id可以很好地帮助我们你恢复误操作的数据，例如我们错误地
reset了一个旧的提交，或者rebase等操作，这个时候我们可以使用reflog去查看在误操作之前的信息，并且使用
git reset 版本号，去恢复之前的某一次操作状态，操作过后依然可以在reflog中看到状态之后的所有操作记录

<br/>区别于git log，log显示的是所有本地版本库的提交信息，commit记录，且不能察看已经删除了的commit记录。
而git reflog可以查看所有分支的所有操作记录（包括commit和reset的操作），包括已经被删除的commit记录，
几乎所有的操作都记录在其中，随时可以回滚。

### 7.补充：vim的操作
1. 输入i进入插入模式
2. 按下ESC键，退出编辑模式，切换到命令模式
3. 保存修改并且退出 vim："ZZ"或者":wq"
4. 保存文件，不退出vim：":w"
5. 放弃修改并退出vim：":q!"
6. 放弃所有文件修改，但不退出 vim：":e!"


### 8.资料来源与阅读推荐
+ [原文链接](https://mp.weixin.qq.com/s/K7004_PVFW0kj8vcFh0s6Q)