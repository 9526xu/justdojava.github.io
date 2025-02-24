---
layout: post
title:  Docker 容器基本操作[Docker 系列-2]
tagline: by 江南一点雨
categories: docker
tag: 
    - docker
---

docker 中的容器就是一个轻量级的虚拟机，是镜像运行起来的一个状态，本文就先来看看容器的基本操作。 

<!--more-->

镜像就像是一个安装程序，而容器则是程序运行时的一个状态。

# 查看容器

## 查看容器

启动 docker 后，使用 `docker ps` 命令可以查看当前正在运行的容器：  

![2-1](/assets/images/2019/java/image_javaboy/0530/2-1.png)  

## 查看所有容器

上面这条命令是查看当前正在运行的容器，如果需要查看所有容器，则可以通过 `docker ps -a` 命令查看：  

![2-2](/assets/images/2019/java/image_javaboy/0530/2-2.png)  

在查看容器时，涉及到几个查看参数，含义分别如下：  

- CONTAINER ID:CONTAINER ID是指容器的id，是一个唯一标识符,这是一个64位的十六进制整数，在不会混淆的情况下可以只采用id的前几位进行标识一个容器。
- IMAGE:IMAGE表示创建容器时使用的镜像。 
- COMMAND:COMMAND表示容器最后运行的命令。
- CREATED:创建容器的时间。
- STATUS:容器的状态，这里可能显示一个容器启动时间，也能显示容器关闭时间。具体显示哪个要看容器当前的状态。  
- PORTS:容器对外开放的端口。
- NAMES:容器的名字，如果不设置，会有一个默认的名字。  

## 查看最新创建的容器

使用 `docker ps -l` 可以查看最近创建的容器，如下：  

![2-3](/assets/images/2019/java/image_javaboy/0530/2-3.png)  

## 查看最新创建的n个容器

可以使用 `docker ps -n=XXX` 来查看最新创建的n个容器，如下：  

![2-4](/assets/images/2019/java/image_javaboy/0530/2-4.png)  

# 创建容器

创建容器整体上来说有两种不同的方式，可以先创建，再启动，也可以连创建带启动一步到位，无论是那种方式，流程都是相似的，当执行一个创建命令之后，docker 首先会去本地路径下查找是否有相应的镜像，如果没有，就去 docker hub 上搜索，如果搜索到了，则下载下来，然后利用该镜像创建一个容器并启动。容器的文件系统是在只读的镜像文件上添加一层可读写的文件层，这样可以使在不改变镜像的情况下，只记录改变的数据。下面对这两种方式分别予以介绍。  

## 容器创建

开发者可以首先使用 `docker create` 命令创建一个容器，这个时候创建出来的容器是处于停止状态，没有运行，例如要创建一个 nginx 容器，创建命令如下：  

```
docker create nginx
```

创建成功后，可以查看容器是否创建成功：  

![2-5](/assets/images/2019/java/image_javaboy/0530/2-5.png)  

此时创建的容器并未运行，处于停止状态，容器的 name 是随机生成的，开发者也可以在创建容器时指定 name ，如下：  

```
docker create --name=nginx nginx
```  

运行结果如下：  

![2-6](/assets/images/2019/java/image_javaboy/0530/2-6.png)  

此时的 name 属性就不是随机生成的，而是用户指定的 name。  

这种方式只是单纯的创建了一个用户，并未启动。  

## 容器创建+启动

如果开发者需要既创建又启动容器，则可以使用 `docker run` 命令。 `docker run` 命令又可以启动两种不同模式的容器：后台型容器和交互型容器，顾名思义，后台型容器就是一个在后台运行的容器，默默的在后台执行计算就行了，不需要和开发者进行交互，而交互型容器则需要接收开发者的输入进行处理给出反馈。对于开发者而言，大部分情况下创建的都是后台型容器，不过在很多时候，即使是后台型容器也不可避免的需要进行交互，下面分别来看。  

### 后台型容器

后台型容器以 nginx 为例，一般 nginx 在后台运行即可：  

```
docker run --name nginx1 -d -p 8080:80 nginx
```  

`--name` 含义和上文一样，表示创建的容器的名字，-d 表示容器在后台运行，-p 表示将容器的 80 端口映射到宿主机的 8080 端口，创建过程如下图：  

