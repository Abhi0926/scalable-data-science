# zeppelin with spark 

## run latest quickly
For the latest zeppelin run:

```
docker run -d -p 8080:8080 -p 8443:8443 xemuliam/zeppelin
```

## build your own

Building a docker image, pushing it to docker hub and running it:
```
  298  docker build -t zeppelin .
  300  docker images
  301  docker run zeppelin
  302  docker run --rm -p 8080:8080 zeppelin
  303  docker images
  304  docker tag 67a628bc6685 raazesh/zeppelin:latest
  305  docker images
  306  docker login
  307  docker push raazesh/zeppelin
  308  docker images
  309  docker rmi -f 67a628bc6685 
  310  docker images
  315  docker run -v $HOME/all/git/scalable-data-science:/sds --rm -p 8080:8080 raazesh/zeppelin
```

This is building on:
 * https://github.com/dylanmei/docker-zeppelin


A `debian:jessie` based Spark and [Zeppelin](http://zeppelin.apache.org) Docker container.

This image is large and opinionated. It contains:

- [Spark 2.0.0](http://spark.apache.org/docs/2.0.0) and [Hadoop 2.7.2](http://hadoop.apache.org/docs/r2.7.2)
- [PySpark](http://spark.apache.org/docs/2.0.0/api/python) support with [Python 3.4](https://docs.python.org/3.4), [NumPy](http://www.numpy.org), [PandaSQL](https://github.com/yhat/pandasql), and [SciPy](https://www.scipy.org/scipylib/index.html), but no matplotlib.
- A partial list of interpreters out-of-the-box. If your favorite interpreter isn't included, consider [adding it with the api](http://zeppelin.apache.org/docs/0.7.0-SNAPSHOT/manual/dynamicinterpreterload.html).
  - spark
  - shell
  - angular
  - markdown
  - postgresql
  - jdbc
  - python
  - hbase
  - elasticsearch

A prior build of `dylanmei/zeppelin:latest` contained Spark 1.6.0, Python 2.7, and **all** of the stock interpreters. That image is still available as `dylanmei/zeppelin:0.6.0-stable`.

## simple usage

To start Zeppelin pull the `latest` image and run the container:

```
docker pull dylanmei/zeppelin
docker run --rm -p 8080:8080 dylanmei/zeppelin
```

Zeppelin will be running at `http://${YOUR_DOCKER_HOST}:8080`.

## complex usage

You can use [docker-compose](http://docs.docker.com/compose) to easily run Zeppelin in more complex configurations. See this project's `./examples` directory for examples of using Zeppelin with `docker-compose` to :

- read and write from local data files
- read and write documents in ElasticSearch

## onbuild

The Docker `onbuild` container is still a part of this project, but **I have no plans to keep it updated**. See the `onbuild` directory to view its `Dockerfile`.

To use it, create a new `Dockerfile` based on `dylanmei/zeppelin:onbuild` and supply a new, executable `install.sh` file in the same directory. It will override the base one via Docker's [ONBUILD](https://docs.docker.com/reference/builder/#onbuild) instruction.

The steps, expressed here as a script, can be as simple as:

```
#!/bin/bash
cat > ./Dockerfile <<DOCKERFILE
FROM dylanmei/zeppelin:onbuild

ENV ZEPPELIN_MEM="-Xmx1024m"
DOCKERFILE

cat > ./install.sh <<INSTALL
git pull
mvn clean package -DskipTests \
  -Pspark-1.5 \
  -Dspark.version=1.5.2 \
  -Phadoop-2.2 \
  -Dhadoop.version=2.0.0-cdh4.2.0 \
  -Pyarn
INSTALL

docker build -t my_zeppelin .
```

## license

MIT

