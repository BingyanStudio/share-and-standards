#RESTful API 设计规范   
##综述   
###介绍   
Representational state transfer (REST),中文名为含状态传输,它是是Roy Fielding博士在2000年他的博士论文中提出来的一种软件架构风格,值得注意的是REST是一种风格而不是一种标准.   
这一份设计规范是针对于团队中设计API而写的指导性质的文档.   
>API应当尽量简洁   
   
###概念   
* **资源(Resource)** 一个对象,例如一个用户   
* **集合(Collection)** 一些对象的集合,例如所有的用户   
* **HTTP** HTTP协议   
* **使用者(Consumer)** 使用api的客户机,例如一个应用程序   
* **第三方开发者(Third Party Developer)** 不是团队内部的人但想要使用资源的开发者   
* **服务(Server)** 支持HTTP协议服务器   
* **端点(Endpoint)** 一个代表着资源或者集合的API URL   
* **URL片段(URL Segment)** URL中斜杠分割的信息,例如/zoo/1/animals/   
   
###请求方法   
* **GET** 获取一个资源或者一个集合的所有资源   
* **POST** 在服务器上新建资源   
* **PUT** 更新服务器上的资源(提供整个修改后的资源)   
* **PATCH** 更新服务器上的资源(只提供需要修改的部分)   
* **DELETE** 从服务器上删除一个资源   
* **HEAD** (鲜有人知)获取资源的元信息(结构)   
* **OPTIONS** (鲜有人知)获取使用者可以对资源进行的操作   
   
###感性认识   
通过感性认识来简单了解一下REST的风格是怎样的   
   
* 返回所有动物园信息的列表   
GET /zoos   
* 创建一个新的动物园   
POST /zoos   
* 获取ID为1的动物园信息   
GET /zoos/1   
* 更新ID为1的动物园的信息(替换所有的信息)   
PUT /zoos/1   
name=lalala&&animal_num=500   
* 更新ID为1的动物园的信息(更新部分信息)   
PATCH /zoos/1   
name=lalala&&animal_num=500   
* 删除ID为1的动物园ID为1的雇员   
DELETE /zoos/1/employees/1   
* 获取ID为1的动物园的所有动物信息的列表   
GET /zoos/1/animals   
       
通过这些http请求返回相应的信息或者是修改相应的信息   
   
文档中所有API请求都使用了简洁的表述形式   
完整的API请求头信息   
       
    POST /v1/animal HTTP/1.1   
    Host: api.example.org   
    Accept: application/json   
    Content-Type: application/json   
    Content-Length: 24   
    
    {   
        "name": "Gir",   
        "animal_type": 12   
    }   
   
完整的返回HTTP头信息   
   
    HTTP/1.1 200 OK   
    Date: Wed, 18 Dec 2013 06:08:22 GMT   
    Content-Type: application/json   
    Access-Control-Max-Age: 1728000   
    Cache-Control: no-cache   
    
    {   
        "id": 12,   
        "created": 1386363036,   
        "modified": 1386363036,   
        "name": "Gir",   
        "animal_type": 12   
    }   
   
##规范细节   
###API地址   
对于数量很大或者未来会发展的很大的API应当分配专门的API地址   
       
    http://api.exmaple.com/   
对于数量不是很大也不会发展的很大的API可以简单使用URL片段的形式   
       
    http://example.com/api/   
   
