## 整体说明

监测平台通过Prometheus+Grafana搭建

Prometheus(普罗米修斯)主要用于指标的提取，Grafana用于展示界面

主节点需额外安装Prometheus与Grafana，被监测节点需安装Node_Exporter，如需监测主节点则需要在主节点上也安装Node_Exporter。(虽然可以直接使用Prometheus的默认端口监控主节点，但为了配置一致性，建议在所有受控节点上都安装Node_Exporter)

注：centos7等使用systemctl控制服务的说明文档请访问[链接](https://github.com/HalfCoke/prometheusAndGrafana/tree/v1.0)

## 配置相关
### 主节点配置
#### 安装前配置
1. Prometheus配置

    安装前请先配置Prometheus文件夹下的 *prometheus.yml*文件。
    
    a) *prometheus.yml* 
    
    以下简单说明配置文件内容，详细内容请参考[官方文档](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)
    
    该配置文件主要用于配置监测的探测时间，及监测节点列表

    ```yaml
    global:
        # 全局探测时间
        scrape_interval: 15s

    scrape_configs:
    # 监测节点
    - job_name: 'prometheus'
        # 这个选项可以覆盖全局探测时间
        scrape_interval: 5s
        static_configs:
        # 可以在这个列表中增加需要监控的机器的地址及端口。需要考虑防火墙配置，是否更改端口。
          - targets: ['localhost:9090']
    ```

    b) *prometheus*
    
    该文件主要需要配置**web.listen-address**选项，更改端口号可以改变Prometheus默认的web ui及监控系统默认的端口号

    ```shell
    --web.listen-address=localhost:9090
    ```

2. Grafana配置

    修改Grafana配置请在安装完后修改
#### 安装后修改配置
1. 修改Prometheus配置
    a) 更改Prometheus配置文件及添加受控节点
    
    需要更改路径`/etc/prometheus/prometheus.yml`的配置文件。
    更改完成后需要执行`sudo service prometheus restart`
    b) 更改Prometheus监控系统默认端口及web ui端口

    需要更改路径`/etc/init.d/prometheus`文件。
    
    更改完成后请执行`sudo service prometheus restart`


2. 修改Grafana配置

    修改grafana配置请修改路径`/etc/grafana/conf/custom.ini`文件，详细内容请参考[官方文档](https://grafana.com/docs/grafana/latest/administration/configuration/)
    
    以下简单说明如何更改Grafana的web端口

    修改http_port后的端口号，如果前面有";"，需要删掉。
    ```ini
    [server]
    # Protocol (http, https, h2, socket)
    ;protocol = http

    # The ip address to bind to, empty will bind to all interfaces
    ;http_addr =

    # The http port  to use
    http_port = 3010
    ```
    更改完成后需要执行`sudo service grafana restart`

### 受控节点配置
#### 安装前配置
##### Node_Exporter配置
主要是修改Node_Exporter汇报的端口配置，需要修改文件夹Node_Exporter文件夹下的*node_exporter*文件。

修改其中的**web.liste-address**选项后的端口号。
```
options="--web.listen-address=:9100"
```

#### 安装后修改配置

需要更改路径`/etc/init.d/node_exporter`文件。
    
更改完成后请依次执行`sudo service node_exporter restart`


## 安装
### 主控节点安装

主控节点安装时，需要以管理员权限执行`Grafana/setupGrafana`及`Prometheus/setupPrometheus`
    
### 受控节点安装

受控节点安装时，需要以管理员权限执行`Node_Exporter/setupNodeExporter`



安装完成后便可以按照Grafana配置的端口进行访问