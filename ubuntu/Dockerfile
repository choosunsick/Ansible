FROM ubuntu:latest

RUN apt update && apt install openssh-server sudo -y

RUN useradd -rm -d /home/ubuntu -s /bin/bash -g root -G sudo -u 1000 pi 

RUN echo 'pi:raspberry' | chpasswd

RUN adduser pi sudo 

RUN echo 'user ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers


RUN service ssh start

EXPOSE 22 80

CMD ["/usr/sbin/sshd","-D"]