FROM centos/systemd

### MODIFY ONDEMAND USERNAME AND PASSWORD HERE
ENV ONDEMAND_USER ondemand
ENV ONDEMAND_PASSWORD password
ENV ROOT_PASSWORD password
### END MODIFY

### DO NOT MODIFY ###

RUN adduser $ONDEMAND_USER

# Setup all SSH stuff
RUN yum install -y openssh-server
RUN mkdir /var/run/sshd
RUN echo "root:$ROOT_PASSWORD" | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

COPY ./sshd_config /etc/ssh/sshd_config

# Required for base host key, otherwise you can't get into the container :(
RUN /usr/bin/ssh-keygen -A

# Install OnDemand
RUN yum update -y \
    && yum install -y centos-release-scl \
    && yum install -y https://yum.osc.edu/ondemand/1.6/ondemand-release-web-1.6-4.noarch.rpm \
    && yum install -y ondemand-selinux

# Systemd
RUN yum -y install systemd; yum clean all; \
    (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
    rm -f /lib/systemd/system/multi-user.target.wants/*; \
    rm -f /etc/systemd/system/*.wants/*;\
    rm -f /lib/systemd/system/local-fs.target.wants/*; \
    rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
    rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
    rm -f /lib/systemd/system/basic.target.wants/*; \
    rm -f /lib/systemd/system/anaconda.target.wants/*;

RUN systemctl enable sshd.service
RUN systemctl enable httpd24-httpd

RUN scl enable ondemand -- htpasswd -cb /opt/rh/httpd24/root/etc/httpd/.htpasswd $ONDEMAND_USER $ONDEMAND_PASSWORD

COPY ./entrypoint.sh /usr/sbin/entrypoint
RUN chmod a+x /usr/sbin/entrypoint

VOLUME [ "/sys/fs/cgroup" ]
CMD ["/usr/sbin/entrypoint"]