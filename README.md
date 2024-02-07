# 方案概述

Microservice architecture open source solutions are designed to support:
- Development
- management of microservices.
- ServiceMesh based on Linkerd2, Amesh, or Istio.

# 通用组件

- CI/CD           | GitHub Action                | 自动化构建、测试、部署 | 促进持续集成和持续交付流程 |
- GitOPS          | FluxCD/Flagger               | 自动化Canary， Blue-Green,  A/B Testing | 应用渐进发布流程 |
- Scalability     | HPA/KEDA                     | 基于资源指标，事件状态，动态扩缩POD副本  | 弹性伸缩 |
- Gateway         | Apisix                       | 应用网关，熔断，限流。降级，流量调度  | 微服务网关 |
- Config Server   | Nacos                        | 配置中心，用于集中管理微服务的配置信息，支持动态更新配置  | 配置中心 |
- Registry Server | Consol/Nacos                 | 注册中心，管理服务实例的注册与发现，支持服务健康检查和负载均衡  | 注册中心 |
- Cache/DB        | redis/postgresql/mongodb     | 

# 其他

- Notifications   | Novu                         | The ultimate service for managing multi-channel notifications with a single API.
- Workflows       | Windmill                     | Developer platform for APIs, background jobs, workflows and UIs

# UPstream

- https://github.com/novuhq/novu


# CICD

## 流水线配置文件 
配置文件位于 .github/workflows/pipeline.yaml 由三个阶段组成：

1. 同步部署镜像：此阶段将同步chart包和应用镜像。
3. 设置 K3s：此阶段在远程服务器上设置 K3s 集群。
4. 部署应用：此阶段将chart包和应用镜像部署到 K3s 集群。

## 触发器

管道由以下事件触发：

- 当打开或更新拉取请求时。
- 当代码推送到主分支时。
- 当工作流程手动调度时。

## 环境变量

Pipeline env:

- TZ: 用于时间戳的时区。
- REPO: 制品存储库的名称。
- IMAGE: 要构建的 Docker 镜像的名称。
- TAG: 要分配给 Docker 镜像的标签。

Actions secrets:

- ADMIN_INIT_PASSWORD
- HELM_REPO_PASSWORD
- HELM_REPO_REGISTRY
- HELM_REPO_USER
- HOST_DOMAIN
- HOST_IP
- HOST_USER
- SSH_PRIVATE_KEY
- DNS_AK
- DNS_SK
