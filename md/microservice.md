- <p class="fragment fade-up">微服务概述</p>
- <p class="fragment fade-up">如何构建微服务</p>
- <p class="fragment fade-up">进程间通信</p>
- <p class="fragment fade-up">服务发现</p>
- <p class="fragment fade-up">数据管理</p>
- <p class="fragment fade-up">重构单体应用</p>
- <p class="fragment fade-up">群脉在微服务上的实践</p>



微服务概述


单体应用结构

<img src="/img/single-application.png" width="500px" height="500px"/>


当你的应用变的越来越庞大，庞大到任何一个开发者都无法完全理解，这时候，修复 bug 和实施新功能也就极其困难


即便你顶着困难和沮丧修复好了 bug，来到了部署，却发现虽然只更新了单个部分的代码，但整个应用都需要重新部署，又是一个漫长的过程...


即便你使用的是轻量的虚拟机技术(docker)，构建镜像的过程也是同样的耗费时间


另外当你的模块在系统资源方面有所冲突，例如一些模块是 cpu 密集型，一些需要大内存，一些属于磁盘 IO 密集型，鉴于共同部署，这时你只能在硬件选择中做妥协


最后，单体应用会让选用新技术新语言变的及其困难。陈旧低效的技术几乎只会让开发人员感到沮丧...


这就是微服务出现的原因


每个微服务完成特定的功能，比如订单管理，客户管理，并且拥有自己的独立数据库。专门的团队开发和独立部署，直击单体应用的痛点



如何构建微服务


<pre>以某东商城的手机 APP 为例，在产品详情页面，需要包含商品信息，配送选项，推荐，以及客户评论等 N 个数据</pre>


<img src="/img/product-detail-1.jpg" width="200px" height="500px"/>
<img src="/img/product-detail-2.jpg" width="200px" height="500px"/>


　　在传统应用架构下，手机客户端需要向服务端应用程序发起一次请求来获取数据，服务端负载均衡将此次请求路由给可用的服务端程序实例之一，随后，实例接收请求访问各种数据库，整合数据并返回


<img src="/img/product-detail-single-mode.png" width="500px" height="500px"/>


微服务架构下，产品详情页的数据可能来自不同的服务上，此时客户端该如何获取数据?


两种方法


  与微服务直接通信，每个微服务暴露一个公开的接口，类似 https://*.api.company.com，
  为了获取产品详情，客户端需要逐一向这些接口发起请求


<img src="/img/product-detail-microservice-direct-mode.png" width="500px" height="500px"/>


这样做会引起的一些问题

- <p class="fragment fade-up">在某些复杂的业务场景下，客户端可能会同时发起成百上千个请求，这在网络传输上十分低效</p>
- <p class="fragment fade-up">微服务使用的协议(RPC，AMQP)可能浏览器(http/https,ftp,file...)并不支持</p>
- <p class="fragment fade-up">当你想合并或者拆分一些服务进行重构时，要考虑向后兼容或者需要直接重构客户端代码，这样代价过重</p>


使用 API 网关

- <p class="fragment fade-up">API 网关是进入系统的唯一节点，它通过调用多个微服务合并结果来处理一个请求</p>
- <p class="fragment fade-up">同时它负责 web 协议与内部使用的协议之间的转换</p>
- <p class="fragment fade-up">跟单体应用一样，API 网关也会暴露一个接口，供客户端调用</p>


<img src="/img/product-detail-microservice-api-gateway-mode.png" width="500px" height="500px"/>


如何实现 API 网关?


- 性能
- 服务调用
- 服务发现
- 处理局部失败


为了性能和可扩展性，API 网关必须构建在支持异步、I/O非阻塞的平台上。
比较流行的选项是 Node.js 或者 NGINX Plus。NGINX Plus 提供一个高性能可扩展的 web 服务器和可配置可编程的反向代理。


另外，微服务作为一个分布式系统，必须使用进程间通信(IPC)机制，不像单体应用，各个模块的互相调用是通过语言级别的方法或函数实现的


API 网关也需要知道与之通信的每个微服务的位置，与单体应用不同，微服务地址总是动态分配的


最后，在所有的分布式系统中，当一个服务调用另一个服务，后者不可用或者响应慢时，就会有所谓的局部失败的问题


在商品详情的场景下，如果推荐服务不可用，此时可以返回一个固定的 TOP 10 列表替代，也可以返回缓存的数据



进程间通信


通常来讲就是微服务之间的通信，每个微服务都是独立的进程


