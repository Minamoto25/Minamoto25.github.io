# Simple git

恰好看到cs61b有一个关于git的tutorial，这里简单的记录一下。

1. `git int` 用于创建仓库。实际上初始化一个`staging area`，用于暂存`git add`添加的文件的`snapshot`，并且创建一个`commit`的链表，存储一系列版本。所谓`commit`，就是一些列tracked file的快照，git存储commit来实现对文件内容的记录，实现版本控制。

2. `git checkout`  可以传入一个commit的id，用于切换当前`HEAD`指针所指向的commit。当我们执行完git init以后，git为我们创建了两个指针。

   `master`：默认的分支指针，总是指向一个分支中最后一个commit。

   `HEAD`：当前目录下文件的实际内容，由HEAD指针所指向commmit决定。

3. `git status`：git实际保存的是commit，一个文件的状态可以有：untracked，modified，tracked。当一个文件刚被创建，未在staging area出现过时，处于untracked状态。被add以后但又被修改，处于modified状态。只有处于tacked状态，才不会使得status显示任何信息。checkout之前，必须keep git status clean。

4. `git add`实际会比较所添加的文件，和当前repo的commit的信息，如果没有改变，实际不会加入到staging area。

5. `git commit`实际执行的动作，首先复制上一个commit，然后根据staging area的内容做出修改，然后将head和master同时指向新创建的commit。

6. `git clone`将远程仓库中的所有commit下载到本地。克隆之后发现有一个指针`origin/master`，表明远程仓库在克隆时所处的状态。使用git remote将列出所有remote以及权限（pull/push），可以找到origin的实际位置。

7. `git push`将本地仓库的commit和远程仓库的同步，并且修改origin/master，远程仓库同步所有commit（并不是当前这一个），并且修改master指针。

----

这个tutorial没有涉及到分支的知识，对于多人协作的workflow的介绍如下。

1. 假设两个人A，B同时clone了一个仓库，并完成了各自部分的工作。A先push，B再push后会失败。于是B进行pull。pull的过程实际上是在远程仓库中找到origin/master，然后回溯到和本地仓库的分歧点，在将这一段commit拼接到本地的分歧点后，然后尝试自动合并，如果成功，git创建一个新的commit，内容是合并后的结果，并且commit按照时间顺序穿起来。
2. B自动合并成功，进行push。此时A进行pull，会发现从远程仓库中取到的分歧点--origin/master这一部分，正好拼接到了A当前master(HEAD)末尾，因此直接做fast forward合并即可。
3. 至此，A，B的状态达到同步，假设B做出改动，push。A在同一部分做出改动，push，失败，pull会merge conflict，此时A的文件被git标注，以进行手动合并。手动merge以后，commit push成功。

另外，可能存在一些细节谬误：

如果同时有多个remote，a，b。某种操作导致文件删除，此时如果pull a，可能无法恢复，因为本地仓库的head在a/master后面，不会导致合并。同时如果git add *，不会记录文件被删除这一事实，因为当前目录下找不到该文件。应该改git add <被删除文件名>。可以从指定commit中恢复文件：git checkout <id> -- <filename>，恢复的文件自动加入到了staging area。

![image-20221209191054456](/assets/image-20221209191054456.png)