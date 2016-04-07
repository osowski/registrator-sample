# Microservice dynamic auto-scaling example

This sample application will run Let's Chat, an open-sourced team-based chat application, in a dynamically scalable, auto load-balanced fashion.

## Components used:
- [Docker](https://www.docker.com/what-docker)
- [Registrator](https://github.com/gliderlabs/registrator) - Service Registrar
- [Consul](https://www.consul.io/intro/) - Service Registry
- [Consul-Template](https://github.com/hashicorp/consul-template) - Configuration file template tool
- [Nginx](https://www.nginx.com/resources/wiki/) - HTTP/Proxy server
- [Let's Chat](https://github.com/sdelements/lets-chat) - Our application

## Application flow
1. Docker Compose stands up all the containers needed
2. Registrator listens to the Docker Event System and automatically registers new containers to the Consul service discovery engine
3. Consul Template, built into our Nginx container, queries Consul for available services and automatically generates an nginx.conf file, based on the available services.
4. Nginx then round-robins the load across all the available services that Consul says exist.
5. As Docker Compose scales the number of Let's Chat services, Registrator automatically registers these new instances in Consul.
6. Consul Template detects the changes in Consul, regenerates an updated nginx.conf with the new service endpoints, and then reloads the nginx configuration for updated _upstream_ servers.

To dig deeper on the application & interaction flow, see [Reference Links](#reference-links) **#2** and **#4** below where other teams have already documented this quite well.

## Pre-requisites

- Docker
- Docker Compose
- Clone this repo from https://github.com/osowski/registrator-sample.git

## Running this sample:

1. Set your Docker virtual machine or hostname IP to the MYHOST environment variable  
  ` export MYHOST=$(docker-machine ip default)`

2. Pull the pre-requisite images used in this sample  
  `docker-compose pull`

3. Build the images used in this sample  
  `docker-compose build`

4. Stand up the component container instances, in _detached mode_.  
  `docker-compose up -d`

5. View the running instances of _Let's Chat_, automatically registered in Consul via Registrator  
  `curl http://${MYHOST}:8500/v1/catalog/service/lets-chat`

  You will see a response on the command line, similar to the following:  
  `[{"Node":"192.168.99.100","Address":"192.168.99.100","ServiceID":"192.168.99.100:registratorsample_lets-chat_1:8080","ServiceName":"lets-chat","ServiceTags":[],"ServiceAddress":"192.168.99.100","ServicePort":32807,"ServiceEnableTagOverride":false,"CreateIndex":7,"ModifyIndex":7}]`

6. Check the logs of the _Let's Chat_ container to ensure that it is up and running.  
  `docker logs registratorsample_lets-chat_1`

7. Access the _Let's Chat_ app via your browser at `http://${MYHOST}/` and note the **Hosted by XYZ** above the login box.  

8. Scale up the _Let's Chat_ component.  
  `docker-compose scale lets-chat=5`

9. View the running instances of _Let's Chat_ again, automatically registered in Consul via Registrator  
  `curl http://${MYHOST}:8500/v1/catalog/service/lets-chat`  

  You will see an updated response on the command line, similar to the following:  
  `[{"Node":"192.168.99.100","Address":"192.168.99.100","ServiceID":"192.168.99.100:registratorsample_lets-chat_1:8080","ServiceName":"lets-chat","ServiceTags":[],"ServiceAddress":"192.168.99.100","ServicePort":32808,"ServiceEnableTagOverride":false,"CreateIndex":7,"ModifyIndex":7},{"Node":"192.168.99.100","Address":"192.168.99.100","ServiceID":"192.168.99.100:registratorsample_lets-chat_2:8080","ServiceName":"lets-chat","ServiceTags":[],"ServiceAddress":"192.168.99.100","ServicePort":32810,"ServiceEnableTagOverride":false,"CreateIndex":9,"ModifyIndex":9},{"Node":"192.168.99.100","Address":"192.168.99.100","ServiceID":"192.168.99.100:registratorsample_lets-chat_3:8080","ServiceName":"lets-chat","ServiceTags":[],"ServiceAddress":"192.168.99.100","ServicePort":32812,"ServiceEnableTagOverride":false,"CreateIndex":11,"ModifyIndex":11},{"Node":"192.168.99.100","Address":"192.168.99.100","ServiceID":"192.168.99.100:registratorsample_lets-chat_4:8080","ServiceName":"lets-chat","ServiceTags":[],"ServiceAddress":"192.168.99.100","ServicePort":32809,"ServiceEnableTagOverride":false,"CreateIndex":8,"ModifyIndex":8},{"Node":"192.168.99.100","Address":"192.168.99.100","ServiceID":"192.168.99.100:registratorsample_lets-chat_5:8080","ServiceName":"lets-chat","ServiceTags":[],"ServiceAddress":"192.168.99.100","ServicePort":32811,"ServiceEnableTagOverride":false,"CreateIndex":10,"ModifyIndex":10}]`

10. Check the logs of the new _Let's Chat_ container to ensure that it is up and running.  
  `docker logs registratorsample_lets-chat_5`

11. Back in your browser, reload the _Let's Chat_ login page at `http://${MYHOST}/` and note the updated **Hosted by XYZ** above the login box.  You can reload this page any number of times and the _nginx_ container will dynamically load-balance via round-robin across all the new _Let's Chat_ container instances.

12. You can scale the number of _Let's Chat_ services back down to 1 and see the original **Hosted by XYZ** being the only host remaining.


### Reference links:

1. [Registrator Quickstart](http://gliderlabs.com/registrator/latest/user/quickstart/)
2. [Load-balancing Docker containers with Nginx and Consul-Template](https://tech.bellycard.com/blog/load-balancing-docker-containers-with-nginx-and-consul-template/)
3. [GitHub: bellycard/docker-loadbalancer](https://github.com/bellycard/docker-loadbalancer)
4. [A production ready Docker workflow. Part 4: Service Discovery and the load balancer](http://www.luiselizondo.net/a-production-ready-docker-workflow-part-4-service-discovery-and-the-load-balancer/)
5. [Registrator Issue: Wrong IP assigned](https://github.com/gliderlabs/registrator/issues/169#)

### Key changes from reference links:

- Updated Dockerfile to use `unzip` and `chmod` **consul-template** files as appropriate
- Updated Let's Chat login page to display the hostname of the container it was running on