![2-7](/assets/images/2019/java/image_javaboy/0530/2-7.png)  

首先依然会去本地检查，本地没有相应的容器，则会去 Docker Hub 上查找，查找到了下载并运行，并且生成了一个容器 id。运行成功后，在浏览器中输入 `http://localhost:8080` 就能看到 Nginx 的默认页面了，如下：  

![2-8](/assets/images/2019/java/image_javaboy/0530/2-8.png)  

这是一个后台型容器的基本创建方式。

### 交互型容器

也可以创建交互型容器，例如创建一个 ubuntu 容器，开发者可能需要在 ubuntu 上面输入命令执行相关操作，交互型容器创建方式如下：  

```
docker run --name ubuntu -it ubuntu /bin/bash
```  

参数含义都和上文一致，除了 -it，-it 参数，i 表示开发容器的标准输入（STDIN），t 则表示告诉 docker，为容器创建一个命令行终端。执行结果如下：  

![2-9](/assets/images/2019/java/image_javaboy/0530/2-9.png)  

该命令执行完后，会打开一个输入终端，读者就可以在这个终端里愉快的操作 ubuntu 了。 

想要退出该终端，只需要输入 exit 命令即可。  

# 容器启动

## 启动

如果开发者使用了 `docker run` 命令创建了容器，则创建完成后容器就已经启动了，如果使用了 `docker create` 命令创建了容器，则需要再执行 `docker start` 命令来启动容器，使用 `docker start` 命令结合容器 id 或者容器 name 可以启动一个容器，如下：  

![3-1](/assets/images/2019/java/image_javaboy/0530/3-1.png)  

`docker start` 启动的是一个已经存在的容器，要使用该命令启动一个容器，必须要先知道容器的 id 或者 name ，开发者可以通过这两个属性启动一个容器（案例中，nginx 是通过 name 启动，而 ubuntu 则是通过 id 启动）。一般来说，第一次可以使用 `docker run` 启动一个容器，以后直接使用 `docker start` 即可。  

## 重启

容器在运行过程中，会不可避免的出问题，出了问题时，需要能够自动重启，在容器启动时使用 --restart 参数可以实现这一需求。根据 docker 官网的解释，docker 的重启策略可以分为 4 种，如下：  

![3-2](/assets/images/2019/java/image_javaboy/0530/3-2.png)  

四种的含义分别如下：  

1. no表示不自动重启容器，默认即此。
2. on:failure:[max-retries]表示在退出状态为非0时才会重启（非正常退出），有一个可选择参数：最大重启次数，可以设置最大重启次数，重启次数达到上限后就会放弃重启。
3. always表示始终重启容器，当docker守护进程启动时，也会无论容器当时的状态为何，都会尝试重启容器。
4. ubless-stopped表示始终重启容器，但是当docker守护进程启动时，如果容器已经停止运行，则不会去重启它。  

# 容器停止

通过 `docker stop` 命令可以终止一个容器，如下：

![3-3](/assets/images/2019/java/image_javaboy/0530/3-3.png)  

可以通过 name 或者 id 终止一个容器。  

# 容器删除

## 单个删除

容器停止后还依然存在，如果需要，还可以通过 `docker start` 命令再次重启一个容器，如果不需要一个容器，则可以通过 `docker rm` 命令删除一个容器。删除容器时，只能删除已经停止运行的容器，不能删除正在运行的容器。如下：

![3-4](/assets/images/2019/java/image_javaboy/0530/3-4.png)  

可以通过 name 或者 id 删除一个容器。如果非要删除一个正在运行的容器，可以通过 -f 参数实现，如下：  

![3-5](/assets/images/2019/java/image_javaboy/0530/3-5.png)  

## 批量删除

容器也可以批量删除，命令如下：  
```
docker rm $(docker ps -a -q)
```  

`docker ps -a -q` 会列出所有容器的 id ，供 rm 命令删除。  

如下命令也支持删除已退出的孤立的容器：  

```
docker container prune
```

# 总结

本文主要向大家介绍了 Docker 容器的基本操作，更多高级操作我们将在下篇文章中介绍。

参考资料：

[1] 曾金龙，肖新华，刘清.Docker开发实践[M].北京：人民邮电出版社，2015.