# TBD


## Pre-requisites

- _Docker_
- _Docker Compose_
- Clone this repo

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

8. **SCALE UP THE LET'S CHAT COMPONENT**

9. **VIEW CONSUL SERVICES**

10. **RELOAD BROWSER PAGE**




### Reference links:

- [Registrator Quickstart](http://gliderlabs.com/registrator/latest/user/quickstart/)
- [Load-balancing Docker containers with Nginx and Consul-Template](https://tech.bellycard.com/blog/load-balancing-docker-containers-with-nginx-and-consul-template/)
- [GitHub: bellycard/docker-loadbalancer](https://github.com/bellycard/docker-loadbalancer)
- [A production ready Docker workflow. Part 4: Service Discovery and the load balancer](http://www.luiselizondo.net/a-production-ready-docker-workflow-part-4-service-discovery-and-the-load-balancer/)
- [Registrator Issue: Wrong IP assigned](https://github.com/gliderlabs/registrator/issues/169#)

### Key changes from reference links:

- Updated Dockerfile to use `unzip` and `chmod` **consul-template** files as appropriate
