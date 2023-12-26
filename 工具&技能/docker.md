### 基本操作

- docker安装`curl https://get.docker.com/ | bash -s docker --mirror Aliyun`
- 检查运行状态`sudo systemctl status docker`
  - 启动`sudo systemctl start docker`
- 将用户添加到docker可执行组`sudo usermod -aG docker hadoop`
- 拉取docker镜像：`docker pull name`
- 查看已经安装的镜像：`docker images`
- 删除镜像：`docker rmi name`
  - 强制删除：`docker rmi --force name`