#gateway认证方案

* 请求格式

用户验证通过 HTTP header 来进行。Header中包含2部分，MSP-AppKey和MSP-AuthKey。MSP-AppKey头标明当前调用者是哪个App，而MSP-AuthKey头（生成方式见下）用来对调用者鉴定。


* 请求验证

当消费者调用 MSP 上的微服务接口时，gateway需要对头部信息中 MSP-AuthKey 进行验证，MSP-AuthKey 的生成规则如下：

````java
//当前时间毫秒数 为 timestamp格式的时间
String authKey = MD5（client_id + client_secret +'AT' + 当前时间毫秒数 ）+ '.' +当前时间毫秒数
````

例如：消费者的AppKey是EA7DE061A6AA48BF997F81E8BC8138BE，client\_id是0C64F80F490B46439EBD36B0092298BC, client\_secret是1DF41BC6DB5944D8BE82605DA7B88591，则消费者应在请求头部信息MSP-AuthKey中设置的值为："0C64F80F490B46439EBD36B0092298BC"+"1DF41BC6DB5944D8BE82605DA7B88591"+"AT"+"62FB16B2-0ED6-B460-1F60-EB61954C823B"+"AT"+当前时间毫秒数组合字符串后通过MD5加密后，返回的字符串＋.当前时间毫秒数。

示例代码如下：

````java
Timestamp now = new Timestamp(System.currentTimeMillis());
String authKey = MD5("0C64F80F490B46439EBD36B0092298BC"+"1DF41BC6DB5944D8BE82605DA7B88591"+"AT"+now.getTime())+"."+now.getTime()
````

* 验证流程

1. 请求时间有效性校验：从authKey中取出请求时间，校验是否过期
2. app身份校验：根据AppKey查询client\_id和client\_secret后，与请求header中的authKey做比对
3. 权限校验：根据AppKey查询已申请的service\_key列表，校验是否有权限消费所请求的service_key（从url中获取）




  
  
  
  
  