## 1. 什么是wget

目前它支持HTTP、HTTPS，月以及FTP

## 2. 特点

它的主要特点包括：

- 支持递归下载
- 恰当地转换页面中的连接
- 生成可在本地浏览的页面镜像
- 支持代理服务器
## 3. 安装

Linux系统上的安装（以Ubuntu为例）

```text
sudo apt-get install wget
```

## 4.使用方法

> 注释：这里以[清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/)的文件为例。

1. **使用wget下载单个文件：**

```text
wget https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.10/ubuntu-20.10-desktop-amd64.iso
```

并保存到当前文件夹（打开终端的所在文件夹）

2. **使用wget -O下载并以不同的文件名保存**

wget默认会以最后一个符合'/'的后面的字符来命名，那么如果一个动态链接的下载通常会有问题。那么需要自己定义一个名字，下面是将上述的名字定义为ubuntu的下载：

```text
wget -O https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.10/ubuntu-20.10-desktop-amd64.iso
```

**3. wget还可以限定速度下载**

下面的命令是将下载速度限制在500k/s的速度：

```text
wget –limit-rate=500k https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.10/ubuntu-20.10-desktop-amd64.iso
```

**4. 断续下载**

在一般下在中，最让人头疼的是下载这网络不稳定，突然断网了，那么最好用下面的断续下在：

```text
wget -c https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.10/ubuntu-20.10-desktop-amd64.iso
```

**5. 下载整个网站**

如果你想下载某个不错的个人网站（当然该网站支持wget下载），而内容很多的情况下，不能逐个下载，那么就可以使用wget的递归功能来下载整个网站：

```text
wget -c -r -np -k -L -p https://***.org/path/
```

上述的https://***.org/path/是要下载的网址。

如果在该网址中，有一些需其它连接的数据， 就需要加一个-H参数，如下：

```text
wget -np -nH -R index https://***.org/path/
```

上述的https://***.org/path/是要下载的网址。下载完毕后，将会自动生成一个index.html的文件，打开文件就是需要下载的网址内容了。

- -r : 遍历所有子目录
- -np : 不到上一层子目录去
- -nH : 不要将文件保存到主机名文件夹
- -R index ： 不下载 index.html 文件，会自动生成index