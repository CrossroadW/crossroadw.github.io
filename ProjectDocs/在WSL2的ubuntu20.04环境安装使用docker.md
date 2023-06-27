
# 1 安装docker
因为wsl2已经完整使用了linux内核了，此种方式和先前在linux安装docker类似，步骤如下：
```bash

$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
$ sudo service docker start
执行脚本安装过程中，脚本提示“建议使用Docker Desktop for windows”，20s内按Ctrl+C会退出安装，所以需要等待20s，另外此种方式需要访问外网。
```

# 2 检查docker安装正常
```bash
## 检查dockerd进程启动
sudo service docker status
sudo ps aux|grep docker
# 检查拉取镜像等正常
sudo docker pull busybox
sudo docker images

注意：不同于完全linux虚拟机方式，WLS2下通过apt install docker-ce命令安装的docker无法启动，因为WSL2方式的ubuntu里面没有systemd。上述官方get-docker.sh安装的docker，dockerd进程是用ubuntu传统的init方式而非systemd启动的。

```
3 docker服务的systemd指令和sysvinit指令
```bash
wsl2 没有systemd指令，而是sysvinit指令，双方对照表为：

Systemd 命令	Sysvinit 命令
systemctl start service_name	service  service_name start
systemctl stop service_name	service  service_name stop
systemctl restart service_name	service  service_name restart
systemctl status service_name	service  service_name status
systemctl enable service_name	chkconfig service_name on
systemctl disnable service_name	chkconfig service_name off
```
4 常用几个操作语句
```bash

打开ubuntu后常规语句是
 sudo service docker start

该语句启动了Docker-engine.

接着登录远程的Docker-Hub
sudo docker login

查看Docker-hub上有啥新鲜镜像，比如，我要一个tensorflow的模块。
sudo search tensorflow



显示docker-hub上大量的tensorflow镜像。

随便拉一个下来( 比如 下面三个 )
sudo docker pull tensorflow/tensorflow：latist

sudo docker pull tensorflow/tensorflow：1.9.0-d

sudo docker pull theshadowx/qt5

 查看是否已经被下载到本地
sudo docker images



 启动一个容器
sudo docker run -it -p 7777:7777 --name tf-1.99 tensorflow/tensorflow:1.9.0-devel-py3

 注意：1）启动容器后面的参数“tensorflow/tensorflow:1.9.0-devel-py3”是个镜像

2）将端口换换（将7777:7777换成8888：8888） 重复此语句，将出现两个相同容器，端口不通。

查看启动后容器
 sudo docker ps

查看所有容器
sudo docker ps -a

进入容器内部
法1 ： sudo docker exec -it  62e188e9e750 /bin/bash

法2 ： sudo docker exec -it  tf-1.9  /bin/bash

注意，此句中的 62e188e9e750是通过，sudo docker ps -a查看到的容器ID号。tf-1.9是通过sudo docker ps -a查看到的容器名称。执行失败多是因为 没加 /bin/bash

从容器中退出
exit

查看容器的整长度ID号
sudo docker ps --no-trunc -a 

删除某些容器
sudo docker container prune

删除本地image镜像
sudo docker rmi  image-name

```
5 文件交换
我们存在这样的问题，已经跑好的Win10项目，如何移植到WSL2下的ubuntu内部的container中去，因此需要子啊列操作：将做好的项目存放在Win10下某个盘，我这里存到e：盘根目录。

sudo  cp -r /mnt/e/MNIST_test    /home/huatec
这就将整个项目（在e：/MNIST_test下）全部挪到WSL2的ubuntu中了。

然后，再将此项目移植到container中。

sudo docker ps --no-trunc

显示container的ID全称 

sudo docker cp /home/huatec/MNIST_test  62e188e9e7509f520cd7609f04c9c522c634b5bf9238b43eb23433e7081fe092:/home

这样将全部的项目导入在运行的container内部。
