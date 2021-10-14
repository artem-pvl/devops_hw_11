FROM maven:latest

# Install pacakges
RUN apt-get update

# Install docker 18.03
RUN apt-get install -y docker.io

# Clear cache
RUN apt-get clean

COPY daemon.json /etc/docker