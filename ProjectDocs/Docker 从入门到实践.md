**Docker** 最初是 `dotCloud` 公司创始人 [Solomon Hykes (opens new window)](https://github.com/shykes)在法国期间发起的一个公司内部项目.

[Docker — 从入门到实践](https://yeasy.gitbook.io/docker_practice/image/pull)


# 获取镜像

[Docker Hub](https://hub.docker.com/search?q=&type=image) 
$ docker pull [选项] \[Docker Registry 地址[:端口号]/]仓库名[:标签]

具体的选项可以通过 `docker pull --help` 命令看到，

地址的格式一般是 `<域名/IP>[:端口号]`。默认地址是 Docker Hub(`docker.io`)。
```bash
比如：
$ docker pull ubuntu:18.04  
# Docker Hub （`docker.io`） 官方镜像 `library/ubuntu` 仓库

18.04: Pulling from library/ubuntu
92dc2a97ff99: Pull complete
be13a9d27eb8: Pull complete
c8299583700a: Pull complete
Digest: sha256:4bc3ae6596938cb0d9e5ac51a1152ec9dcac2a1c50829c74abd9c4361e321b26
Status: Downloaded newer image for ubuntu:18.04
docker.io/library/ubuntu:18.04  # 镜像的完整名称  

# 镜像是由多层存储所构成。
```

你所看到的层 ID 以及 `sha256` 的摘要和这里的不一样。这是因为官方镜像是一直在维护的。

## 运行

如果我们打算启动里面的 `bash` 并且进行交互式操作的话，可以执行下面的命令。
```bash

$ docker run -it --rm ubuntu:18.04 bash

root@e7009c6ce357:/# cat /etc/os-release
NAME="Ubuntu"
VERSION="18.04.1 LTS (Bionic Beaver)"
ID=ubuntu
......

```

参数
- `-it`：这是两个参数， `-i`：交互式操作，`-t` 终端。我们这里打算进入 `bash` 执行一些命令并查看返回结果，因此我们需要交互式终端。
- `--rm`：容器退出后随之将其删除。
- `ubuntu:18.04`：用 `ubuntu:18.04` 镜像为基础来启动容器。
- `bash`：放在镜像名后的是 **命令**，用的是 `bash`。
 `cat /etc/os-release`，查看当前系统版本的命令，

# 列出镜像

要想列出已经下载下来的镜像，可以使用 `docker image ls` 命令。
```bash
$ docker image ls

REPOSITORY TAG IMAGE ID CREATED SIZE

redis latest 5f515359c7f8 5 days ago 183 MB

ubuntu 18.04 329ed837d508 3 days ago 63.3MB

ubuntu bionic 329ed837d508 3 days ago 63.3MB

```
 `仓库名`、`标签`、`镜像 ID`、`创建时间` 以及 `所占用的空间`。

**镜像 ID** 则是镜像的唯一标识，一个镜像可以对应多个 **标签**。
可以看到 `ubuntu:18.04` 和 `ubuntu:bionic` 拥有相同的 ID，因为它们对应的是同一个镜像。

## 镜像体积

比如，`ubuntu:18.04` 镜像大小，在这里是 `63.3MB`，但是在 [Docker Hub](https://hub.docker.com/layers/ubuntu/library/ubuntu/bionic/images/sha256-32776cc92b5810ce72e77aca1d949de1f348e1d281d3f00ebcc22a3adcdc9f42?context=explore) 却是 `25.47 MB`。这是因为 Docker Hub 中显示的体积是压缩后的体积。

	在镜像下载和上传过程中镜像是保持着压缩状态的，因此 Docker Hub 所显示的大小是网络传输中更关心的流量大小。
	而 `docker image ls` 显示的是镜像下载到本地后，展开的大小，准确说，是展开后的各层所占空间的总和，因为镜像到本地后，查看空间的时候，更关心的是本地磁盘空间占用的大小。

	`另外一个需要注意的问题是，`docker image ls` 列表中的镜像体积总和并非是所有镜像实际硬盘消耗。由于 Docker 镜像是多层存储结构，并且可以继承、复用，因此不同镜像可能会因为使用相同的基础镜像，从而拥有共同的层。由于 Docker 使用 Union FS，相同的层只需要保存一份即可，因此实际镜像硬盘占用空间很可能要比这个列表镜像大小的总和要小的多。
`
 `docker system df` 命令来便捷的查看镜像、容器、数据卷所占用的空间。
``` bash
$ docker system df
```

## 虚悬镜像
下面的镜像列表中，还可以看到一个特殊的镜像，这个镜像既没有仓库名，也没有标签，均为 `<none>`。：
```bash
<none> <none> 00285df0df87 5 days ago 342 MB
```
`这个镜像原本是有镜像名和标签的，原来为 `mongo:3.2`，随着官方镜像维护，发布了新版本后，重新 `docker pull mongo:3.2` 时，`mongo:3.2` 这个镜像名被转移到了新下载的镜像身上，而旧的镜像上的这个名称则被取消，从而成为了 `\<none>`。除了 `docker pull` 可能导致这种情况，`docker build` 也同样可以导致这种现象。由于新旧镜像同名，旧镜像名称被取消，从而出现仓库名、标签均为 `\<none>` 的镜像。`
这类无标签镜像也被称为 **虚悬镜像(dangling image)** ，
```bash
$ docker image ls -f dangling=true
REPOSITORY TAG IMAGE ID CREATED SIZE
<none> <none> 00285df0df87 5 days ago 342 MB
一般来说，虚悬镜像已经失去了存在的价值，是可以随意删除的，可以用下面的命令删除。
$ docker image prune
```

## 中间层镜像

为了加速镜像构建、重复利用资源，Docker 会利用 **中间层镜像**。所以在使用一段时间后，可能会看到一些依赖的中间层镜像。默认的 `docker image ls` 列表中只会显示顶层镜像，如果希望显示包括中间层镜像在内的所有镜像的话，需要加 `-a` 参数。
```bash
$ docker image ls -a
```
这样会看到很多无标签的镜像，与之前的虚悬镜像不同，这些无标签的镜像很多都是中间层镜像，是其它镜像所依赖的镜像。
相同的层只会存一遍，只要删除那些依赖它们的镜像后，这些依赖的中间层镜像也会被连带删除。

## 列出部分镜像

根据仓库名列出镜像
```bash
$ docker image ls ubuntu

REPOSITORY TAG IMAGE ID CREATED SIZE
ubuntu 18.04 329ed837d508 3 days ago 63.3MB
ubuntu bionic 329ed837d508 3 days ago 63.3MB
列出特定的某个镜像，也就是说指定仓库名和标签

$ docker image ls ubuntu:18.04

REPOSITORY TAG IMAGE ID CREATED SIZE
ubuntu 18.04 329ed837d508 3 days ago 63.3MB
```

过滤器参数 `--filter`，或者简写 `-f`。
比如，我们希望看到在 `mongo:3.2` 之后建立的镜像，可以用下面的命令：
```bash
$ docker image ls -f since=mongo:3.2

REPOSITORY TAG IMAGE ID CREATED SIZE
redis latest 5f515359c7f8 5 days ago 183 MB
nginx latest 05a60462f8ba 5 days ago 181 MB


想查看某个位置之前的镜像也可以，只需要把 `since` 换成 `before` 即可。
此外，如果镜像构建时，定义了 `LABEL`，还可以通过 `LABEL` 来过滤。

$ docker image ls -f label=com.example.version=0.1
...
```

## 以特定格式显示

`docker image rm` 命令作为参数来删除指定的这些镜像，这个时候就用到了 `-q` 参数。
```bash

$ docker image ls -q
5f515359c7f8
05a60462f8ba
fe9198c04d62
00285df0df87
329ed837d508
329ed837d508
```

 [Go 的模板语法](https://gohugo.io/templates/introduction/)。
比如，下面的命令会直接列出镜像结果，并且只包含镜像ID和仓库名：
```bash
$ docker image ls --format "{{.ID}}: {{.Repository}}"
5f515359c7f8: redis
05a60462f8ba: nginx
fe9198c04d62: mongo
00285df0df87: <none>
329ed837d508: ubuntu
329ed837d508: ubuntu
C:\Users\31093>docker images --format 
	"{{.Repository}} :: {{.Tag}} ->> {{.ID}} .{{.Size}}"
redis :: latest ->> 8e69fcb59ff4 .130MB
tomcat :: latest ->> fb5657adc892 .680MB
mysql :: latest ->> 3218b38490ce .516MB
ubuntu :: 18.04 ->> 5a214d77f5d7 .63.1MB
```

以表格等距显示，并且有标题行，和默认一样，不过自己定义列：
```bash

$ docker image ls --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
IMAGE ID REPOSITORY TAG
5f515359c7f8 redis latest
05a60462f8ba nginx latest
fe9198c04d62 mongo 3.2
00285df0df87 <none> <none>
329ed837d508 ubuntu 18.04
329ed837d508 ubuntu bionic

C:\Users\31093>docker images --format "table {{.ID}}::{{.Tag}} ->> {{.Repository}}"
IMAGE ID::TAG ->> REPOSITORY
8e69fcb59ff4::latest ->> redis
fb5657adc892::latest ->> tomcat
3218b38490ce::latest ->> mysql
5a214d77f5d7::18.04 ->> ubuntu
```

# 删除本地镜像
如果要删除本地的镜像，可以使用 `docker image rm` 命令，其格式为：

$ docker image rm [选项] <镜像1> [<镜像2> ...]

## 

用 ID、镜像名、摘要删除镜像[](https://yeasy.gitbook.io/docker_practice/image/rm#yong-id-jing-xiang-ming-zhai-yao-shan-chu-jing-xiang)

其中，`<镜像>` 可以是 `镜像短 ID`、`镜像长 ID`、`镜像名` 或者 `镜像摘要`。

比如我们有这么一些镜像：

$ docker image ls

REPOSITORY TAG IMAGE ID CREATED SIZE

centos latest 0584b3d2cf6d 3 weeks ago 196.5 MB

redis alpine 501ad78535f0 3 weeks ago 21.03 MB

docker latest cf693ec9b5c7 3 weeks ago 105.1 MB

nginx latest e43d811ce2f4 5 weeks ago 181.5 MB

我们可以用镜像的完整 ID，也称为 `长 ID`，来删除镜像。使用脚本的时候可能会用长 ID，但是人工输入就太累了，所以更多的时候是用 `短 ID` 来删除镜像。`docker image ls` 默认列出的就已经是短 ID 了，一般取前3个字符以上，只要足够区分于别的镜像就可以了。

比如这里，如果我们要删除 `redis:alpine` 镜像，可以执行：

$ docker image rm 501

Untagged: redis:alpine

Untagged: redis@sha256:f1ed3708f538b537eb9c2a7dd50dc90a706f7debd7e1196c9264edeea521a86d

Deleted: sha256:501ad78535f015d88872e13fa87a828425117e3d28075d0c117932b05bf189b7

Deleted: sha256:96167737e29ca8e9d74982ef2a0dda76ed7b430da55e321c071f0dbff8c2899b

Deleted: sha256:32770d1dcf835f192cafd6b9263b7b597a1778a403a109e2cc2ee866f74adf23

Deleted: sha256:127227698ad74a5846ff5153475e03439d96d4b1c7f2a449c7a826ef74a2d2fa

Deleted: sha256:1333ecc582459bac54e1437335c0816bc17634e131ea0cc48daa27d32c75eab3

Deleted: sha256:4fc455b921edf9c4aea207c51ab39b10b06540c8b4825ba57b3feed1668fa7c7

我们也可以用`镜像名`，也就是 `<仓库名>:<标签>`，来删除镜像。

$ docker image rm centos

Untagged: centos:latest

Untagged: centos@sha256:b2f9d1c0ff5f87a4743104d099a3d561002ac500db1b9bfa02a783a46e0d366c

Deleted: sha256:0584b3d2cf6d235ee310cf14b54667d889887b838d3f3d3033acd70fc3c48b8a

Deleted: sha256:97ca462ad9eeae25941546209454496e1d66749d53dfa2ee32bf1faabd239d38

当然，更精确的是使用 `镜像摘要` 删除镜像。

$ docker image ls --digests

REPOSITORY TAG DIGEST IMAGE ID CREATED SIZE

node slim sha256:b4f0e0bdeb578043c1ea6862f0d40cc4afe32a4a582f3be235a3b164422be228 6e0c4c8e3913 3 weeks ago 214 MB

​

$ docker image rm node@sha256:b4f0e0bdeb578043c1ea6862f0d40cc4afe32a4a582f3be235a3b164422be228

Untagged: node@sha256:b4f0e0bdeb578043c1ea6862f0d40cc4afe32a4a582f3be235a3b164422be228

## 

Untagged 和 Deleted[](https://yeasy.gitbook.io/docker_practice/image/rm#untagged-he-deleted)

如果观察上面这几个命令的运行输出信息的话，你会注意到删除行为分为两类，一类是 `Untagged`，另一类是 `Deleted`。我们之前介绍过，镜像的唯一标识是其 ID 和摘要，而一个镜像可以有多个标签。

因此当我们使用上面命令删除镜像的时候，实际上是在要求删除某个标签的镜像。所以首先需要做的是将满足我们要求的所有镜像标签都取消，这就是我们看到的 `Untagged` 的信息。因为一个镜像可以对应多个标签，因此当我们删除了所指定的标签后，可能还有别的标签指向了这个镜像，如果是这种情况，那么 `Delete` 行为就不会发生。所以并非所有的 `docker image rm` 都会产生删除镜像的行为，有可能仅仅是取消了某个标签而已。

当该镜像所有的标签都被取消了，该镜像很可能会失去了存在的意义，因此会触发删除行为。镜像是多层存储结构，因此在删除的时候也是从上层向基础层方向依次进行判断删除。镜像的多层结构让镜像复用变得非常容易，因此很有可能某个其它镜像正依赖于当前镜像的某一层。这种情况，依旧不会触发删除该层的行为。直到没有任何层依赖当前层时，才会真实的删除当前层。这就是为什么，有时候会奇怪，为什么明明没有别的标签指向这个镜像，但是它还是存在的原因，也是为什么有时候会发现所删除的层数和自己 `docker pull` 看到的层数不一样的原因。

除了镜像依赖以外，还需要注意的是容器对镜像的依赖。如果有用这个镜像启动的容器存在（即使容器没有运行），那么同样不可以删除这个镜像。之前讲过，容器是以镜像为基础，再加一层容器存储层，组成这样的多层存储结构去运行的。因此该镜像如果被这个容器所依赖的，那么删除必然会导致故障。如果这些容器是不需要的，应该先将它们删除，然后再来删除镜像。

## 

用 docker image ls 命令来配合[](https://yeasy.gitbook.io/docker_practice/image/rm#yong-docker-image-ls-ming-ling-lai-pei-he)

像其它可以承接多个实体的命令一样，可以使用 `docker image ls -q` 来配合使用 `docker image rm`，这样可以成批的删除希望删除的镜像。我们在“镜像列表”章节介绍过很多过滤镜像列表的方式都可以拿过来使用。

比如，我们需要删除所有仓库名为 `redis` 的镜像：

$ docker image rm $(docker image ls -q redis)

或者删除所有在 `mongo:3.2` 之前的镜像：

$ docker image rm $(docker image ls -q -f before=mongo:3.2)

充分利用你的想象力和 Linux 命令行的强大，你可以完成很多非常赞的功能。