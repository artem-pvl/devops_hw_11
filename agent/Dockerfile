FROM ubuntu:latest
USER root
ARG DEBIAN_FRONTEND=noninteractive
RUN apt update
RUN apt install -y default-jdk maven
RUN apt install -y docker.io
RUN apt clean
COPY daemon.json /etc/docker/