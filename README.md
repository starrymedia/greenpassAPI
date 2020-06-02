# greenpassAPI
GreenPass Api接口文档

 [TOC] 

 ### 概述
 
欢迎使用GreenPass API！ 你可以使用此 API 得到亦来云DID账号，管理自己的健康数据（体温、试剂检测、位置变更或者应用自定义的健康数据类型），获取健康状态。
 ### 名词解释
名称	说明
did	如无特别说明本文所提及的did均指的是Elastos Did

 #### 统一说明

 ##### 系统域名
 
测试系统域名:https://greenpass.starrymedia.com:4433
正式系统域名:

 ##### 参数说明:
一次请求的发起,应当包含公共请求参数和具体接口的业务参数,其中公共请求参数是必须的要传递的.业务请求参数应当根据具体的接口来进行传递

公共请求参数:

名称 | 类型 | 说明
---|---|---
timestamp|	string|	时间戳
sign|	string	|签名
tenantId|	int	|租户id

备注:

 ##### 签名生成方案:
 
将业务参数按照一定的排序规则进行排序,将排序之后的结果结合应用授权码使用AES加密,即可得到相应的签名

排序规则:

按照字段名的ASCII码从小到大排序

    public   String sign(Map<String, String> map, String secret) {

     String result = "";
    try {
        List<Map.Entry<String, String>> infoIds = new ArrayList<Map.Entry<String, String>>(map.entrySet());
        //对所有传入参数按照字段名的 ASCII 码从小到大排序（字典序）
        infoIds.sort(new Comparator<Map.Entry<String, String>>() {
            public int compare(Map.Entry<String, String> o1, Map.Entry<String, String> o2) {
                return (o1.getKey()).compareTo(o2.getKey());
            }
        });
        // 构造签名键值对的格式
        StringBuilder sb = new StringBuilder();
        for (Map.Entry<String, String> item : infoIds) {
            if (item.getKey() != null || item.getKey() != "") {
                String key = item.getKey();
                String val = item.getValue();


                if (!(val == "" || val == null)) {
                    sb.append(key + "=" + val + "&");
                }
            }
        }
        //删除结尾&
        sb.deleteCharAt(sb.length() - 1);

        //签名
        result = HMACSHA256.sha256_HMAC(sb.toString(), secret);

    } catch (Exception e) {
        return null;
    }
    return result;
    }
    
 ##### 请求参数示例  
    Map<String, String> param = new HashMap<>();
    param.put("timestamp", String.valueOf(System.currentTimeMillis()));
    param.put("tenantId", tenantId);
    param.put("userDid", did);
    param.put("pageNum", String.valueOf(page));
    param.put("pageSize", String.valueOf(size));
    param.put("sign", sign(param, secret));

 
 #### 返回格式
 ##### 无数据返回
 Demo如下

    {
    "code":10006,
    "map":{},
    "msg":"",
    "success":false
    }

 ##### 有数据返回
 Demo如下

    {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":{
    "uid":XX,
    "nickName":"GREENPASS",
		...
    "temperatureType":"C",
    "parkIds":[
    15
    ],
    "parkId":null
    },
    "map":{}
    }

 #### 字段说明: 
 


名称 | 类型 | 说明
---|---|---
code|	Int	| 返回状态码
success| 	Boolean	|返回结果
msg	| String	|返回信息
map	| JSONObject|	预留字段
data|	Object|	响应结果.




 ### 接入说明

 #### 准备工作:
首先您需要申请测试环境的授码和租户id,只有拥有了合法的授权码,您才可以对接我们的服务.参照相关文档进行对接测试即可.当您所需要的操作均已在测试环境调试通过时,只需要将请求的地址切换到我们提供的正式环境,同时将授权码更换为正式的授权码即可访问我们的服务.

 #### 附
授权码申请地址:

授权码申请邮箱地址: contact@mygreenpass.life

注意:
授权码是您接入GreenPass的唯一凭证, 请务必保存好您的授权码,同时请务必向第三方透露您的授权码.当GreenPass检测到您的恶意非法操作时,GreenPass有权停止对您的服务,并保留诉讼的权利.

