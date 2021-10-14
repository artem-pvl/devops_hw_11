FROM ubuntu:latest
ARG DEBIAN_FRONTEND=noninteractive
RUN apt update
RUN apt install -y default-jdk maven
RUN apt install -y docker.io
RUN apt clean
RUN useradd -u 1000 jenkins
COPY daemon.json /etc/docker/