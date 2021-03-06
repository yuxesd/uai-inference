

# Hot-Standby机制

## 什么是Hot-Standby
Hot-Standby是UAI在线服务在算力独占模式下，为减少独占资源在用户无访问时的资源浪费，用降配资源支持服务待机的一种模式。
开启Hot-Standby机制后，当AI在线服务在30分钟内无任何请求时，系统会自动将AI在线服务迁移至降配资源池，同时大幅减少费用支出。同时，一旦有用户请求，就会立刻恢复独占节点。
Hot-Standby涉及的产品定价详见[](uai-inference/price)

## Hot-Standby适用于什么场景
1. 服务本身对单节点算力要求高，必须由独占算力（GPU节点）
2. 服务存在明显的闲置期（无用户访问），但又需要随时待命

## 如何启动Hot-Standby
### 1. 服务类型为算力独占模式
由于算力共享模式的服务，本身已经精确按量计费，在用户无请求时是不收取任何费用的，因此无需Hot-Standby模式。

![hot-standby01](ai/uai-inference/images/intro/hot-standby01.png)

### 2. 服务启动自动伸缩容规则

Hot-Standby是自动伸缩规则的一个新特性，它调整算力到降配节点、恢复算力到独占节点的功能都需要自动伸缩引擎来统一执行。
![hot-standby02](ai/uai-inference/images/intro/hot-standby02.png)

### 3. Hot-Standby扩缩容的触发条件

Hot-Standby主要监控QPS，当QPS降到0，并且维持30分钟后，自动将**当前所有节点**逐渐迁移到降配节点上。

自动扩缩容也主要监控QPS，当QPS值在30秒内稳定到达扩容阈值以上、缩容阈值以下，自动将对节点进行扩容、缩容操作。

由以上两点，我们可以归纳得出：

* Hot-Standby的触发条件是独立的（QPS=0），与扩容阈值、缩容阈值无关

* 最小节点数量，就是Hot-Standby降配的节点数量
![hot-standby03](ai/uai-inference/images/intro/hot-standby03.png)

### 4. 最大节点、最小节点、扩容阈值、缩容阈值推荐配置值

Q=以并发数为1，测试单节点最大支持的qps

N=缩容最小节点数

扩容QPS应配置=略小于Q

缩容QPS应配置=略小于(N*Q)/(N+1)

**例如：**某用户的服务单节点稳定响应时间是500ms，那么Q=2。用户期望最大节点数量为2，最小节点数量为1。

扩容QPS应配置=略小于2=1.9

缩容QPS应配置=略小于1* 2/( 1 + 1) =略小于1=0.9

## Q&A

### 1. 降配节点还是GPU节点么？算力会差多少？

答：降配节点仍然是GPU节点，算力与独占节点相同。但不保证节点的独占性。由于共同使用降配节点的服务均处于闲置期，所以互相性能影响程度不大。

### 2. 启用Hot-Standby功能后，节点的切换会不会对客户请求有明显的影响？

答：无论是降配还是恢复独占算力，系统均会保证服务可用性，用户不会有服务在切换配置有感受。但对于部分AI在线服务，由于代码原因初次用户请求用时较久，会在服务配置切换时被用户感受到。建议代码编写上能够在启动时包含预热操作。

### 3. Hot-Standby期间只会有一个节点么？突发访问量很大怎么办？

答：Hot-Standby的节点数是伸缩规则中的“最小节点数”，所以如果突发访问量很大，担心单个节点不能承受并发压力，建议适当调整“最小节点数”。

### 4. 服务用量很小，平时1个节点就够了，但是最大节点数至少是2，怎么办？

答：可以将扩容阈值设置得比较大，这样就不会触发扩容了。


