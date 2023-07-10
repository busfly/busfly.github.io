<div style="color:#16b0ff;font-size:50px;font-weight: 900;text-shadow: 5px 5px 10px var(--theme-color);font-family: 'Comic Sans MS';">Monitor</div>

<span style="color:#16b0ff;font-size:20px;font-weight: 900;font-family: 'Comic Sans MS';">Introduction</span>：收纳日志监控分析告警相关的知识总结！

[TOC]

# 背景

## 系统监控

- SkyWalking
- Zabbix



## 业务监控(APM)

- Pinpoint
- Prometheus



# Prometheus

## prometheus

### Linux

```shell
# 下载
wget https://github.com/prometheus/prometheus/releases/download/v2.35.0-rc1/prometheus-2.35.0-rc1.linux-amd64.tar.gz

# 解压
tar -zxvf prometheus-2.35.0-rc1.linux-amd64.tar.gz
mv prometheus-2.35.0-rc1.linux-amd64 prometheus

# 启动
./prometheus --config.file=./prometheus.yml --web.enable-lifecycle
# --config.file=./prometheus.yml 指定配置
# --web.enable-lifecycle这个参数的主要目的是为了运行时重载配置

# 测试地址：http://localhost:9090/targets
```



### Docker

```shell
# 启动prometheus
docker run -d --name=prometheus -p 9090:9090 \
-v /home/soft/monitor/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml \
-v /etc/localtime:/etc/localtime:ro \
prom/prometheus
```



### 配置





## node_exporter

node_exporter用于采集服务器层面的运行指标，包括机器的loadavg、filesystem、meminfo等基础监控，类似于传统主机监控维度的zabbix-agent。node_exporter由prometheus官方提供、维护，不会捆绑安装，但基本上是必备的exporter。建议最好安装在linux上使用。

### 下载安装

```shell
# 下载地址https://github.com/prometheus/node_exporter/releases
wget https://github.91chi.fun//https://github.com//prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-386.tar.gz

# 解压
tar -zxvf node_exporter-1.3.1.linux-386.tar.gz

# 启动node_exporter
nohup ./node_exporter --web.listen-address 172.16.1.167:8081 &

# 访问地址：http://172.16.1.167:8081/metrics
```



### 配置方式

```yaml
  - job_name: 'node_local'
    static_configs:
      - targets: ['localhost:9100']
```



### 监控模板

https://grafana.com/grafana/dashboards/1860

https://grafana.com/grafana/dashboards/9276



## mysql_exporter

### 下载安装

```shell
# 下载地址：https://github.com/prometheus/mysqld_exporter/releases
wget https://github.91chi.fun//https://github.com//prometheus/mysqld_exporter/releases/download/v0.14.0/mysqld_exporter-0.14.0.linux-386.tar.gz

export DATA_SOURCE_NAME='root:123456@(127.0.0.1:3306)/'
```



### 监控模板

https://grafana.com/grafana/dashboards/7362





# Grafana

## 安装启动

### Docker安装

```shell
# 启动grafana
docker run -d -p 3000:3000 --name=grafana \
-v /home/soft/monitor/grafana/Grafana-storage:/var/lib/Grafana \
-v /etc/localtime:/etc/localtime:ro \
grafana/grafana
```

**使用方式**

- 第一步：添加数据源prometheus
- 第二步：导入或创建Dashboard





## cAdvisor

### Docker安装

Docker部署cAdvisor，用于监控docker的容器的运行指标。

```shell
# 启动cAdvisor
docker run -d \
–volume=/:/rootfs:ro \
–volume=/var/run:/var/run:ro \
–volume=/sys:/sys:ro \
–volume=/var/lib/docker/:/var/lib/docker:ro \
–volume=/dev/disk/:/dev/disk:ro \
-v /etc/localtime:/etc/localtime:ro \
–publish=8080:8080 \
–detach=true \
–name=cadvisor \
google/cadvisor:latest
```

访问方法：192.168.200.137:8080 可以看到cAdvisor是否启动
访问：http://192.168.200.137:8080/metrics有数据就说明cAdvisor是启动好了的



### 加入配置

```yaml
- job_name: 'docker'
    static_configs:
      - targets: ['192.168.200.137:8080']
        labels:
          instance: docker
```



### 监控模板

https://grafana.com/grafana/dashboards/193



## spring-boot-admin

监控springboot。

### 依赖包

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<!--prometheus监控  https://prometheus.io/docs/introduction/overview/-->
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```



### 配置信息

```yaml
scrape_configs:
  - job_name: 'demo-server-monitor'
    metrics_path: '/actuator/prometheus'
    scrape_interval: 2s
    static_configs:
      - targets: ['localhost:8080']
```



### 监控模板

https://grafana.com/grafana/dashboards/12900





# Sentry



# SkyWalking



