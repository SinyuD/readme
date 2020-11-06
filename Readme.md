
## 功能概述  
####  managerctl   
* 用以部署 istio 环境 以及 istio-manager    
#### istio-manager      
* 增删改查服务  
 * 增删改查服务路由  
 * 设定服务tcp最大连接数  
 * 服务流量权重比控制  
## 编译 需要 GO 1.14以上版本 
使用Makefile进行编译. 编译命令如下 
* install :编译istio-manager主程序到dist目录下。
* deploy: 编译managerctl部署程序到dist目录下。 
* image： 编译主程序并且构建tag为istio-manager:v1的镜像。 
* install_all: 编译主程序和部署程序。 
* clean: 清理dist编译的程序和镜像。    
编译命令举例 ``make install_all``     
 ## 部署
 ### 部署顺序： 
 ` Jager-operator --> Jaeger --> Pormetheus ---> istio-operator ---> istio ` 
 * 确保istio在Jaeger和Prometheus部署成功之后部署...Istio在Jaeger前部署的话，会出现部分调用链不能收集的情况，原因未明.    
 *  卸载顺序则相反 ### 部署方法：  
#### 使用managerctl部署 
* 部署istio环境    
 `managerctl install istio` 
 * 部署istio-manager       
  `managerctl install manager`
####  使用yaml部署   
略. 部署所用yaml配置可参考 istio-manager/yaml/ 目录下各部分yaml配置.      
    
###  managerctl 参数说明 
managerctl 可以直接指定参数进行部署istio/istio-manager,也可以指定读取ini配置文件进行部署 
* `managerctl install istio -h`    
 --kubeconfig  k8s的kubeconfig文件配置路径。默认：$HOME/.kube/config。如有移动需要指定.        
 --config   ini配置文件路径。        
 --istio_image 部署istiod的镜像
 * `managerctl install manager -h`        
 --config   ini配置文件路径。      
### ini文件配置说明 
ini配置文件参考 istio-manager/dist目录下 istio.ini
    
``` 
[istio] 部署istio的配置 
Sampling = 50               Tracing 采样率 
IstioImage = docker.io/istio/pilot:1.6.9  Istiod镜像 
SidecarImage = docker.io/istio/proxyv2:1.6.9   proxy镜像 
OperatorImage = docker.io/istio/operator:1.6.9   istio-operator镜像    
ReloadCmImage = jimmidyson/configmap-reload:v0.3.0   prometheus 重载cm镜像 
PromeImage = prom/prometheus:v2.19.0      prometheus 主程序镜像    
JaegerImage = jaegertracing/all-in-one:1.18.1      jaeger镜像 
JaegerOperator = jaegertracing/jaeger-operator:1.18.1  jaeger-operator镜像   

[manager] 部署istio-manager的配置 
ManagerImage = istio-manager:v1        istio-manager镜像 
LogDir = 主机挂载日志路径 
LogLevel = 5 日志级别 
PromeAddr = http://prometheus.istio-system.svc:9090   prometheus的地址，用来查询和推送熔断记录 
NsSrm = default   // `
