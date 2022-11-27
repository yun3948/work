#网络创建
`
docker network create itmentu-net
`

#ElasticSearch安装

docker run -d \
	--name elasticsearch \
    -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
    -e "discovery.type=single-node" \
    -v es-data:/usr/share/elasticsearch/data \
    -v es-plugins:/usr/share/elasticsearch/plugins \
    --privileged \
    --network itmentu-net \
    -p 9200:9200 \
    -p 9300:9300 \
elasticsearch:7.12.1

命令说明：

-e "cluster.name=es-docker-cluster"：设置集群名称
-e "http.host=0.0.0.0"：监听的地址，可以外网访问
-e "ES_JAVA_OPTS=-Xms512m -Xmx512m"：内存大小
-e "discovery.type=single-node"：非集群模式
-v es-data:/usr/share/elasticsearch/data：挂载逻辑卷，绑定elasticsearch的数据目录
-v es-logs:/usr/share/elasticsearch/logs：挂载逻辑卷，绑定elasticsearch的日志目录
-v es-plugins:/usr/share/elasticsearch/plugins：挂载逻辑卷，绑定elasticsearch的插件目录
--privileged：授予逻辑卷访问权
--network itmentu-net ：加入一个名为itmentu-net的网络中
-p 9200:9200：端口映射配置



安装完成后，在浏览器中输入：http://192.168.211.130:9200/ 即可看到elasticsearch的响应结果：


3 安装Kibana

3.2 Kibana安装
使用Docker安装Kibana非常简单，只需要执行如下命令即可，但是执行命令需要注意Kibana操作的ElasticSearch地址，因为Kibana是需要连接ElasticSearch进行操作的，命令如下:
docker run -d \
--name kibana \
-e ELASTICSEARCH_HOSTS=http://192.168.211.130:9200 \
--network itmentu-net \
-p 5601:5601  \
kibana:7.12.1

命令说明：

--network itmentu-net ：加入一个名为itmentu-net的网络中，与elasticsearch在同一个网络中
-e ELASTICSEARCH_HOSTS=http://192.168.211.130:9200"：设置elasticsearch的地址，因为kibana已经与elasticsearch在一个网络，因此可以用容器名直接访问elasticsearch，也可以写IP地址实现访问。
-p 5601:5601：端口映射配置

 
 日志中如果出现了http://0.0.0.0:5601即可访问Kibana后台服务，日志如下：
 3.3 Kibana中文配置
我们发现Kibana是英文面板，看起来不是很方便，但Kibana是支持中文配置，所以我们可以把Kibana配置成中文版，便于我们操作。
切换中文操作如下:
#进入容器
docker exec -it kibana /bin/bash

#进入配置文件目录
cd /usr/share/kibana/config

#编辑文件kibana.yml
vi kibana.yml

#在最后一行添加如下配置
i18n.locale: zh-CN

#保存退出
exit

#并重启容器
docker restart kibana

4.2 IK分词器配置

我们只需要将上面elasticsearch-analysis-ik-7.12.1拷贝到ElasticSearch的plugins目录中即可，但由于当前服务采用的是docker安装，所以需要将文件拷贝到docker容器的plugins目录才行。
操作如下：
#为了方便配置，我们将elasticsearch-analysis-ik-7.12.1改成ik文件夹
mv elasticsearch-analysis-ik-7.12.1 ik

#将ik文件夹拷贝到elasticsearch容器中
docker cp ik elasticsearch:/usr/share/elasticsearch/plugins

#重启容器
docker restart elasticsearch

作者：bysecby
链接：https://juejin.cn/post/7074115690340286472
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