基于微服务的分布式应用运行于多台机器上，通常有以下两种 [IPC](https://zh.wikipedia.org/wiki/%E8%A1%8C%E7%A8%8B%E9%96%93%E9%80%9A%E8%A8%8A) 机制

- <p class="fragment fade-up">异步，基于消息传递的机制</p>
- <p class="fragment fade-up">诸如 HTTP 这样的同步机制</p>


基于消息的异步通信


消息通过渠道发送，生产者发送消息，消费者从渠道中接受数据。
频道有两种，点对点和发布/订阅渠道。

<img src="/img/product-detail-microservice-async-mode.png" width="500px" height="500px"/>


消息系统可以选择： [AMQP](https://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol)，RabbitMQ


优点:

- 解耦客户端和服务端。客户端只需要将消息发送到正确的渠道，不需要了解服务实例，更不需要服务发现机制来确定服务实例的位置
- 消息缓冲。消息中间人只需要对消息队列进行管理，直到被消费者处理。即便消费者不可用，请求仍能进入队列。


缺点:

- 消息系统需要单独安装，配置和部署。必须高可用，否则系统的可靠性会受到影响
- 实现请求/响应交互模式会比较复杂，直接使用 HTTP 的 IPC 机制更容易


基于请求/响应的的同步 IPC


客户端发送请求，服务端需要立即响应，常见的协议是 REST，RPC


<img src="/img/product-detail-microservice-sync-mode.png" width="500px" height="500px"/>


REST，基于 HTTP 协议，通过 URL 实现，核心是资源

- GET 返回资源
- POST 创建资源
- PUT 更新资源


优点:

- 简单
- 容易测试
- 支持请求/响应模式的通信
- 不需要中间代理


缺点:

- 只支持请求/响应模式(HTTP2 支持 server push，尚未普及不做考虑)
- 客户端必须知道每个服务实例的 URL，客户端必须使用服务实例发现机制


GRPC

REST 的代替品，实现了多语言 RPC(远程过程调用) 客户端和服务端调用，基于 HTTP/2，使用 Protobuffer 作为消息格式


消息格式的选择

- 文本格式 JSON 和 XML，优点是可读自描述
- 二进制格式 Protocl Buffers，传输效率更高



服务发现


为什么需要服务发现?


假设我们需要调用 REST API 或者 GRPC API，为了完成一次请求，需要知道服务实例的网络地址。传统应用中，服务实例的网络地址相对固定，只需要从一个偶尔更新的配置文件中读取即可。


然而对于微服务应用，服务实例的网络地址都是动态分配的，无法通过传统的 hard code 方式获取


<img src="/img/microservice-find.png" width="500px" height="500px"/>


服务发现的模式有两种，客户端发现和服务端发现


服务实例的网络位置在启动时被记录到服务注册表(后面会提到，可以理解为数据库)，终止时被删除，注册信息使用心跳机制来定期刷新。
客户端通过查询服务注册表，从中选择一个实例，发出请求。


客户端通过 SLB 向某个服务提出请求，SLB 查询服务注册表，并将请求转发到可用的服务实例。


服务端发现的一种做法是使用 Consul Template，以及 Nginx 来实现。Consul Template 定期从服务注册表中读取数据并生成 Nginx 配置文件，然后通过 HUP 信号通知 Nginx Master 进程重新加载配置文件

优点: 客户端无需关心服务发现的细节，只需要向 SLB 发送请求即可


服务注册表

服务发现的核心部分，包含服务实例网络地址的数据库。服务注册表需要高可用并且随时更新。


常见的服务注册表

- etcd  提供键值存储，用于共享配置和服务发现(k8s 的内置服务注册表)
- consul  提供 API 实现客户端注册和服务发现
- zookeeper  高性能协调服务


服务注册的方式

- 自注册 就是服务实例自己注册，必须在每个编程语言和框架内实现注册代码
- 第三方注册 由服务注册器通过查询部署环境或订阅事件的方式追踪服务实例的更改



数据管理


单体应用通常使用关系型数据库，因此应用能够使用 ACID 事务

- 原子性 原子粒度的更改
- 一致性 数据库的状态保持一致
- 隔离性 并发执行的事务显示为串行执行
- 持久性 事务一旦提交就不会被撤销

这样的特性可以让应用简单地开始事务、更改、提交事务


然而在微服务架构下，每个服务拥有的数据专门用于该服务，仅通过 API 访问。这种封装保证了微服务之间松耦合，可以独立更新。


另外，不同的微服务之间通常使用不同类型的数据库，可能是类似 Mongodb、Elasticsearch 这些 NoSql 数据库


挑战: 如何保持多种服务的数据一致性?


以在线 B2B 商店为例。客户服务维护信用信息，订单服务管理订单，验证新订单是否超出用户的信用额度


在单体应用中，可以使用事务来进行信用额度的核对进而创建订单


在微服务架构中，订单表和客户表为各自的服务私有，订单服务无法访问客户表，只能通过客户服务提供的 API。

<img src="/img/microservice-data-management-table.png" width="500px" height="500px"/>


如果订单和客户服务都使用关系型数据库，则可以通过 [2PC](https://stackoverflow.com/questions/24872902/real-difference-between-one-phase-and-two-phase-xa-commit) 的方式进行分布式事务。


但如果选择了 NoSql 数据库，可能根本不支持事务，更无法支持 2PC。


解决: 事件驱动的架构


事件驱动下的事务由一系列步骤组成，每一步都有一个微服务更新业务实体，然后触发下一步的事件。


＃ 订单服务创建状态为 NEW 的订单，发布“订单已创建”事件

<img src="/img/microservice-data-management-new-order.png" width="500px" height="500px"/>


＃ 客户服务获取“订单已创建”事件，为此订单保留信用

<img src="/img/microservice-data-management-publish.png" width="500px" height="500px"/>


＃ 订单服务获取“信用保留”事件，把订单状态改为 OPEN

<img src="/img/microservice-data-management-open.png" width="500px" height="500px"/>



重构单体应用


停止挖坑


应该停止让单体应用继续变大，也就是在实现新功能的时候，不应该再增加代码。相反，把这部分新代码开发称为独立的微服务

<img src="/img/microservice-restruct-one.png" width="500px" height="500px"/>


提取微服务


将单体应用内现有的模块转变为独立的微服务。每当提取模块并将其转化为服务，单体应用就会收缩。一旦转化了足够的模块，单体应用也将彻底消失，或者小到成为另一个微服务