---
title: "DevOps 工程概述"
---

当今互联网产品研发的世界里唯快不破，迭代速度往往很快，有些企业甚至在一天时间内有多次迭代。在产品快速发展与迭代中，如何让项目产品平稳的落地，一套完善且可靠的持续集成 CI/CD 和 DevOps 解决方案就是多数企业和 IT 团队所期待的。

相较于 Express Edition (易捷版)，DevOps 工程是 Advanced Edition (高级版) 独有的功能，针对企业实际的快速迭代和快速交付业务需求和场景，可以发现很多企业和 IT 团队都有持续集成和持续交付的需求。DevOps 工程提供从仓库 (SVN/Git)、代码编译、镜像制作、镜像安全、推送到仓库、应用版本、到定时构建的端到端流水线设置，支持用户在开发、测试等环境下的端到端高效流水线能力，支持用户成员管理，同时提供完整的日志功能，记录 CI/CD 流水线的每个过程。

由于软件开发复杂度的增高和更多的协同工作，团队开发成员间如何更好地在协同工作中确保软件开发和交付质量，逐渐成为研发过程中不可回避的问题。众所周知，敏捷开发 (Agile) 在业内日趋流行，团队如何在不断变化的需求中快速适应和保证软件质量就变得极其重要了。而持续集成，就是专门为解决上述需求的软件开发实践。持续集成要求每次的集成都是通过自动化的构建来验证，包括自动编译、发布和测试，从而尽快地发现集成错误，让团队能够更快的开发内聚的软件，减轻了软件发布时的压力。

对企业而言，高效可靠的 CI/CD 流水线是一个 IT 组织实现软件应用与服务快速交付的基础，虽然业界流行的 CI/CD 工具很多，比如 Travis CI、Teamcity 和 Jenkins 等，但如今大量企业选择采用 Jenkins 来搭建其交付流水线。谈到 Jenkins，大部分人肯定耳熟能详，持续集成/持续交付，自动化部署工具，测试工具，以及丰富的插件支持，配合目前大多数开发工具, 简直是万能的瑞士军刀。

因此，在经过调研之后，我们的 DevOps 工程选择基于 Jenkins 提供可视化的 CI/CD 流水线构建或基于代码仓库已有的 Jenkinfile 构建流水线，正是因为 Jenkins 易配置、支持 JUnit/TestNG 测试报告、支持分布式构建、支持众多第三方插件等特点。KubeSphere 的 DevOps 工程下 Jenkins 流水线是基于 KubeSphere 的动态 Jenkins Slave，也就是说 Jenkins Slave 是具有动态伸缩的能力，能够根据任务的执行状态进行动态创建或自动注销释放资源。实际上， Jenkins Master 和 Jenkins Slave 以 Pod 形式运行在 KubeSphere 集群的 Node 上，Master 运行在其中一个节点，并且将其配置数据存储到一个 Volume 上去，Slave 运行在各个节点上，并且它不是一直处于运行状态，它会按照需求动态的创建并自动删除。

上述的工作流程可以理解为：当 Jenkins Master 接受到 Build 请求时，会根据配置的 Label 动态创建一个运行在 Pod 中的 Jenkins Slave 并注册到 Master 上，当运行完任务后，这个 Slave 会被注销并且这个 Pod 也会自动删除，恢复到最初状态。

所以，这种动态的 Jenkins Slave 优势就显而易见了：

- 动态伸缩，合理使用资源，每次运行任务时，会自动创建一个 Jenkins Slave，任务完成后，Slave 自动注销并删除容器，资源自动释放，而且 KubeSphere 会根据每个资源的使用情况，动态分配 Slave 到空闲的节点上创建，降低出现因某节点资源利用率高，还排队等待在该节点的情况。
- 扩展性好，当 KubeSphere 集群的资源严重不足而导致任务排队等待时，可以很容易的添加一个 KubeSphere Node 到集群中，从而实现扩展。
- 高可用，当 Jenkins Master 出现故障时，KubeSphere 会自动创建一个新的 Jenkins Master 容器，并且将 Volume 分配给新创建的容器，保证数据不丢失，从而达到集群服务高可用。

