
# Pega PE with external kafka for stream services in dockers

## Note! Currenct instruction is used for Pega PE 8.7 and kafka 3.6.0. It might works wrong on other versions

You may use current project for start up Pega PE without stream node type and external kafka for queue processors. OS that was using for this project is ubuntu. Also you have to prepare minimum ~10 gb free space

## Getting started

### Docker

* Before start your host should has the docker and the docker compose. You can install it by links below:

    [Install docker](https://docs.docker.com/install/)

    [Install docker compose](https://docs.docker.com/compose/install/)


### Kafka

* You might read about bitnami/kafka by this [link](https://hub.docker.com/r/bitnami/kafka/). However you just could donwload current project with all settings 

## Configure kafka docker container

* Run the docker-compose file

* Inside kafka docker you should run kafka-storage.sh for generate kafka cluster id (path to script /opt/bitnami/kafka/bin):

```bash
        ./kafka-storage.sh random-uuid
```

* Update docker-compose.yml by generated uuid above

* Next step is adding network for our dockers (you can use any ip as you want) and don`t forget to apply new network for kafka-ui and kafka services:

```docker
        networks:
        Your_Network_Name:
            ipam:
            driver: default
            config:
                - subnet: {ip}/16
                gateway: {ip}
```

* Run docker-compose and check that kafka ui works fine and kafka broker works too by localhost:8080

## Configure Pega PE

* Firstly, you have to prepare pega for next using. You may use [this](https://github.com/kannan-raveendran-nair/pega-pe-docker/tree/master) project for install pega with docker. **However before build pega image you have to [change](https://docs-previous.pega.com/decision-management/87/configuring-external-kafka-stream-service) prconfig.xml from project.** Change prconfig.xml and put your gateway_ip from previous step to line below

```xml
 	    <env name="services/stream/broker/url" value="{gateway_ip}" />
```

* The following setting allows you to run the application with the required node types: open the docker-compose file with pega services and put next line in the environment for pega pe service:

```docker
        - NODE_TYPE=Batch,RealTime,BackgroundProcessing,Search,WebUser
```

* Build and run Pega PE from the project [pega-pe-docker](https://github.com/kannan-raveendran-nair/pega-pe-docker)

## Summary

* If all the steps were done correctly, then in pega it will be possible to see (Configure -> Deciding -> Infrastructure -> Services -> Stream) the external kafka in the normal status. In the kafka ui tool it will possible to see new topics for all QPs
