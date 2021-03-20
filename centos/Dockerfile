FROM centos:latest

RUN yum -y install systemd; yum clean all; \
(cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;

RUN yum -y install openssh-server;

RUN useradd -rm -d /home/centos -s /bin/bash -g root -u 1000 pi

RUN echo 'pi:raspberry' | chpasswd

RUN echo 'user ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

RUN systemctl start ssh

EXPOSE 22 80

CMD ["/usr/sbin/init"]
