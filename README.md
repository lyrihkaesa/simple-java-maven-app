# simple-java-maven-app

This repository is for the
[Build a Java app with Maven](https://jenkins.io/doc/tutorials/build-a-java-app-with-maven/)
tutorial in the [Jenkins User Documentation](https://jenkins.io/doc/).

The repository contains a simple Java application which outputs the string
"Hello world!" and is accompanied by a couple of unit tests to check that the
main application works as expected. The results of these tests are saved to a
JUnit XML report.

The `jenkins` directory contains an example of the `Jenkinsfile` (i.e. Pipeline)
you'll be creating yourself during the tutorial and the `jenkins/scripts` subdirectory
contains a shell script with commands that are executed when Jenkins processes
the "Deliver" stage of your Pipeline.


Update for Publish for SSH

```bash
docker run \
  --name jenkins-blueocean \
  --detach \
  --network jenkins \
  --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client \
  --env DOCKER_TLS_VERIFY=1 \
  --publish 49000:8080 \
  --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  --volume "$HOME":/home \
  --restart=on-failure \
  --env JAVA_OPTS="-Dhudson.plugins.git.GitSCM.ALLOW_LOCAL_CHECKOUT=true" \
  myjenkins-blueocean:2.426.2-1
```

install jre/jdk 17 pada vps ubuntu

```bash
sudo apt-get update
sudo apt-get install openjdk-17-jre
```

Default port prometheus 9090

```bash
docker run \
  -d \
  --name prometheus \
  --network jenkins \
  -p 9091:9090 \
  prom/prometheus
```

```bash
vi /etc/prometheus/prometheus.yml
```

```bash
  - job_name: "jenkins"
    metrics_path: /prom/
    static_configs:
       - targets: ["jenkins-blueocean:8080"]
```

Default port grafana 3000

```bash
docker run \
  -d \
  --name grafana \
  --network jenkins \
  -p 3031:3031 \
  -e "GF_SERVER_HTTP_PORT=3031" \
  grafana/grafana
```