###端点   
端点标识着一些特定的资源或者是集合,例如   
* 动物园信息的端点   
api.example.com/**zoos**   
* 动物信息的端点   
api.example.com/**animals**   
* 动物类别信息的端点   
api.example.com/**animal_types**   
   
###返回数据   
**信息**   
在对单个对象进行请求的时候应当返回**整个对象**的信息,使用json,xml或者是其他的格式   
在对整个集合进行请求的时候应当返回**整个集合中所有的对象**,即使有的场合,集合会会非常庞大,返回它们会使得响应时间过长.因为这样做能够减轻开发者调试的时间.有时候返回了不完整的信息,开发者会思考到底是链接问题还是服务方返回的问题,在文档中进行说明将会减少开发者的顾虑,返回完整的信息也能提供给更多的灵活性   
更多有用的信息   
* **PUT/PATCH** 应当返回整个修改后的资源   
* **POST** 应当返回整个新建的资源   
* **DELETE** 返回空文档   
   
**返回信息的格式**   
对于不同的场合,使用API的人可能希望获取不同格式的信息,json,xml或者其他格式   
使用者希望返回的格式应当在http头的Accept中写明   
如   
Accept: application/json   
API应当广泛的支持不同的格式给开发者以不同的选择   
对应端点的返回或者提交信息的格式应当在文档中详细说明   
   
**状态码**   
服务器通过返回不同的状态码来通知用户之前操作的结果   
* **200** OK ***GET***方法   
服务器正确返回了资源的信息   
* **201** CREATED ***POST/PUT/PATCH***方法   
服务器正确修改或者创建了资源   
* **204** NO CONTENT ***DELETE***方法   
服务器正确删除了指定资源   
* **400** INVALID REQUEST    
用户提交了不正确的信息给服务器(服务器不做任何修改,只返回错误信息)   
* **404** NOT FOUND    
用户请求了不存在的资源或者集合   
* **500** INTERNAL SERVER ERROR   
服务器内部错误   
   
###选项   
API的设计中应当允许使用者对返回的数据进行过滤或者选择   
选项实现最好的形式是再端点的后面跟上'?'及不同的选项类似于普通的GET请求   
下面列举一些应当支持的选项   
* **限制个数limit**   
返回10个动物园的信息   
GET /api.exmaple.com/zoos?limit=10   
注意:没有提供这个选项的求情应当返回完整的列表信息   
* **偏移offset**   
返回ID偏移量为20的动物园信息   
GET /api.exmaple.com/zoos?offset=20   
注意:有偏移选项的前提是当前的集合是有序的,默认为0   
* **顺序order**   
按照name升序返回所有的动物园   
GET /api.exmaple.com/zoos?order=asc&orderby=name   
>**注意**   
升序为asc   
降序为desc   
随机顺序为rand   
没有提供这个选项的请求应当返回集合的默认顺序   
orderby后面的应当为能够进行排序的内容,如果没有orderby或者orderby指定的排序内容不能排序,应当按照首选排序内容按照order选项指定的顺序进行排序   
多级排序orderby=name,animal\_num,employee\_num   
* **内容筛选**   
获取ID为1的动物园   
GET /api.exmaple.com/zoos?id=1   
这条请求与   
GET /api.exmaple.com/zoos/1   
等价   
提供多种方式获取信息对于开发者来说是件好事   
>**注意**   
在很多场合可能需要对内容的大小进行筛选   
例如   
需要获取动物数量为200以上的动物园信息,可行的做法是返回所有动物列表让客户端进行筛选,如果要在请求中加入例如这一信息一种可行的设计是   
GET api.exmaple.com/zoos?animal\_num\_lower=200   
上限可以为   
GET api.exmaple.com/zoos?animal\_num\_upper=300   
在设计API时应当选择合适的方式进行取舍   
   
不同的选项之间可以进行组合,例如下面是一个较为完整的请求   
       
    GET http://api.example.com/animals?offset=10&limit=20&order=rand   
   
###请求验证   
对于大多说情况下我们要对发送过来的请求进行**语法上**的验证,在语法错误的时候返回验证错误的信息以便开发者进行调试   
PUT和POST方法都会对服务器上的资源进行更新,对于结构不可变的资源应当对这两种方法中的属性进行检查,在检查出有错误时应当最大限度的让请求能够正常执行,即是有不符合结构的属性名存在时应当忽视错误的属性而不影响其他属性的修改   
例如   
   
    PATCH /animals/1   
    name=123&xxx=abc   
animal中没有xxx的属性   
如果animal是结构可变的则要在id=1的animal中添加xxx属性   
如果animal结构是不可变的,就要忽视xxx属性,只修改name属性   
   
###版本迭代   
添加新的端点或者为资源加上新的属性的时候不需要增加API的版本号,但是有必要在文档中更新相应的说明信息.   
当你想要删除某一个API端点或者服务的时候,应当在文档中标识即将停用(to be deprecated),并继续支持一段时间,在更远版本的API中删除该项,更加人性化的做法是给使用者发送一封API更新的邮件,好让开发者及时更新代码   
API版本号的更新不需要非常频繁,小的更新完全可以在当前版本上进行,只有在重新组织、添加了许多特性的情况下才有必要提升版本号   
不同API的版本号应该在URL片段中显示出来,例如   
api.example.com/v1/animals   
api.example.com/v2/animals   
example.com/api/v1/animals   
example.com/api/v2/animals   
   
###文档   
文档的编写应当遵循清晰简洁的原则,最大限度给开发者提供信息,要求配有使用的小例子或者代码片段,以便开发者进行测试   
例如   
   
>     animal的结构   
    {   
        id:1,//动物的id   
        name:'Nick',//动物的名字   
        age:13,//动物的年龄   
        zoo_id:1//动物所在动物园的id   
    }   
根据id获取动物信息   
GET /animals/**ID**   
ID替换成对应动物的id   
返回值是json或者xml格式的动物信息   
返回值的格式由请求头信息的accept决定   
   
文档中应当对下列信息进行详细说明   
* 端点   
* 资源结构及属性命名   
* 使用者授权方式   
* 使用者权限说明   
* 测试方法(代码)   
   
###数据统计   
在API实现的过程中,应该对使用者每次请求和访问添加计数,甚至是详细使用记录,这样做有助于日后分析API的使用情况.对于使用量最高的API端点进行优化可以大大提升服务的效率和使用者的满意度,对于使用量小的API端点可以考虑废止或者更新添加新的内容   
   
###安全   
在API开放使用的过程中,我们应当知晓每一个使用者的信息,并对其身份进行认证,对传输的信息进行加密   
   
**身份认证**   
目前安全可靠的认证方式是OAuth2.0,所以认证的方式最好是使用OAuth或者xAuth,但是装配OAuth的过程可能耗费大量的时间和精力,更为简便的做法是使用挑战码/应答进行验证   
挑战码/应答的验证做法是,服务器方产生一条与客户机有关的随机信息,服务器和客户机使用同一种算法进行计算,客户机将计算的结果返回给服务器,由服务器比对两者是否相同来认证客户机是否是可信赖的使用方,这种做法可以一定程度上防范重放攻击   
   
**信息加密**   
对于所有的资源信息应当使用https进行加密   
对于像用户密码这类非常敏感的信息不应当向第三方开发者开发   
   
**权限管理**   
对于每一个身份认证过的用户必须要进行相应的权限管理   
在管理权限的时候应当针对每一种方法和资源赋予不同的权限   
* **GET** 对开放信息没有权限限制,对敏感信息(隐私协议中受保护的信息)应当不对外开放   
* **PUT/POST/PATCH/DELETE** 可以修改服务器上信息的操作必须要对用户进行认证,并分级管理,只允许可信赖的用户进行修改   
   
###规范更新   
该设计规范为团队共享资源,并非一次书写终身适用,况且一人之力也有限.   
团队各组人可以通力合作,共同完善这份文档   
   
##参考文献   
[Thoughts on RESTful API Design](http://restful-api-design.readthedocs.org/en/latest/)   
[Principles of good RESTful API Design](http://codeplanet.io/principles-good-restful-api-design/)   
[Wikipedia: Representational state transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)   
[Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)   
[Wikipedia: List of HTTP status code](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes)   
[Wikipedia: Replay attack](http://en.wikipedia.org/wiki/Replay_attack)   
[百度百科: HTTP状态码](http://baike.baidu.com/view/1790469.htm?fr=aladdin#3)   
[百度百科: 重放攻击](http://baike.baidu.com/view/1569933.htm)   
