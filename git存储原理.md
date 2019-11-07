## 聊聊Git

#### Git起源

​	2005 年，开发 BitKeeper 的商业公司同 Linux 内核开源社区的合作关系结束，他们收回了免费使用 BitKeeper 的权力，于是Linux 的缔造者 Linus    Torvalds由于没钱，再加上很气愤，便开发了自己可以免费使用的Git版本管理系统

#### Git系统

​	Git 究竟是怎样的一个系统呢？简单来说，Git 只关心文件数据的整体是否发生变化，而不关心文件内容的具体差异。Git 并不保存这些前后变化的差异数据。实际上，Git    更像是把变化的文件作快照后，记录在一个微型的文件系统中。每次提交更新时，它会纵览一遍所有文件的指纹信息并对文件作一快照，然后保存一个指向这次快照的索引。为提高性能，若文件没有变化，Git    不会再次保存，而只对上次保存的快照作一链接。

#### Git存储原理

​	Git是一套内存寻址文件系统，其存储内容都是通过内容地址维护，可以把它理解成一个键值对存储方式：即给定一个存储文件，该系统根据文件信息和内容，使用SHA-1算法计算，返回一个由40个十六进制字符组成的字符串，之后只需要通过该字符串即可访问该文件，这个字符串就是Git中通常所说的校验和，也就是commit-id。我们新建一个`readme.txt`文件，可以使用`git hash-object `命令查看该文件将会生成的key。

```
▶ git hash-object  readme.txt
# 40位长
e69de29bb2d1d6434b8b29ae775ad8c2e48c5391
```



​	当你在一个目录下执行git init命令时，会生成一个.git目录，它的目录结构是这样的：

```
.git/
├── branches
├── config
├── description
├── HEAD
├── hooks
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── prepare-commit-msg.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   └── update.sample
├── info
│   └── exclude
├── objects
│   ├── info
│   └── pack
└── refs
    ├── heads
    ├── tags
  	└── remotes
```

​	其中`branches`目录已经不再使用，`config`包含了项目的特有的配置信息；`description`包含了项目的描述信息，仅供GitWeb程序使用；`hooks`文件包含了各个git生命周期的钩子

​	需要我们重点关注的是`HEAD`和`refs目录` ` index文件` 以及`objects` 

