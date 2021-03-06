

## 弹性伸缩规则设置
对于“弹性服务”，其自动进行弹性伸缩，无需设置。 
对于“独占服务”，需要用户设定伸缩规则，服务将依据伸缩规则动态调整节点数量。 
具体说明请参见[扩缩容机制](uai-inference/intro/structure/auto)

## 使用Console设定伸缩规则
### 设定独占服务伸缩规则

1.  在服务的“伸缩管理”标签页中，点击“添加规则”按钮新建伸缩规则。  
![](ai/uai-inference/images/use/scale-0.jpg)
2.  设置伸缩规则。  
![](ai/uai-inference/images/use/scale-create.png)  
**参数说明**  
**指标**： 弹性伸缩依据的指标，目前支持平均QPS（总QPS/节点数）  
**最大节点数**：服务扩容的节点上限。  
**最小节点数**：服务缩容的节点下限。  
**扩容阈值**： 当平均QPS大于该值时，并持续一段时间，开始扩容。  
**缩容阈值**： 当平均QPS小于该值时，并持续一段时间，开始缩容。  
3. 启用伸缩规则。  
![](ai/uai-inference/images/use/scale-start.png)