FROM node:lts-alpine3.12

LABEL AUTHOR="lianlian" \
      VERSION=1.0.1

# FROM # 基础镜像 比如node
# MAINTAINER # 镜像是谁写的 姓名+邮箱(MAINTAINER已经被废弃)
# LABEL # 给镜像添加一些元数据
# ARG # 构建参数,与 ENV 作用一至.不过作用域不一样。ARG设置的环境变量仅对Dockerfile内有效,也就是说只有docker build的过程中有效，构建好的镜像内不存在此环境变量
# RUN # 镜像构建时需要运行的命令
# ADD # 添加，比如添加一个tomcat压缩包
# WORKDIR # 镜像的工作目录
# VOLUME # 挂载的目录
# EXPOSE # 指定暴露端口，跟-p一个道理
# RUN # 最终要运行的
# CMD # 指定这个容器启动的时候要运行的命令，只有最后一个会生效，而且可被替代
# ENTRYPOINT # 指定这个容器启动的时候要运行的命令，可以追加命令
# ONBUILD # 当构建一个被继承Dockerfile 这个时候运行ONBUILD指定，触发指令
# COPY # 将文件拷贝到镜像中
# ENV # 构建的时候设置环境变量

# ====================================================================
# Add proxy, using --build-arg "HTTP_PROXY=http://192.168.1.100:7890"

ENV HTTP_PROXY ${HTTP_PROXY}
ENV HTTPS_PROXY ${HTTP_PROXY}
ENV http_proxy ${HTTP_PROXY}
ENV https_proxy ${HTTP_PROXY}

# set -e ： -e这个参数的含义是,当命令发生错误的时候,停止脚本的执行
# set -x ： -x参数的作用是把将要运行的命令用一个+标记之后显示出来
# echo -e ：激活转义字符
# echo -n ：不换行输出
# apk : 软件包管理工具
# mkdir -p ：递归创建目录，即使上级目录不存在，会按目录层级自动创建目录
# git clone -b : 克隆指定的分支 git clone -b 分支名 仓库地址

# 设置环境变量，容器内部使用
ENV DEFAULT_LIST_FILE=crontab_list.sh \
    CUSTOM_LIST_MERGE_TYPE=append \
    COOKIES_LIST=/scripts/logs/cookies.list \
    REPO_URL=git@gitee.com:zhaozhanzhan520/jd_docker.git \
    REPO_BRANCH=master

RUN set -ex 
RUN echo $http_proxy 
RUN echo -e "https://mirrors.ustc.edu.cn/alpine/latest-stable/main\nhttps://mirrors.ustc.edu.cn/alpine/latest-stable/community" > /etc/apk/repositories 
RUN apk update 
RUN apk upgrade 
RUN apk add --no-cache bash tzdata git moreutils curl jq openssh-client 
RUN apk add --no-cache make gcc g++ python3-dev pkgconfig pixman-dev cairo-dev pango-dev libjpeg-turbo-dev giflib-dev 
RUN rm -rf /var/cache/apk/* 
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime 
RUN echo "Asia/Shanghai" > /etc/timezone 
RUN mkdir /scripts
COPY ../ /scripts
RUN ls
WORKDIR /scripts 
RUN ls
RUN mkdir logs 
RUN npm config set registry https://registry.npm.taobao.org 
RUN ls
RUN npm install 
RUN cp /scripts/docker/docker_entrypoint.sh /usr/local/bin 
RUN chmod +x /usr/local/bin/docker_entrypoint.sh

#Add pip install mirror:

RUN echo "[global] \
    index-url = https://mirrors.aliyun.com/pypi/simple \
    trusted-host = mirrors.aliyun.com \
    timeout = 120 \
    " > /etc/pip.conf
# ====================================================================
# RUN pip install --upgrade pip 
# ====================================================================
ENV HTTP_PROXY ""
ENV HTTPS_PROXY ""
ENV http_proxy ""
ENV https_proxy ""
# ====================================================================

WORKDIR /scripts

ENTRYPOINT ["docker_entrypoint.sh"]

# alpine版本内嵌的是BusyBox，使用alpine的crontab实际就是使用BusyBox的crond服务
CMD [ "crond" ]
