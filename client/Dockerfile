FROM ubuntu:16.04
WORKDIR /work
RUN apt-get update && \
    apt-get install -y iproute2 iputils-ping curl

#RUN tc qdisc add dev eth0 root netem delay 100ms
