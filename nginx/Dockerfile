FROM nginx:1.7
MAINTAINER Shane Sveller <shane@bellycard.com>
#https://github.com/bellycard/docker-loadbalancer/blob/master/Dockerfile

RUN DEBIAN_FRONTEND=noninteractive \
    apt-get update -qq && \
    apt-get -y install curl runit unzip && \
    rm -rf /var/lib/apt/lists/*

#ENV CT_URL https://github.com/hashicorp/consul-template/releases/download/v0.1.0/consul-template_0.1.0_linux_amd64.tar.gz
ENV CT_URL https://releases.hashicorp.com/consul-template/0.14.0/consul-template_0.14.0_linux_amd64.zip
#RUN curl -L $CT_URL | tar -C /usr/local/bin --strip-components 1 -zxf -
RUN curl -L -o consul-template.zip $CT_URL && unzip consul-template.zip -d /usr/local/bin

ADD nginx.service /etc/service/nginx/run
RUN chmod +x /etc/service/nginx/run
ADD consul-template.service /etc/service/consul-template/run
RUN chmod +x /etc/service/consul-template/run

RUN rm -v /etc/nginx/conf.d/*
ADD nginx.conf /etc/consul-templates/nginx.conf

CMD ["/usr/bin/runsvdir", "/etc/service"]
