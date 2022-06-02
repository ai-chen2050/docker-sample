# InfluxDB & Grafana

## Background 

- 此处使用 InfluxDB 2.0 版本。其中，Grafana 的版本为 latest。
  
- 注意事项：
  - influxDB 2.0 和 1.0 的数据查询模型不一样。1.0 叫 influxQL, 2.0 的叫 flux. 2.0 的查询语言更像脚本语言， 1.0 的 QL 类似 SQL。而导入的 Dashboard Json query 使用的是 1.0 的 influxQL。
  - InfluxDB 2.0 中数据对应的叫 bucket 概念，不在存在 database 的概念。  

  - 本用例是为了 ETH系 metric 监控指标而搭建。其中，若使用 influxDB 2.0，则需要注意配套使用 blockchain 中 `--influxdbV2` 以及`bucket、token` 进行推送数据。

  - 为了兼容 [Eth Dashboard](https://grafana.com/grafana/dashboards/13877), 此 Config Query 使用了 1.0 的 influxQL 进行数据查询。故需要进行如下操作。
    - 首先，要在 2.0 的数据库里触发一个兼容 1.0 DB `influx v1 dbrp`的指令。
    - 其次，Grafana 选择查询模型的时候选择 1.0 的 influxQL 进行连接。
    - 否则，请使用 influxDB 1.0. 参考 [eth grafana](https://ethereum.org/en/developers/tutorials/monitoring-geth-with-influxdb-and-grafana/) 进行搭建。


## InfluxDB 2.x

[InfluxDB docker doc](https://hub.docker.com/_/influxdb)

如果你想继续支持 influxDB 1.0 influxQL 的查询及其写入，你需要将 2.0 的 bucket map to 1.0 的 database. 则需要执行如下命令，或者可以启动 docker,初始化之后手动执行。反之可以忽略:

- 1、创建容器启动初始化脚本文件 `setup-v1.sh`

```sh
mkdir -p $PWD/influx/scripts
touch setup-v1.sh
```
- 2、将如下内容写入 `setup-v1.sh`
```sh
#!/bin/bash
set -e

influx v1 dbrp create \
  --bucket-id ${DOCKER_INFLUXDB_INIT_BUCKET_ID} \
  --db ${V1_DB_NAME} \
  --rp ${V1_RP_NAME} \
  --default \
  --org ${DOCKER_INFLUXDB_INIT_ORG}

influx v1 auth create \
  --username ${V1_AUTH_USERNAME} \
  --password ${V1_AUTH_PASSWORD} \
  --write-bucket ${DOCKER_INFLUXDB_INIT_BUCKET_ID} \
  --org ${DOCKER_INFLUXDB_INIT_ORG}
```

- 3、启动 docker container.

```sh
docker run -d -p 8086:8086 \
      --name influxdb \
      --net=influxdb   \
      -v $PWD/influx/data:/var/lib/influxdb2 \
      -v $PWD/influx/config:/etc/influxdb2 \
      -v $PWD/influx/scripts:/docker-entrypoint-initdb.d \
      -e DOCKER_INFLUXDB_INIT_MODE=setup \
      -e DOCKER_INFLUXDB_INIT_USERNAME=my-user \
      -e DOCKER_INFLUXDB_INIT_PASSWORD=my-password \
      -e DOCKER_INFLUXDB_INIT_ORG=my-org \
      -e DOCKER_INFLUXDB_INIT_BUCKET=my-bucket \
      -e V1_DB_NAME=v1-db \
      -e V1_RP_NAME=v1-rp \
      -e V1_AUTH_USERNAME=v1-user \
      -e V1_AUTH_PASSWORD=v1-password \
      influxdb:2.0
```

## Grafana

如果需要将数据持久化，可参见[社区讨论](https://community.grafana.com/t/new-docker-install-with-persistent-storage-permission-problem/10896/5)

- 我们这里使用映射到 host 文件夹的方式持久化。

- 1、当前用户下创建文件夹 

```sh
mkdir -p $PWD/grafana/data
```

- 2、启动 docker container.

```sh
uid = $(id -u)
docker run -d  --name grafana  --user uid  --net=influxdb -v $PWD/grafana/data:/var/lib/grafana  -p 7000:3000 grafana/grafana
```
- 3、配置数据源 http://localhost:7000/ admin admin
    - 连接时选择 influxQL
    - IP 需要填写 host IP or influxdb 
      (containner name ,cos same network)
    - DB: v1-db, user: v1-user, pw: v1-password
    - connect & testing & work.


## docker compose 

参见文件 [influxDB & grafana docker compose file](./influxdb-grafana.yaml)

启动 docker-compose 命令如下所示：

```sh
export UID; docker-compose -f compose.yaml up -d
```