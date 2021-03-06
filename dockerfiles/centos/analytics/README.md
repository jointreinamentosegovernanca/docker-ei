# Dockerfile for WSO2 Enterprise Integrator Analytics #

This section defines the step-by-step instructions to build [CentOS](https://hub.docker.com/_/centos/) Linux based Docker images for multiple profiles
provided by WSO2 Enterprise Integrator 6.4.0, namely:<br>

1. Dashboard
2. Worker

## Prerequisites

* [Docker](https://www.docker.com/get-docker) v17.09.0 or above

## How to build an image and run
##### 1. Checkout this repository into your local machine using the following Git command.

```
git clone https://github.com/wso2/docker-ei.git
```

>The local copy of the `dockerfile/centos/analytics` directory will be referred to as `ANALYTICS_DOCKERFILE_HOME` from this point onwards.


##### 2. Add Analytics profile distribution and MySQL connector to `<ANALYTICS_DOCKERFILE_HOME>/base/files`.

- Download [AdoptOpenJDK 8](https://adoptopenjdk.net/) and extract it to `<ANALYTICS_DOCKERFILE_HOME>/files`.
- Download [WSO2 Enterprise Integrator 6.4.0 distribution](https://wso2.com/integration/) distribution.
Extract the product distribution and execute the `<EI_HOME>/bin/profile-creator.sh` to generate the Micro Integrator
profile distribution.

```
./<EI_HOME>/bin/profile-creator.sh
``` 

Extract the generated profile distribution to `<ANALYTICS_DOCKERFILE_HOME>/base/files`.

- Download [MySQL Connector/J](https://downloads.mysql.com/archives/c-j)
and copy that to `<ANALYTICS_DOCKERFILE_HOME>/base/files`.
- Once all of these are in place, it should look as follows:

  ```bash
  <ANALYTICS_DOCKERFILE_HOME>/base/files/wso2ei-6.4.0/
  <ANALYTICS_DOCKERFILE_HOME>/base/files/mysql-connector-java-<version>-bin.jar
  ```

>Please refer to [WSO2 Update Manager documentation]( https://docs.wso2.com/display/WUM300/WSO2+Update+Manager)
in order to obtain latest bug fixes and updates for the product.

##### 3. Build the base Docker image.

- For base, navigate to `<ANALYTICS_DOCKERFILE_HOME>/base` directory. <br>
  Execute `docker build` command as shown below.
    + `docker build -t wso2ei-analytics-base:6.4.0-centos .`
    
##### 4. Build Docker images specific to each profile.

- For dashboard, navigate to `<ANALYTICS_DOCKERFILE_HOME>/dashboard` directory. <br>
  Execute `docker build` command as shown below.
    + `docker build -t wso2ei-analytics-dashboard:6.4.0-centos .`
- For worker, navigate to `<ANALYTICS_DOCKERFILE_HOME>/worker` directory. <br>
  Execute `docker build` command as shown below.
    + `docker build -t wso2ei-analytics-worker:6.4.0-centos .`
    
##### 5. Running Docker images specific to each profile.

- For dashboard,
    + `docker run -p 9713:9713 -p 9643:9643 ...all-port-mappings-here... wso2ei-analytics-dashboard:6.4.0-centos`
- For worker,
    + `docker run -p 9444:9444 ...all-port-mappings-here... wso2ei-analytics-worker:6.4.0-centos`
    
##### 6. Accessing the Dashboard portal.

- For Analytics Dashboard,
    + Business Rules:<br>
    `https://<DOCKER_HOST>:9643/business-rules`
    + Portal:<br>
    `https://<DOCKER_HOST>:9643/portal`
    + Monitoring:<br>
    `https://<DOCKER_HOST>:9643/monitoring`
    
>In here, <DOCKER_HOST> refers to hostname or IP of the host machine on top of which containers are spawned.

## How to update configurations
Configurations would lie on the Docker host machine and they can be volume mounted to the container. <br>
As an example, steps required to change the port offset using `deployment.yaml` is as follows.

##### 1. Stop the Enterprise Integrator Analytics container if it's already running.
In WSO2 Enterprise Integrator 6.4.0 product distribution, `deployment.yaml` configuration file <br>
can be found at `<DISTRIBUTION_HOME>/wso2/analytics/conf/worker`. Copy the file to some suitable location of the host machine, <br>
referred to as `<SOURCE_CONFIGS>/deployment.yaml` and change the offset value under ports to 2.

##### 2. Grant read permission to `other` users for `<SOURCE_CONFIGS>/deployment.yaml`
```
chmod o+r <SOURCE_CONFIGS>/deployment.yaml
```

##### 3. Run the image by mounting the file to container as follows.
```
docker run 
-p 9444:9444
--volume <SOURCE_CONFIGS>/deployment.yaml:<TARGET_CONFIGS>/deployment.yaml
wso2ei-analytics-worker:6.4.0-centos
```

>In here, <TARGET_CONFIGS> refers to /home/wso2carbon/wso2ei-6.4.0/wso2/analytics/conf/worker folder of the container.


## Docker command usage references

* [Docker build command reference](https://docs.docker.com/engine/reference/commandline/build/)
* [Docker run command reference](https://docs.docker.com/engine/reference/run/)
* [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
