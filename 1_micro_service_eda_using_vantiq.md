# 使用Vantiq实现事件驱动的微服务架构

Vantiq是一个构建实时企业应用的平台，它使用事件驱动和流式处理，实现企业内设备、系统和人之间的连接。而Vantiq平台其中一个应用场景，就是作为事件驱动平台，为企业内的各种系统实现低耦合、可扩展、可管理的链接。

## 微服务架构与事件驱动架构
微服务架构就是将现有的大的系统，拆分成一个个的小的服务，并使用分布式的部署等，实现可扩展、高可用的一种分布式系统架构。微服务架构的初衷，是通过服务的拆分，不同服务之家的低耦合，来实现可扩展、易维护等目的。但是实际上，一个复杂的微服务系统中，服务之间的相互通信势必会越来越多，如果使用直接通信，会使得整个系统变得越来越复杂，服务之间的低耦合性也逐渐被打破。特别是当一个业务请求需要多个服务共同完成的时候，如果是服务之间直接调用，还会有一致性的问题，这个时候如果想实现分布式事务，实现最终的数据一致性，就会更加复杂。

事件驱动架构，实际上就是在微服务架构的基础上，通过一个中间服务来进行服务间的通信。举例来说就是，当一个服务要调用另一个服务时，不是直接调用，而是发送一个事件到一个消息中间件，由另一个服务订阅这个事件来触发相应流程。

事件驱动架构带来的最大好处就是实现了服务之间的高度松耦合，从而对系统的扩展和长期维护带来很大的好处。同时，它的缺点就是，开发和测试上的不便利。

开发上的不便利就是，开发人员以前都是直接调用的同步调用方式，现在要变成事件触发的异步和回调方式，这是一种开放思维上的转变。不过，这些年响应式编程提到的也越来越多，不管的前端开发还是服务器端，都越来越多的使用响应式的编程，前端框架如Angular、React和Vuejs都是使用响应式编程模式，还有Spring Boot 2.0以后，也开始大量使用Reactive方式来实现。

再说到测试，实际上，测试是更方便的，只要每个服务的每个service方法，都能够正确的执行他们自己的逻辑，然后，当多个服务的多个方法通过事件串在一起的时候，也应该是正确的。而我们的一个个的服务，如果真能够做到低耦合，那么写测试用例就会更加容易，因为它不需要依赖外部的服务和环境。那么这个时候，这个事件就是我们的服务之间通信的‘接口’，我们就需要对这个事件的数据结构和规范做好定义。

所以，使用事件驱动的微服务架构，不但能带来高度低耦合、可扩展、可维护的遍历，也能通过合理的设计和规范，来消除开发和测试的不便利。

## 事件驱动架构的两种模式
### Broker模式
Broker模式就是使用一个消息中间件，每个服务将自己的消息发送到某一个队列，其他的服务，根据自己的需要，自己去订阅相应的队列，来实现业务逻辑。

![EDA-broker-topology](micro_service_eda_using_vantiq/EDA-broker-topology.png?raw=true "Broker模式")

这种方式的缺点就是，每个服务都需要知道自己需要的事件在哪里。例如一个微服务系统，有一个订单服务、库存服务和用户服务，当订单服务产生一个订单以后，将该事件发送到新订单的队列，库存服务要订阅这个事件，进行库存的修改等，用户服务也要订阅，进行用户订单等一些处理。如果整个系统中有多个事件会对库存有更新的话，库存服务就需要订阅所有这些队列上的事件。


### Mediator模式
Mediator模式就是增加了一个Mediator（协调者）的角色，他可能是一个服务，也可能是某种具有流程处理功能的消息中间件。它的作用就是当有一个事件的时候，根据业务流程，产生一个个的事件，来进行业务流程的编排。例如在上面订单、库存和用户服务的例子中，当有一个订单的时候，这个协调者（Mediator）就将该事件发送到库存修改的队列里，以及用户的相应队列里。

![EDA-mediator-topology](micro_service_eda_using_vantiq/EDA-mediator-topology.png?raw=true "Broker模式")


这种模式的好处就是，每个服务，每个方法，只需要订阅一个自己的队列，根据里面事件的数据，来判断该事件的来源。这样就能进一步降低服务和事件直接的耦合性。这时候，这个事件和服务之间的耦合性，就放到了这个协调者（Mediator）上。但是，由于所有的耦合关系是在统一的一个地方保存，而不是分散在整个系统的多有的服务上，这在可维护性上还是有好处的。


## Vantiq事件驱动架构平台（未完）
Vantiq事件驱动架构平台介绍，Pronto(Dynamic Advanced Event Broker)介绍，功能，优点。

### 使用Vantiq实现Broker模式的事件驱动架构（未完）
介绍实现方式，缺点。


### 使用Vantiq实现Mediator模式的事件驱动架构（未完）
使用Vantiq作为Mediator协调器，来编排、调度、协调事件。有两种方式：
1. 使用流程编排，来实现当一个事件到达时，在Vantiq中编排流程，如先调用一个服务，根据结果再触发另一个事件等流程。
2. 使用Pronto的事件管理。通过Pronto可以实现，当一个队列上有个事件产生时，可以将它分发到其他某些队列。例如，在上面订单、库存和用户服务的例子中，当有一个订单的时候，这个事件被分发到库存和用户的自己的队列，这样库存和用户服务只需要订阅自己的队列。（有点类似AMQP协议，一个Exchange，根据一定的binding规则，将接收到的消息分发到多个Queue当中。）

