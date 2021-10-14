FROM ubuntu:latest
ARG DEBIAN_FRONTEND=noninteractive
RUN apt update
RUN apt install default-jdk maven -y
RUN apt install -y docker.io
RUN apt clean
COPY daemon.json /etc/docker