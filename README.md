# Docker Sample

## 说明
本项目提供常用的组件/工具使用`Docker`或`docker compose`的部署方法，便于更快的学习使用相关组件/工具。

## 已完成组件

### [pulsar](./pulsar)

> 相关链接：[pulsar官网](https://pulsar.apache.org), [pulsar官方文档](https://pulsar.apache.org/docs/zh-CN/standalone/)

- 支持单节点模式部署
- 支持集群模式部署

### [flink](./flink)

> 相关链接：[flink官方文档](https://flink.apache.org/)

**包含三个版本Flink集群部署方法**

1. Flink集群部署
    > 常规Flink集群

2. 挂载lib目录部署
   > 挂载flink客户端的lib目录，便于添加第三方依赖包

3. Flink-Monitor集群部署
    > 配置Flink集群的[Metrics](https://ci.apache.org/projects/flink/flink-docs-release-1.13/docs/ops/metrics/)转发至[Prometheus](https://prometheus.io/)，并通过[Grafana](https://grafana.com/)展示Metrics数据


### [elasticsearch](./elasticsearch)

> 相关链接：[Elasticsearch Guide](https://www.elastic.co/guide/en/elasticsearch/reference/8.1/index.html), [install with docker](https://www.elastic.co/guide/en/elasticsearch/reference/8.1/docker.html)

- 基于官方docker部署文档实现，es集群+kibana部署

### [kafka](./kafka/)

> 相关链接：[kafka doc](https://kafka.apache.org/documentation/#quickstart)，[kafka github](https://github.com/apache/kafka/)

- 基于Zookeeper的单节点kafka
- 基于Kraft的单节点kafka

### [InfluxDB & Grafana](./influxdb-grafana/)

> 相关链接：[InfluxDB docker doc](https://hub.docker.com/_/influxdb), [Grafana docker](https://grafana.com/docs/grafana/latest/installation/docker)

- 基于docker实现的 InfluxDB 2.0 + Grafana 的部署
  
  (适用于 ETH 系列的 metric 监控指标)

### [MongoDB](./mongodb/)

- 直接 docker-compose  *.yaml 启动即可


### [MongoDB](./postgre/)

- 直接 docker-compose  *.yaml 启动即可

