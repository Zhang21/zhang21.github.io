# OpenTelemetry


OpenTelemetry 学习。

<br/>

<!--more-->

<br/>

参考：

- [OpenTelemetry](https://opentelemetry.io/zh/docs/what-is-opentelemetry/)

<br/>

# 概述

OpenTelemetry 是一个可观测性框架和工具包， 旨在创建和管理遥测数据，如链路、 指标和日志。它可以与各种可观测性后端一起使用，如 Jaeger 和 Prometheus。

OpenTelemetry 专注于遥测数据的生成、采集、管理和导出。重要的是，遥测数据的存储和可视化是有意留给其他工具处理的。

可观测性是通过检查系统输出来理解系统内部状态的能力。在软件的背景下，这意味着能够通过检查遥测数据来理解系统的内部状态。

要使系统可观测，必须对其进行仪表化。也就是说，代码必须发出链路、指标或日志。 然后，仪表化的数据必须发送到可观测性后端。

<br/>

---

<br/>

# 概念

<br/>
<br/>

## 可观测性入门

<br/>
<br/>

### 什么是可观测性

可观测性让你能够从外部理解一个系统，它允许你在不了解系统内部运作的情况下，对该系统提出问题。更重要的是， 它能帮你轻松排查和处理新出现的问题。

要对你的系统提出这些问题，你的应用程序必须进行适当的插桩。也就是说，应用程序代码必须能够发出信号， 例如链路、 指标和日志。

OpenTelemetry 就是一种为应用程序代码进行插桩的机制，它的目的是帮助使系统变得可观测。

<br/>
<br/>

### 分布式链路

分布式链路，通常简称为链路，记录了请求在多服务架构中传播的路径。

<br/>
<br/>

## 上下文传播

上下文是一个对象，它包含发送和接收服务用于将一个信号与另一个信号关联起来的信息。

传播是上下文在服务和进程之间移动的机制。

通过上下文传播，信号可以相互关联。

<br/>
<br/>

## 信号

信号是系统输出，描述了系统和平台上运行的应用程序的底层活动。

OpenTelemetry 支持以下信号：

- 追踪（Trace）
- 指标（Metric）
- 日志（Log）
- 行李（Baggage）

<br/>
<br/>

### 追踪

追踪是请求经过应用程序的路径，可以了解向应用程序发出请求时发生了什么。

<br/>

TracerProvider 通常是使用 OT 追踪的第一步。

Tracer 可创建 span，包含有关操作所发生情况的更多信息。

Trace exporter 将追踪信息发送到消费者（标准输出、OT collector 或开源后端）。

<br/>

Span 表示一个操作单元，它是追踪的组成部分。包括如下信息：

- name
- pid
- scontext
- timestamp
- attribute
- event
- link
- status

一个示例：

```json
{
  "name": "/v1/sys/health",
  "context": {
    "trace_id": "7bba9f33312b3dbb8b2c2c62bb7abe2d",
    "span_id": "086e83747d0e381e"
  },
  "parent_id": "",
  "start_time": "2021-10-22 16:04:01.209458162 +0000 UTC",
  "end_time": "2021-10-22 16:04:01.209514132 +0000 UTC",
  "status_code": "STATUS_CODE_OK",
  "status_message": "",
  "attributes": {
    "net.transport": "IP.TCP",
    "net.peer.ip": "172.17.0.1",
    "net.peer.port": "51820",
    "net.host.ip": "10.177.2.152",
    "net.host.port": "26040",
    "http.method": "GET",
    "http.target": "/v1/sys/health",
    "http.server_name": "mortar-gateway",
    "http.route": "/v1/sys/health",
    "http.user_agent": "Consul Health Check",
    "http.scheme": "http",
    "http.host": "10.177.2.152:26040",
    "http.flavor": "1.1"
  },
  "events": [
    {
      "name": "",
      "message": "OK",
      "timestamp": "2021-10-22 16:04:01.209512872 +0000 UTC"
    }
  ]
}
```

<br/>
<br/>

### 指标

指标是运行时捕获的测量值（包括时间和相关元数据）。

<br/>

MeterProvider 通常是使用 OT 计量的第一步。

Meter 创建指标，它由 MeterProvider 创建。

Metric Exporter 发送指标到消费者。

<br/>

指标定义如下：

- name
- kind
  - counter
  - gauge
  - histogram
- unit（可选）
- description（可选）

<br/>
<br/>

### 日志

日志是事件的记录。

<br/>
<br/>

### 行李

行李是信号之间传递的上下文信息，它是键值存储。

<br/>
<br/>

## 仪器化

要使系统可观测，必须对其进行仪器化：也就是说，系统组件必须发出追踪、指标和日志。

使用 OT，要使代码仪器化，有两种主要方式：

- 基于代码的方案（通过 API 或 SDK）
- 零代码的方案

<br/>
<br/>

### 零代码的方案

在无需编写代码的情况下，为应用程序添加可观测性。

通常作为一个代理或类似代理的安装。从字节码操作、猴子补丁或 eBPF 将 OT API 和 SDK 的调用注入应用程序。

![零代码](https://raw.githubusercontent.com/zhang21/images/master/cs/monitor/opentelemetry/zero-code.svg)

<br/>
<br/>

### 基于代码的方案

基于代码的仪器化。

- 导入 OT API 和 SDK。
- 配置 OT API。
- 配置 OT SDK。
- 创建 Telemetry 数据。
- 导出数据。
  - 直接从进程导出：依赖一个或多个探针，将信息转换为特定类型，来适配如 Jaeger 或 Prometheus 等分析工具。
  - 通过 OT Collector 代理导出：所有 OT SDK 都支持 OTLP 协议，该协议可向 OT Collector 发送数据。

<br/>
<br/>

### 库的方案

OT 为许多库提供了仪器化库，这通常是通过库钩子或猴子补丁来实现的。

<br/>
<br/>

## 组件

OT 主要由以下主要部分构成：

- 规范
- Collector：接收、处理和遥测数据。
- API 和 SDK
  - 插桩库
  - 导出器
  - 零代码插桩
  - 资源检测器
  - 跨服务传输器
  - 采样器
- K8s Operator
- FaaS

<br/>

---

<br/>

# 演示

演示：<https://opentelemetry.io/zh/docs/demo/>

<br/>

---

<br/>

# API和SDK仪器化

参考: <https://opentelemetry.io/zh/docs/languages/>

OpenTelemetry 代码仪器化支持多种编程语言。

```txt
                                          -----> Jaeger/其它 (trace)
App + SDK ---> OpenTelemetry Collector ---|
                                          -----> Prometheus (metrics)

```

<br/>

---

<br/>

# 零代码仪器化

参考: <https://opentelemetry.io/zh/docs/zero-code/>

<br/>

一些示例

```sh
# opentelemetry-特定语言-instrument 软件，程序在启动时指定相关参数
# 运行 OT Go Auto 需要 root 权限
sudo OTEL_GO_AUTO_TARGET_EXE=/home/bin/service_executable OTEL_SERVICE_NAME=my_service OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318 ./otel-go-instrumentation


# java agent
java -javaagent:path/to/opentelemetry-javaagent.jar -Dotel.service.name=your-service-name -jar myapp.jar


# nodejs
npm install --save @opentelemetry/api
npm install --save @opentelemetry/auto-instrumentations-node
env OTEL_TRACES_EXPORTER=otlp OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=your-endpoint \
node --require @opentelemetry/auto-instrumentations-node/register app.js

```

<br/>

---

<br/>

# Collector

收集器接收、处理和导出遥测数据。它具有更好的扩展性，支持向一个或多个后端发送可观测性数据格式（如 Jaeger, Prometheus 等）。

虽然可以直接将数据发送给后端，但建议使用收集器。因为它可以让你快速卸取数据，还可进行重试、批处理等功能。

![收集器](https://raw.githubusercontent.com/zhang21/images/master/cs/monitor/opentelemetry/otel-collector.svg)

<br/>
<br/>

## 端口信息

| 端口 | 协议 | 介绍 |
| - | - | - |
| 4317 | gRPC | 接收 OTLP 遥测数据（traces, metrics, logs）|
| 4318 | http | 接收 OTLP 遥测数据 |

<br/>
<br/>

## 快速开始

<br/>
<br/>

### 先决条件

- docker 或其他容器运行时
- Go 对应版本
- GOBIN 环境变量

```sh
export GOBIN=${GOBIN:-$(go env GOPATH)/bin}

```

<br/>
<br/>

### 配置环境

```sh
# 拉取镜像
docker pull otel/opentelemetry-collector-contrib:0.118.0

# 安装 telemetrygen，用于生成遥测数据，做测试
go install github.com/open-telemetry/opentelemetry-collector-contrib/cmd/telemetrygen@latest

```

<br/>
<br/>

### 生成并收集数据

```sh
# 启动收集器
docker run \
  -p 127.0.0.1:4317:4317 \
  -p 127.0.0.1:4318:4318 \
  -p 127.0.0.1:55679:55679 \
  otel/opentelemetry-collector-contrib:0.118.0 \
  2>&1 | tee collector-output.txt

# 生成一些追踪数据
$GOBIN/telemetrygen traces --otlp-insecure --traces 3

# 查看日志中的收集活动
grep -E '^Span|(ID|Name|Kind|time|Status \w+)\s+:' ./collector-output.txt

# 通过浏览器查看数据
curl http://localhost:55679/debug/tracez

# 退出收集器容器
ctrl-c
```

<br/>
<br/>

## 安装收集器

<br/>
<br/>

### Docker安装

```sh
docker pull otel/opentelemetry-collector-contrib:0.118.0
docker run otel/opentelemetry-collector-contrib:0.118.0

# 指定配置
docker run -v $(pwd)/config.yaml:/etc/otelcol-contrib/config.yaml otel/opentelemetry-collector-contrib:0.118.0
# 映射端口
```

<br/>
<br/>

### Docker-Compose安装

```yml
# docker-compose.yaml
otel-collector:
  image: otel/opentelemetry-collector-contrib
  volumes:
    - ./otel-collector-config.yaml:/etc/otelcol-contrib/config.yaml
  ports:
    - 1888:1888 # pprof extension
    - 8888:8888 # Prometheus metrics exposed by the Collector
    - 8889:8889 # Prometheus exporter metrics
    - 13133:13133 # health_check extension
    - 4317:4317 # OTLP gRPC receiver
    - 4318:4318 # OTLP http receiver
    - 55679:55679 # zpages extension

```

<br/>
<br/>

### K8S安装

yaml 、helm chart 或 OpenTelemetry Operator。

<br/>
<br/>

### Linux安装

```sh
# deb 或 rpm 包安装
wget https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v0.118.0/otelcol_0.118.0_linux_amd64.rpm
sudo rpm -ivh otelcol_0.118.0_linux_amd64.rpm


# 二进制安装
curl --proto '=https' --tlsv1.2 -fOL https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v0.118.0/otelcol_0.118.0_linux_amd64.tar.gz
tar -xvf otelcol_0.118.0_linux_amd64.tar.gz


# 源码安装
git clone https://github.com/open-telemetry/opentelemetry-collector.git
cd opentelemetry-collector
make install-tools
make otelcorecol

```

<br/>
<br/>

## 部署

<br/>
<br/>

### 无收集器

最简单的模式是不使用收集器，直接向后端输出遥测数据。

![无收集器](https://raw.githubusercontent.com/zhang21/images/master/cs/monitor/opentelemetry/otel-sdk.svg)

<br/>
<br/>

### 使用收集器

![使用收集器](https://raw.githubusercontent.com/zhang21/images/master/cs/monitor/opentelemetry/otel-agent-sdk.svg)

<br/>

示例

```sh
export OTEL_EXPORTER_OTLP_ENDPOINT=http://collector.example.com:4318

```

配置

```yml
receivers:
  otlp: # the OTLP receiver the app is sending traces to
    protocols:
      http:
        endpoint: 0.0.0.0:4318

# trace
processors:
  batch:

exporters:
  otlp/jaeger: # Jaeger supports OTLP directly
    endpoint: https://jaeger.example.com:4317

service:
  pipelines:
    traces/dev:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp/jaeger]

```

<br/>
<br/>

### 网关模式

向一个或多个收集器实例提供的单个 OTLP 端点地址发送遥测数据。一般是使用负载均衡器。

![负载均衡](https://raw.githubusercontent.com/zhang21/images/master/cs/monitor/opentelemetry/otel-gateway-sdk.svg)

<br/>

nginx 代理负载的示例

```conf
# grpc 4317
server {
    listen 4317 http2;
    server_name _;

    location / {
            grpc_pass      grpc://collector4317;
            grpc_next_upstream     error timeout invalid_header http_500;
            grpc_connect_timeout   2;
            grpc_set_header        Host            $host;
            grpc_set_header        X-Real-IP       $remote_addr;
            grpc_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

# http 4318
server {
    listen 4318;
    server_name _;

    location / {
            proxy_pass      http://collector4318;
            proxy_redirect  off;
            proxy_next_upstream     error timeout invalid_header http_500;
            proxy_connect_timeout   2;
            proxy_set_header        Host            $host;
            proxy_set_header        X-Real-IP       $remote_addr;
            proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

upstream collector4317 {
    server collector1:4317;
    server collector2:4317;
}

upstream collector4318 {
    server collector1:4318;
    server collector2:4318;
}

```

<br/>

将收集器作为代理和网关进行部署。

在部署多个收集器时，通常既要作为网关，又要作为代理。

![网关架构](https://raw.githubusercontent.com/zhang21/images/master/cs/monitor/opentelemetry/otel-gateway-arch.svg)

<br/>
<br/>

## 配置

```sh
# 验证配置文件
otelcol validate --config=config.yaml

# 使用配置文件启动
otelcol --config=env:MY_CONFIG_IN_AN_ENVVAR --config=https://server/config.yaml

```

<br/>
<br/>

### 配置文件结构

配置文件由四类管道组件组成：

- receivers
- processors
- exporters
- connectors

配置每个组件后，必须在 service 的 pipeline 中启动它。

还可以配置 extensions 组件来扩展功能，如添加诊断工具等。扩展不需要直接访问遥测数据。

一般来说，最好绑定到 localhost，下面使用 `0.0.0.0` 是出于测试方便使用。

请注意，管道是通过组件标识符定义的（`type[/name]`），如 otlp 和 otlp/2。只要标识符唯一，就可以多次定义给定类型的组件。

<br/>

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
  otlp/2:
    protocols:
      grpc:
        endpoint: 0.0.0.0:55690

processors:
  batch:
  batch/test:

exporters:
  otlp:
    endpoint: otelcol:4317
  otlp/2:
    endpoint: otelcol2:4317

extensions:
  health_check:
  pprof:
  zpages:

service:
  extensions: [health_check, pprof, zpages]
  pipelines:
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp]
    traces/2:
      receivers: [otlp/2]
      processors: [batch/test]
      exporters: [otlp/2]
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp]
    logs:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp]

```

<br/>

配置文件同样可以包含其他文件，收集器会将整体配置合并到内存中。

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317

exporters: ${file:exporters.yaml}

service:
  extensions: []
  pipelines:
    traces:
      receivers: [otlp]
      processors: []
      exporters: [otlp]
```

<br/>
<br/>

### Receivers

receivers 从一个或多个源收集遥测数据。可以是 pull 或 push 模式。

默认是程序主动推送 OTLP 遥测数据到收集器。OTLP 协议没有原生的 pull 模式，要通过其他方式来实现，有些麻烦。

许多接收器都带有默认配置项，因此只需指定接收器名称即可。

配置了 receivers 并没有启用它，需要在 service 中启用。

收集器需要一个或多个接收器。

```yaml
receivers:
  # Data sources: logs
  fluentforward:
    endpoint: 0.0.0.0:8006

  # Data sources: traces
  jaeger:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      thrift_binary:
      thrift_compact:
      thrift_http:

  # Data sources: traces, metrics, logs
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
        tls:
          cert_file: cert.pem
          key_file: cert-key.pem
      http:
        endpoint: 0.0.0.0:4318

  # Data sources: metrics
  prometheus:
    config:
      scrape_configs:
        - job_name: otel-collector
          scrape_interval: 5s
          static_configs:
            - targets: [localhost:8888]

  # Data sources: traces
  zipkin:
```

<br/>
<br/>

### Processors

processsors 获取接收器收集的数据，并对其进行修改或转换，然后发送给 exporters。管道中处理器的顺序决定了收集器对数据进行操作的顺序。

此部分配置是可选的。

配置 processors 不会启用它，需要在 service 管道中启用。

```yaml
processors:
  # Data sources: traces
  attributes:
    actions:
      - key: environment
        value: production
        action: insert
      - key: db.statement
        action: delete
      - key: email
        action: hash

  # Data sources: traces, metrics, logs
  batch:

  # Data sources: metrics, metrics, logs
  filter:
    error_mode: ignore
    traces:
      span:
        - 'attributes["container.name"] == "app_container_1"'
        - 'resource.attributes["host.name"] == "localhost"'
        - 'name == "app_3"'
      spanevent:
        - 'attributes["grpc"] == true'
        - 'IsMatch(name, ".*grpc.*")'
    metrics:
      metric:
        - 'name == "my.metric" and resource.attributes["my_label"] == "abc123"'
        - 'type == METRIC_DATA_TYPE_HISTOGRAM'
      datapoint:
        - 'metric.type == METRIC_DATA_TYPE_SUMMARY'
        - 'resource.attributes["service.name"] == "my_service_name"'
    logs:
      log_record:
        - 'IsMatch(body, ".*password.*")'
        - 'severity_number < SEVERITY_NUMBER_WARN'

  # Data sources: traces, metrics, logs
  memory_limiter:
    check_interval: 5s
    limit_mib: 4000
    spike_limit_mib: 500

  # Data sources: traces
  resource:
    attributes:
      - key: cloud.zone
        value: zone-1
        action: upsert
      - key: k8s.cluster.name
        from_attribute: k8s-cluster
        action: insert
      - key: redundant-attribute
        action: delete

  # Data sources: traces
  probabilistic_sampler:
    hash_seed: 22
    sampling_percentage: 15

  # Data sources: traces
  span:
    name:
      to_attributes:
        rules:
          - ^\/api\/v1\/document\/(?P<documentId>.*)\/update$
      from_attributes: [db.svc, operation]
      separator: '::'

```

<br/>
<br/>

### Exporters

Exporters 将数据发送到一个或多个后端，可以是 pull 或 push 模式。

配置 exporters 并没有启用它，需要在 service 的管道中启用。

```yaml
exporters:
  # Data sources: traces, metrics, logs
  file:
    path: ./filename.json

  # Data sources: traces
  otlp/jaeger:
    endpoint: jaeger-server:4317
    tls:
      cert_file: cert.pem
      key_file: cert-key.pem

  # Data sources: traces, metrics, logs
  otlp:
    endpoint: otelcol2:4317
    tls:
      cert_file: cert.pem
      key_file: cert-key.pem

  # Data sources: traces, metrics
  otlphttp:
    endpoint: https://otlp.example.com:4318

  # Data sources: metrics
  prometheus:
    endpoint: 0.0.0.0:8889
    namespace: default

  # Data sources: metrics
  prometheusremotewrite:
    endpoint: http://prometheus.example.com:9411/api/prom/push
    # When using the official Prometheus (running via Docker)
    # endpoint: 'http://prometheus:9090/api/v1/write', add:
    # tls:
    #   insecure: true

  # Data sources: traces
  zipkin:
    endpoint: http://zipkin.example.com:9411/api/v2/spans
```

<br/>
<br/>

### Connectors

Connectors 连接两个管道，同时充当 exporter 和 reciever。可使用连接器汇总消费数据、复制数据或路由数据。

配置 connectors 并没有启用，需要在 service 管道中启用。

```yaml
receivers:
  foo:

exporters:
  bar:

connectors:
  count:
    spanevents:
      my.prod.event.count:
        description: The number of span events from my prod environment.
        conditions:
          - 'attributes["env"] == "prod"'
          - 'name == "prodevent"'

service:
  pipelines:
    traces:
      receivers: [foo]
      exporters: [count]
    metrics:
      receivers: [count]
      exporters: [bar]

```

<br/>
<br/>

### Extensions

extensions 扩展收集器的功能，是可选的，以完成与处理遥测数据不直接相关的任务。如，为收集器健康检查和服务发现等添加扩展。

配置 extensions 没有启用，需要在 service 的管道中启用。

```yaml
extensions:
  health_check:
  pprof:
  zpages:

```

<br/>
<br/>

### Service

service 部分用于配置在收集器中启用哪些组件。

service 有以下三部分：

- extensions
- pipelines
- telemetry

<br/>

extensions 表示启用哪些扩展。

```yaml
service:
  extensions: [health_check, pprof, zpages]

```

<br/>

pipelines 是配置管道的地方，可配置以下类型的管道：

- trace：收集和处理追踪数据
- metrics：收集和处理指标数据
- logs：收集和处理日志数据

管道由一组 receivers, processors 和 exporters 组成，也就是启用哪些。

可以在多个管道中使用同一个 receiver, processor 或 exporter。当一个处理器在多个管道中被引用时，每个管道都会获得处理器的一个单独实例。

```yaml
service:
  pipelines:
    metrics:
      receivers: [opencensus, prometheus]
      processors: [batch]
      exporters: [opencensus, prometheus]
    traces:
      receivers: [opencensus, jaeger]
      processors: [batch, memory_limiter]
      exporters: [opencensus, zipkin]
```

<br/>

telemetry 部分，可设置收集器自身的可观测性。它包括：

- logs
- metrics

<br/>
<br/>

### 其他信息

使用环境变量。

```yaml
processors:
  attributes/example:
    actions:
      - key: ${env:DB_KEY}
        action: ${env:OPERATION}
```

<br/>

代理。

<br/>

收集器中的认证机制通过扩展来实现。

<br/>

配置证书。

<br/>
<br/>

### 覆盖配置

使用 `--set` 配置项覆盖已有配置。

```yaml
otelcol --set "exporters::debug::verbosity=detailed"
```

<br/>
<br/>









