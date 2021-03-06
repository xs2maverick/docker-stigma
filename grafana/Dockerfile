FROM centos:6

MAINTAINER heeminChae / version: 1.0

# Add Default User Account : stigma / Change Password : root, stigma
RUN groupadd -g 500 -r stigma && useradd -u 500 -r -m -g stigma stigma && \
        echo "stigma:S2curity" | chpasswd && \
        echo "root:Su2crity" | chpasswd

# Install default package
RUN yum -y install tuned tune-util vim bzip2 gzip unzip tar git wget curl hostname sysvinit-tools util-linux net-tools epel-release openssh* sudo yum-utils

# sudo stigma sshd
RUN echo stigma ALL=/etc/init.d/sshd >> /etc/sudoers
RUN sed -i 's/Defaults    requiretty/\#Defaults    requiretty/g' /etc/sudoers

# TimeZone Change : KST
RUN mv /etc/localtime /etc/localtime.bak && \
        ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime

# Set Env
ENV PROJECT_HOME="/app" \
    GRAFANA_HOME="/app/grafana"  \
    GRAFANA_LOGS="/app/grafana/logs" \
    WORK=/work

# Make default directory
RUN mkdir -p $PROJECT_HOME && mkdir -p $WORK && chown -R stigma:stigma $PROJECT_HOME && \
    mkdir -p $GRAFANA_HOME/logs

# Install grafana, supervisor
ADD ./grafana.repo	/etc/yum.repos.d/grafana.repo
RUN yum install -y grafana supervisor; yum clean all

ADD ./post.sh 	 $WORK/post.sh
ADD ./supervisord.conf	$WORK/supervisord.conf
RUN chmod 755	 $WORK/post.sh

# COPY supervisord.conf
COPY ./supervisord.conf	/etc/supervisord.conf

# compress Application
RUN cd $WORK && tar cvfz $WORK/grafana.tar.gz $GRAFANA_HOME && rm -rf $PROJECT_HOME

EXPOSE	3000

CMD ["/work/post.sh"]