* `objects`：存储所有文件数据信息

  这个目录是用来存储Git对象的，Git对象包含三种：数据(blob)对象、树(tree)对象、提交(commit)对象。Git文件系统的设计思路与linux文件系统相似，即将文件的内容与文件的属性分开存储，文件内容以“装满字节的袋子”存储在文件系统中，文件名、所有者、权限等文件属性信息则另外开辟区域进行存储。在Git中，数据对象相当于文件内容，树对象相当于文件目录树，提交对象则是对文件系统的快照。

  下面的内容，会分别对每种对象进行说明。开始说明之前，先初始化一个Git文件系统：

  ```
  $ mkdir git-test
  $ cd git-test
  $ git init
  ```

  * 数据对象

    数据对象是文件的内容，不包括文件名、权限等信息，<u>***Git会根据文件内容计算出一个hash值，以hash值作为文件索引存储在Git文件系统中。由于相同的文件内容的hash值是一样的，因此Git将同样内容的文件只会存储一次***</u>。`git hash-object`可以用来计算文件内容的hash值，并将生成的数据对象存储到Git文件系统中

    ```
    $ echo 'version 1' | git hash-object -w --stdin
    83baae61804e65cc73a7201a7252750c76066a30
    
    $ git cat-file -p 83baae61804e65cc73a7201a7252750c76066a30
    version 1
    ```

    到此，我们能够对Git文件系统中的数据对象进行读写。但是，我们需要记住每一个数据对象的hash值，才能访问到Git文件系统中的任意数据对象，这显然是不现实的。数据对象只是解决了文件内容存储的问题，而文件名的存储则需要通过下一节的树对象来解决

  * 树对象

    树对象是文件目录树，记录了文件获取目录的名称、类型、模式信息。使用`git update-index`可以为数据对象指定名称和模式，然后使用`git write-tree`将树对象写入到Git文件系统中。

    ```
    $ git update-index --add --cacheinfo 100644 83baae61804e65cc73a7201a7252750c76066a30 test.txt
    $ git write-tree
    d8329fc1cc938780ffdd9f94e0d364e0ea74f579
    ```

    树对象解决了文件名的问题，而且，由于我们是分阶段提交树对象的，树对象可以看做是开发阶段源代码目录树的一次次快照，因此我们可以是用树对象作为源代码版本管理。但是，这里仍然有问题需要解决，即我们需要记住每个树对象的hash值，才能找到个阶段的源代码文件目录树。在源代码版本控制中，我们还需要知道谁提交了代码、什么时候提交的、提交的说明信息等，接下来的提交对象就是为了解决这个问题的。

  * 提交对象

    提交对象是用来保存提交的作者、时间、说明这些信息的，可以使用`git commit-tree`来将提交对象写入到Git文件系统中

    ```
    $ echo 'first commit' | git commit-tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579
    db1d6f137952f2b24e3c85724ebd7528587a067a
    ```

    上面`commit-tree`除了要指定提交的树对象，也要提供提交说明，至于提交的作者和时间，则是根据环境变量自动生成，并不需要指定。这也是为什么每次生成的git commit id不一样的原因，因为即使其他所有的信息都相同，但是只要提交的时间不同，就会产生不同的key。

    提交对象的查看，也是使用`git cat-file`

    ```
    $ git cat-file -p db1d6f137952f2b24e3c85724ebd7528587a067a
    tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579
    author szh <xxx@qq.com> 1571332681 +0800
    committer szh <xxx@qq.com> 1571432681 +0800
    
    first commit
    ```

    我们总结一下，`git add`命令会为加入暂存区的内容或文件生成blob对象，`git commit`命令会为加入版本库的内容或文件生成tree对象和commit对象.

  * git对象的hash方法

    Git中的数据对象、树对象和提交对象的hash方法原理是一样的，可以描述为：

    ```
    header = "<type> " + content.length + "\0"
    hash = sha1(header + content)
    ```

    上面公式表示，Git在计算对象hash时，首先会在对象头部添加一个`header`。这个`header`由3部分组成：第一部分表示对象的类型，可以取值`blob`、`tree`、`commit`以分别表示数据对象、树对象、提交对象；第二部分是数据的字节长度；第三部分是一个空字节，用来将`header`和`content`分隔开。将`header`添加到`content`头部之后(二进制拼接)，使用`sha1`算法计算出一个40位的hash值。

  * git对象存储

    数据对象、树对象和提交对象都是存储在`.git/objects`目录下，目录的结构如下：

    ```
    .git
    |-- objects
        |-- 01
        |   |-- 55eb4229851634a0f03eb265b69f5a2d56f341
        |-- 1f
        |   |-- 7a7a472abf3dd9643fd615f6da379c4acb3e3a
        |-- 83
            |-- baae61804e65cc73a7201a7252750c76066a30
    ```

    从上面的目录结构可以看出，Git对象的40位hash分为两部分：头两位作为文件夹，后38位作为对象文件名。所以一个Git对象的存储路径规则为：

    ```
    .git/objects/hash[0, 2]/hash[2, 40]
    ```

 * `HEAD` : 存储HEAD指针当前的指向

   ​	形象的讲，HEAD就是景区地图上标注你当前在哪里的一个图标。你当前在哪里，HEAD就在哪里。它一般指向某个分支，因为一般我们都会在某个分支之上。因为HEAD是用来标注当前位置的，所以一旦HEAD的位置被改变，工作目录就会切换到HEAD指向的分支。

 * `refs`：存储所有`heads`、`tags`和`remotes`的信息

   * HEAD引用

     用来指向每个分支的最后一次提交对象，这样切换到一个分支之后，才能知道分支的“尾巴”在哪里。HEAD引用存储在`.git/refs/heads`目录下，有多少个分支，就有相应的同名HEAD引用对象。例如代码库里面有`master`和`test`两个分支，那么`.git/refs/heads`目录下就存在`master`和`test`两个文件，分别记录了分支的最后一次提交。

     `.git/refs/heads`目录下存储了每个分支的HEAD，那怎么知道代码库当前处于哪个分支呢?

     ```
     $ cat .git/HEAD
     ref: refs/heads/master
     
     $ git checkout test
     $ cat .git/HEAD
     ref: refs/heads/test
     ```

     切换分支后，`.git/HEAD`文件的内容也跟着指向`.git/refs/heads/test`。`.git/HEAD`也是HEAD引用对象.

     至此，我们分析了两种HEAD引用，一种是分支级别的HEAD引用，用来记录各分支的最后一次提交，存储在`.git/refs/heads`目录下，使用`git update-ref`来维护；一种是代码库级别的HEAD引用，用来记录代码库所处的分支，存储在`.git/HEAD`文件，使用`git symbolic-ref`来维护

   * 标签引用

     顾名思义就是给Git对象打标签

   * 远程引用

     类似于`.git/refs/heads`中存储的本地仓库各分支的最后一次提交，在`.git/refs/remotes`是用来记录多个远程仓库各分支的最后一次提交。

     

 * `index`：存储暂存区的信息（在第一次`git add`后才会出现这个文件)

   ​	暂存区并不是一块区域，只是一个文件，确切的说，是一个索引文件。它保存了项目结构、文件名、时间戳以及blob对象的引用。工作区的文件和blob对象之间就是通过这个索引文件关联起来的。
   
   



