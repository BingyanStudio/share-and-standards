# Android客户端框架文档

恩，这是一个文档

要求: 

1. 良好的可扩展性，能方便添加删除模块以实现产品的功能需求
2. 规范性，所有操作统一化，避免临时性代码
3. 可维护性，配置统一管理

程序包名位net.bingyan.client

net.bingyan.client
----core
----module
----util
----dao
----network
----ui

module是用于不断扩展功能，稍后再说，其余几个包主要是公共基础包

- util 
负责时间格式化，Log格式化，获取缓存目录，图片信息，系统控件信息等
- dao
封装数据库相关的一切操作
- network
 协议封装，网络请求封装
- ui 
各种自定义ui的实现

接下来是module

就是放各个业务模块的啦

每个module下一般分为：

- activity         控制交互流程
- fragment      实现界面上细节的交互
- adapter        负责数据与界面的映射关系
- model           描述各种类的详细数据类型，以及可能与数据库的关联
