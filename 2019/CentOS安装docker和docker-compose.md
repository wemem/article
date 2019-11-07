# CentOS安装docker和docker-compose

````bash
# 安装docker
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo  https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io
sudo systemctl start docker
# 设置开机启动
sudo systemctl enable docker

#安装docker-compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

#出现错误log
ERROR: Command errored out with exit status 1: python setup.py egg_info Check the logs for full command output.
#解决方案
pip install --user --upgrade setuptools
````