如果您已经拿到了自己的授权码,现在可以开始尝试接入GreenPass了.调试工具推荐使用PostMan API Client,这里是下载地址: https://www.postman.com/product/api-client/
下面开始接入吧!

 #### 创建您的did

说明:
接入GreenPass的所有数据需要一个did来作为存储或者取出的唯一标识,通过此接口您将获取到您的did


请求地址:

	/third/1.0/user/thirdAppCreate.do
	
请求方式:
	POST
请求参数:
	
	
 参数名 |	类型 |	是否必填 |	说明
 ---  |  --- |  --- |  ---
userId | 	string |是  | 	第三地方应用用户id


请求范例:
	

响应参数:
	
    {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":"iXgvjQp5DZZMnqXXXXXXXXXXXXXXXXXXXX",
    "map":{}
    }



 #### 获取用户信息

说明:
	根据did获取用户信息
	
请求地址:

	/third/1.0/user/userInfo.json
	
请求方式:
		POST
请求参数:
	
	
 参数名 |	类型 |	是否必填 |	说明
 ---  |  --- |  --- |  ---
userDid | 	String | 	是 | 	用户的did


请求范例:
	
	

响应参数:

    {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":{
        "uid":3XX,
        "account":"1_1000",
        "nickName":null,
        "gender":null,
        "email":null,
        "idCard":null,
        "json":null,
        "name":null,
        "profilePhoto":null,
        "totalScore":20,
        "status":"enabled",
        "temperatureType":null,
        "parkIds":null,
        "parkId":null,
        "did":"iXgvjQp5DZZMnqXXXXXXXXXXXXXXXXXXX"
    },
    "map":{}
    }





 #### 保存健康信息

说明:
	记录健康信息
	
请求地址:

	/third/1.0/health/save.do
	
请求方式:
		POST
		
请求参数:
	
 参数名 |	类型 |	是否必填 |	说明
 ---  |  --- |  --- |  ---
type | 	Int	 | 是  | 打卡类型(1:体温打卡2:试剂检测3:位置打卡4:其他打卡)
detail | 	String |是  | 	数据内容
userDid	 | String | 是 | 	用户的did
temperatureType(可选) | 	String | 否 | 	温度类型(C:摄氏度F:华氏度)
authenticatorType | 	Int | 是 | 	数据提交类型
evidenceImage(可选)	 | String	 | 否 | 证明图片
location(可选) | 	String | 否  | 	位置
address(可选) | 	String |  否 | 	位置
mapType(可选) | 	Int	 |  否 | 地图类型
latitude(可选) | 	BigDecimal	 |   否| 经度
longitude(可选) | 	BigDecimal |   否| 	纬度

说明:

当打卡类型为体温打卡时,温度类型必须要有.当打卡类型为试剂检测时,证明图片必须要有.当打卡类型为位置打卡时,地图类型要和经纬度必须要有
	

请求范例:

响应参数:

        {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":{
        "id":8XX,
        "createTime":1590112952909,
        "updateTime":null,
        "deleted":false,
        "weight":null,
        "version":null,
        "tenantId":1,
        "json":null,
        "authenticatorType":1,
        "mapType":null,
        "status":1,
        "type":1,
        "type1":null,
        "authenticator":"iXgvjQp5DZZMnqXXXXXXXXXXXXXXXXXXXX",
        "coordinates":null,
        "detail":"36.6",
        "evidenceImage":null,
        "didHash":null,
        "hash":null,
        "eidHash":null,
        "latitude":null,
        "location":null,
        "address":null,
        "longitude":null,
        "userId":3XX,
        "remarks":null,
        "parkName":null
    },
    "map":{}
    }



 #### 获取我的健康记录历史记录

说明:

获取我的健康记录历史记录
	
请求地址:

	/third/1.0/health/page.json
	
请求方式:	POST
	
请求参数:
	
	
 参数名 |	类型 |	是否必填 |	说明
 ---  |  --- |  --- |  ---
