
# AnyQ_智能问答的一个非官方帮助手册
本手册由一个俗人(wechat:dotzcv)整理，数据来源为百度Google搜索，官方issues，以及本人的实验所得进行记录扩充

## 前提的准备条件

硬件环境：4核8G 上面测试成功 (我的一台2核4G测试服务器，目前未通过。)</br>
软件环境：
* Docker 推荐
* cmake 3.2.2版本 ，g++ >=4.8.2，bison >=3.0
* java环境 安装操作见下面QA章节 
 
网络环境：正确的上网方式。由于编译时候会在github上进行代码clone，所以建议有条件的使用正确的方式，加速下载代码（当然此处非必须，没有则速度慢一点而已）

## 核心代码安装步骤
1.非Docker：忽略了，没有高一点而且环境比较纯碎的服务器了，没有编译成功。</br>
2.Docker：安装成功了。步骤如下：
  
``` 
#拉取镜像
docker pull hub.baidubce.com/paddlepaddle/paddle:latest-dev

#运行容器
docker run --name paddle -it -v $PWD:/paddle hub.baidubce.com/paddlepaddle/paddle:latest-dev  /bin/bash

#进入容器进行代码clone
git clone https://github.com/baidu/AnyQ.git

#进去AnyQ目录执行

mkdir build && cd build && cmake .. && make
#漫长的等待过程（中间会出现各种事情 见下面QA章节）

核心代码编译完成
```

## 实例Demo安装步骤
在当前build目录下，执行以下操作

构建索引、配置
```
# 获取anyq定制solr，anyq示例配置
cp ../tools/anyq_deps.sh .
sh anyq_deps.sh

# 启动solr, 依赖python-json, jdk>=1.8
cp ../tools/solr -rp solr_script
sh solr_script/anyq_solr.sh solr_script/sample_docs

```

## 启动服务
```
./run_server

# 请求示例：
http://127.0.0.1:8999/anyq?question=NLP是什么

测试请求返回：
curl 0.0.0.0:8999/anyq?question=NLP是什么

[{"answer":"\u60a8\u9700\u8981\u62e5\u6709\u4e00\u4e2a\u767e\u5ea6\u8d26\u53f7\uff0c\u7528\u6765\u767b\u5f55\u767e\u5ea6\u4e91\uff0c\u53ef\u4ee5\u70b9\u51fb\u6b64\u5904\u6ce8\u518c\u767e\u5ea6\u8d26\u6237\u3002\u5982\u60a8\u4ee5\u524d\u62e5\u6709\u767e\u5ea6\u63a8\u5e7f\u8d26\u6237\uff0c\u540c\u6837\u53ef\u4ee5\u767b\u5f55\u767e\u5ea6\u4e91\u3002","confidence":0.51880854368209839,"json_info":"{}\n","qa_id":"1","question":"\u9700\u8981\u4f7f\u7528\u4ec0\u4e48\u8d26\u53f7\u767b\u5f55?"}]

```




# QA 
1. 遇到
```

[ 50%] Built target lod_rank_table_op
[ 50%] Built target pool_op
[ 50%] Built target arg_max_op
[ 50%] Built target lookup_table_op
[ 50%] Built target print_op
[ 50%] Built target bilinear_interp_op
[ 50%] Built target load_op
[ 50%] Built target one_hot_op
Makefile:105: recipe for target 'all' failed
make[3]: *** [all] Error 2
CMakeFiles/extern_paddle.dir/build.make:111: recipe for target 'third_party/paddle/src/extern_paddle-stamp/extern_paddle-build' failed
make[2]: *** [third_party/paddle/src/extern_paddle-stamp/extern_paddle-build] Error 2
CMakeFiles/Makefile2:511: recipe for target 'CMakeFiles/extern_paddle.dir/all' failed
make[1]: *** [CMakeFiles/extern_paddle.dir/all] Error 2
Makefile:83: recipe for target 'all' failed
make: *** [all] Error 2


```
回答：

试下把源码里面paddle改成单线程编译：

#cmake/external/paddle.cmake: line 14
make -j16 -> make -j1
