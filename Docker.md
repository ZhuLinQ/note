# 1、Docker快速入门
## 部署MySQL
1. 部署
```PowerShell
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  mysql
```
Docker会自动搜索并下载MySQL。注意：这里下载的不是安装包，而是镜像。镜像中不仅包含了MySQL本身，还包含了其运行所需要的环境、配置、系统级函数库。因此它在运行时就有自己独立的环境，就可以跨系统运行，也不需要手动再次配置环境了。这套独立运行的隔离环境我们称为**容器。

说明：
- 镜像：英文是image
- 容器：英文是container
![docker1.png](img/docker1.png)
2. 命令解读
	- docker run 创建并运行一个容器，-d：让容器在后台运行
	- --name 为容器起名
	- -p 3306:3306 设置端口映射。容器是一个独立的小空间，有独立的资源，需要设置主机到容器的映射：-p 宿主机端口:容器端口
	- -e KEY=VALUE 设置环境变量（不同镜像不一样）
	- mysql 指定运行的镜像的名字；镜像命名规范：镜像名:版本号
# 2、Docker常见的命令
1. docker pull 拉取镜像
	例：
	```bash
		docker nginx
	```
2. docker images 查看本地的镜像
3. docker rmi 删除本地镜像
4. docker save 保存镜像到本地压缩文件
5. docker load 加载本地压缩文件到镜像
6. docker run 创建并运行容器
7. docker ps 默认查看正在运行中的容器；-a 查看所有容器
8. docker stop 停止容器
9. docker start 启动容器
10. docker rm 删除容器；-f 强制删除
11. docker logs 查看日志；-f 能够持续输出日志
12. docker exec -it  容器名 bash：进入容器，-it表示添加一个可交互的终端，使用bash命令行
# 3、数据卷
1. 数据卷（volume）是一个虚拟目录，是容器内目录与宿主机目录之间映射的桥梁。