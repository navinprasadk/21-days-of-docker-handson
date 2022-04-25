FROM ubuntu:22.04
LABEL maintainer="navinprasadk"
RUN apt-get update && \
    apt-get install -y nginx
ENTRYPOINT ["/usr/sbin/nginx","-g","daemon off;"]
EXPOSE 80
