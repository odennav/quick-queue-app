# Add Spring Boot Application to a Kubernetes Cluster

This Quick Queue application was developed as a Spring Boot application, with the initial artifact generated from the initializer website accessible at [Spring](https://start.spring.io).

Subsequently, we dockerized the application by creating a docker image inclusive of the application and executed this image within Docker on our development machine.

Following this, we uploaded our newly created image to the docker hub repository, facilitating its accessibility for download and installation onto our Kubernetes cluster.

This Spring Boot application will establish connectivity with the PostgreSQL database previously installed within our cluster in this repo here.

## Dockerize Your Application
Upon having your Spring Boot application, it is imperative to construct it as a docker image for deployment in your Kubernetes cluster.

Docker requires specifications for building this image, which are provided through a Dockerfile located at the root of your project.

The Dockerfile is structured as follows:

```bash
FROM openjdk:17-jdk-slim
VOLUME /tmp
COPY build/libs/quick-queue-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT java -jar -Dspring.datasource.url=jdbc:postgresql://${DB_HOST}:5432/postgres?currentSchema=quick_queue -Dspring.datasource.username=${DB_USER} -Dspring.datasource.password=${DB_PW} ./app.jar
```

The first line instructs Docker to base the image on the openjdk:17-jdk-slim image. 

Then, it maps the /tmp folder to a temporary folder on the host, necessary as Spring Boot utilizes the /tmp folder. 

Next, the application JAR is copied into the image as app.jar. 

The ENTRYPOINT directive defines how Docker should execute your application once the container is instantiated. 

## Building the Image
To build your Docker image from the application folder, execute the following commands:

```bash
gradle build -x test
docker build --platform linux/amd64 -t qq_app:q1 .
```

## Running Your Application in a Container
Create a Docker container using the following command:

```bash
docker run -d -p 9000:9000 -p 9191:9191 -e DB_HOST=host.docker.internal -e DB_USER=postgres -e DB_PW=<password> qq_app:q1
```

## Pushing Image to Docker Hub
To upload the image to our Docker Hub repository, follow these steps:

```bash
docker login
docker tag qq_app:q1 odennav/quickqueue:q1
docker push odennav/quickqueue:q1
```

Here, the dockerhub username utilized is odennav, while the :q1 tag denotes a specific version of the image within the repository.

## Stopping the Container
To halt the container, execute the following commands:

```bash
docker ps
docker stop <container id>
docker container rm <container id>
```

For deploying to the Kubernetes (K8s) cluster, refer to the repository linked here for insights into how this Docker image is deployed.




### Reference Documentation
For further reference, please consider the following sections:

* [Official Gradle documentation](https://docs.gradle.org)
* [Spring Boot Gradle Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/3.1.10-SNAPSHOT/gradle-plugin/reference/html/)
* [Create an OCI image](https://docs.spring.io/spring-boot/docs/3.1.10-SNAPSHOT/gradle-plugin/reference/html/#build-image)
* [Spring Web](https://docs.spring.io/spring-boot/docs/3.1.10-SNAPSHOT/reference/htmlsingle/index.html#web)
* [Spring Data JPA](https://docs.spring.io/spring-boot/docs/3.1.10-SNAPSHOT/reference/htmlsingle/index.html#data.sql.jpa-and-spring-data)

