FROM centos:latest

RUN  yum -y install git qemu-img which sudo epel-release && \
     yum -y install python-pip && \
     yum clean all

RUN pip install --upgrade pip && pip install diskimage-builder==2.9.0
