---
title: "Janus 接入指南 - 云文档"
source: "https://docs.corp.kuaishou.com/k/home/VD6SmooptFSM/fcADI2FlLVXS6nNWaigVJFJIY"
author:
published:
created: 2026-08-26
description:
tags:
  - "clippings"
---
目录

赵春林

1108

3

6

预计阅读 22 分钟

本文面向在万擎沙箱或容器云运行智能体（AI Agent）的接入方，说明如何接入 Janus、完成用户身份绑定，并让业务请求在授权范围内自动注入

OBO Token。

重要：接入 Janus 不只是注入 sidecar。无论使用万擎还是容器云，都必须同时完成：

●业务容器信任快手内部根证书

●用户与智能体绑定全生命周期

●OBO Token 默认内网权限与目标服务登记：仅.internal 和.corp.kuaishou.com，公网访问需单独完成安全评估和审批

1\. 接入流程总览

1.确认运行平台：万擎沙箱或容器云。

2.把快手内部根证书安装到业务镜像的系统 CA 信任列表，并覆盖所用语言运行时的证书读取方式。

3.实现用户与智能体的注册绑定、定期续约和释放解绑。

4.按默认基线接入：仅开放.internal 和.corp.kuaishou.com，并由 Janus 为命中请求注入 OBO Token；如需公网访问，必须先单独完成安全评

估和审批。

5.按平台方式启用 Janus。

6.验证 Janus 状态、TLS 信任、Token 注入和智能体释放流程。

2\. 必须完成的前置工作

2.1 信任快手内部根证书

Janus 对受控 HTTPS 流量进行处理时，业务进程必须信任快手内部根证书。证书为 PEM 格式，应在制作业务镜像时安装；不要在运行时关闭

TLS 校验，也不要信任某一张临时叶子证书。

以下文件 kuaishou-internal-root-ca.pem 是正式根证书文件。

kuaishou-internal-root-ca.pem

1.47K

<table><colgroup><col> <col></colgroup><tbody><tr><td rowspan="1" colspan="1"><p>Linux 发行版</p></td><td rowspan="1" colspan="1"><p>安装方式</p></td></tr><tr><td rowspan="1" colspan="1"><p>Debian / Ubuntu</p></td><td rowspan="1" colspan="1"><div><a>Plain Text</a><p>自动换行折叠</p></div><div><pre><code>xxxxxxxxxx</code></pre></div><div><pre><code>install -m 0644 kuaishou-internal-root-ca.pem /usr/local/share/ca-certificates/kuaishou-internal-root-ca.crt</code></pre></div><div><pre><code>update-ca-certificates</code></pre></div></td></tr><tr><td rowspan="1" colspan="1"><p>RHEL / CentOS / Rocky / AlmaLinux</p></td><td rowspan="1" colspan="1"><div><a>Plain Text</a><p>自动换行折叠</p></div><div><pre><code>xxxxxxxxxx</code></pre></div><div><pre><code>install -m 0644 kuaishou-internal-root-ca.pem /etc/pki/ca-trust/source/anchors/kuaishou-internal-root-ca.pem</code></pre></div><div><pre><code>update-ca-trust extract</code></pre></div></td></tr><tr><td rowspan="1" colspan="1"><p>Alpine</p></td><td rowspan="1" colspan="1"><div><a>Plain Text</a><p>自动换行折叠</p></div><div><pre><code>xxxxxxxxxx</code></pre></div><div><pre><code>apk add --no-cache ca-certificates</code></pre></div><div><pre><code>install -m 0644 kuaishou-internal-root-ca.pem /usr/local/share/ca-certificates/kuaishou-internal-root-ca.crt</code></pre></div><div><pre><code>update-ca-certificates</code></pre></div></td></tr></tbody></table>

<table><colgroup><col> <col></colgroup><tbody><tr><td rowspan="1" colspan="1"><p>运行时</p></td><td rowspan="1" colspan="1"><p>接入要求</p></td></tr></tbody></table>

页眉

页脚

AI 摘要

解释

- #### Janus 智能体身份（OBO-Token）服务端接入SOP文档
- #### 【AI-网关】换票接口文档
- #### 容器云接入 Janus 说明

1

1

1

![](https://static.yximgs.com/bs2/kimAvatar/c55ad497b3a64ee395666842205a783e)

金永浩

8月19日 21:19

@赵春林 这一步是要安装到哪里

添加评论...

![](https://static.yximgs.com/bs2/kimAvatar/ef1a362a256c42f6b4639cd442803dde)

柴佳能

8月10日 18:08

这一步是必须的吗？ 我的服务目前要调用data agent的api，后端服务是 serverless部署的，资源选项中看不到 ai-agent

![](https://static.yximgs.com/bs2/kimAvatar/ef1a362a256c42f6b4639cd442803dde)

柴佳能

8月13日 11:38

必须是容器云部署，得找容器云oncall

添加评论...

![](https://static.yximgs.com/bs2/kimAvatar/5f45e7077f424293af9f34d4d6f458d0)

张弘

8月11日 15:20

1\. 业务资源负责人/SRE 可以从其他资源池向 ai-agent 资源池转移配额。

2\. 环境选择 ai-agent 资源池，进行部署。

3\. Serverless 不支持选择资源池，因此需要切换回容器部署。

添加评论...

3

<iframe frameborder="0" src="about:blank"></iframe><iframe src="about:blank"></iframe><iframe frameborder="0" src="about:blank"></iframe>

划线收藏

展开知识库目录

![](chrome-extension://edjnkhdbibdhkeakapmklklbfealkneh/assets/dispose.svg) ![](chrome-extension://edjnkhdbibdhkeakapmklklbfealkneh/assets/success.svg) 收藏成功 ![](https://h2.static.yximgs.com/udata/pkg/IS-DOCS/wiki/venders/img/shortcutIcon/doc-v3.png) Janus 接入指南 - 云文档 ![](chrome-extension://edjnkhdbibdhkeakapmklklbfealkneh/assets/arrow.svg)

添加标签

多个标签回车间隔

储存空间 ![](chrome-extension://edjnkhdbibdhkeakapmklklbfealkneh/assets/setting.svg) ![](https://s2-11442.kwimgs.com/kos/nlav11828/avatar/space4.png) 魏奥迪的空间