pageNum |	Long |是	 |页码	
pageSize |	Long | 否| 	每页条数，默认是10
userDid |	String | 是|	用户的did	

请求范例:

响应参数:

    {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":{
        "list":[
            {
                "id":8XX,
                "createTime":1590112952000,
                "updateTime":null,
                "deleted":false,
                "weight":null,
                "version":null,
                "tenantId":1,
                "json":null,
                "authenticatorType":1,
                "mapType":null,
                "status":1,
                "type":1,
                "type1":null,
                "authenticator":null,
                "coordinates":null,
                "detail":"36.6",
                "evidenceImage":null,
                "didHash":null,
                "hash":null,
                "eidHash":null,
                "latitude":null,
                "location":null,
                "address":null,
                "longitude":null,
                "userId":337,
                "remarks":null,
                "parkName":null
            }
        ],
        "paras":{
            "id":null,
            "createTime":1588867200000,
            "updateTime":null,
            "deleted":null,
            "weight":null,
            "version":null,
            "tenantId":1,
            "json":null,
            "authenticatorType":null,
            "mapType":null,
            "status":null,
            "type":null,
            "type1":2,
            "authenticator":null,
            "coordinates":null,
            "detail":null,
            "evidenceImage":null,
            "didHash":null,
            "hash":null,
            "eidHash":null,
            "latitude":null,
            "location":null,
            "address":null,
            "longitude":null,
            "userId":3XX,
            "remarks":null,
            "parkName":null
        },
        "orderBy":null,
        "pageNumber":1,
        "pageSize":10,
        "totalPage":1,
        "totalRow":1,
        "lastPage":true,
        "firstPage":true
    },
    "map":{}
    }



 #### 获取我的健康记录历史记录

说明:

	获取我的健康记录历史记录
	
请求地址:

	/third/1.0/health/trail.json
	
请求方式:
	GET
	
请求参数:
 参数名 |	类型 |	是否必填 |	说明
 ---  |  --- |  --- |  ---
userDid	 | String |是  | 	用户的did

请求范例:


响应参数:

    {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":[
        {
            "lng":"3x.2xxxx91016",
            "lat":"1xx.5xxxx42944"
        },
        {
            "lng":"3x.2xxxx91016",
            "lat":"1xx.5xxxx42944"
        },
        {
            "lng":"3x.2xxxx91016",
            "lat":"1xx.5xxxx42944"
        },
        {
            "lng":"3x.2xxxx91016",
            "lat":"1xx.5xxxx42944"
        },
    ],
    "map":{}
     }
     



  #### 获取我的最后一条健康记录
 
 说明:
	获取我的最后一条健康记录
请求地址:

	/third/1.0/health/findByNowDay.json
	
 请求方式:
	POST
	
请求参数:
 参数名 |	类型 |	是否必填 |	说明
 ---  |  --- |  --- |  ---
userDid	|String	|是|用户的did

请求范例:


响应参数:

    {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":{
        "id":8XX,
        "createTime":1590118456000,
        "updateTime":null,
        "deleted":false,
        "weight":null,
        "version":null,
        "tenantId":1,
        "json":"",
        "authenticatorType":1,
        "mapType":1,
        "status":1,
        "type":1,
        "type1":null,
        "authenticator":null,
        "coordinates":"",
        "detail":"36.6",
        "evidenceImage":"",
        "didHash":null,
        "hash":null,
        "eidHash":null,
        "latitude":null,
        "location":"",
        "address":"",
        "longitude":null,
        "userId":XX,
        "remarks":null,
        "parkName":null
    },
    "map":{}
    }
    



 #### 错误码说明


    错误码   |	说明
    ---  |  ---
    10006 | 网络繁忙
    10007 | 参数异常
    10005 | 请求超时
    10008 | 签名不匹配
    10002 | 系统参数错误
    10001 | 参数错误
    20001 | 未授权
    20005 | 用户不存在
    20009 | 授权错误
    20005 | 用户不存在
