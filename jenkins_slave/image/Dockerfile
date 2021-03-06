# 指定创建的基础镜像
FROM python:3.6.8-alpine
 
# 作者描述信息
MAINTAINER  zhongqiang <smone123@126.com>

ENV JAVA_HOME /usr/java/jdk

# 替换阿里云的源
# 更新源、安装openssh/tzdata/git/curl/ca/bash, 并且同步时间
RUN echo "http://mirrors.aliyun.com/alpine/latest-stable/main/" > /etc/apk/repositories && \
    echo "http://mirrors.aliyun.com/alpine/latest-stable/community/" >> /etc/apk/repositories && \
    apk update && \
    apk add --no-cache openssh tzdata git curl ca-certificates bash && \ 
    cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    sed -i "s/#PermitRootLogin.*/PermitRootLogin yes/g" /etc/ssh/sshd_config && \
    sed -i "s/.*RSAAuthentication.*/RSAAuthentication yes/" /etc/ssh/sshd_config && \
    sed -i "s/.*PubkeyAuthentication.*/PubkeyAuthentication yes/" /etc/ssh/sshd_config && \
    sed -i "s/.*AuthorizedKeysFile.*/AuthorizedKeysFile   \.ssh\/authorized_keys/" /etc/ssh/sshd_config && \
    ssh-keygen -t dsa -P "" -f /etc/ssh/ssh_host_dsa_key && \
    ssh-keygen -t rsa -P "" -f /etc/ssh/ssh_host_rsa_key && \
    ssh-keygen -t ecdsa -P "" -f /etc/ssh/ssh_host_ecdsa_key && \
    ssh-keygen -t ed25519 -P "" -f /etc/ssh/ssh_host_ed25519_key && \
    echo "root:admin" | chpasswd && \
    git config --global user.name telecom && \
    git config --global user.email smone123@126.com && \
    rm -rf /var/cache/apk/* 

# install python libs,RF
RUN pip --no-cache-dir install robotframework requests openpyxl selenium robotframework-appiumlibrary Appium-Python-Client

# create new user jenkins
RUN addgroup -S -g 1004 jenkins && \
    adduser -S -u 1004 -h /home/jenkins -s /bin/ash -G jenkins jenkins && \
    echo "jenkins:jenkins" | chpasswd && \
    mkdir -p /home/jenkins/.ssh && \
    chmod 700 /home/jenkins/.ssh && \
    chown -R jenkins:jenkins /home/jenkins/.ssh

# install jre dependent libs and config java 
RUN wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub && \
    wget -q https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.29-r0/glibc-2.29-r0.apk && \
    apk add glibc-2.29-r0.apk && \
    rm -rf /var/cache/apk/* && \
    mkdir -p /usr/java/jdk && \
    chmod -R 755 /usr/java/jdk && \
    echo "export PATH=${PATH}:${JAVA_HOME}/bin" >>/etc/profile

# add jre package
ADD jre8.tar.gz /usr/java/jdk/

# 设置免密登录
COPY --chown=jenkins:jenkins --chmod=600 authorized_keys /home/jenkins/.ssh/
COPY --chown=jenkins:jenkins id_rsa* /home/jenkins/.ssh/


# 开放22端口
EXPOSE 22
WORKDIR /home/jenkins
 
# 执行ssh启动命令
CMD  ["/usr/sbin/sshd", "-D"]
