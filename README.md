# greenpassAPI
GreenPass Api接口文档

 [TOC] 

 ### 概述
 
欢迎使用GreenPass API！ 你可以使用此 API 得到亦来云DID账号，管理自己的健康数据（体温、试剂检测、位置变更或者应用自定义的健康数据类型），获取健康状态。
 ### 名词解释
名称	说明
did	如无特别说明本文所提及的did均指的是ElastosDID

 #### 统一说明

 ##### 系统域名
 
测试系统域名:https://greenpass.starrymedia.com:4433
正式系统域名:


 ### 接入说明

 #### 准备工作:
首先您需要申请测试环境的授码和租户id,只有拥有了合法的授权码,您才可以对接我们的服务.参照相关文档进行对接测试即可.当您所需要的操作均已在测试环境调试通过时,只需要将请求的地址切换到我们提供的正式环境,同时将授权码更换为正式的授权码即可访问我们的服务.

 #### 附
授权码申请地址:

授权码申请邮箱地址: contact@mygreenpass.life

注意:
授权码是您接入GreenPass的唯一凭证, 请务必保存好您的授权码,同时请务必不要向第三方透露您的授权码.
当GreenPass检测到您的恶意非法操作时,GreenPass有权停止对您的服务,并保留诉讼的权利.

如果您已经拿到了自己的授权码,现在可以开始尝试接入GreenPass了.调试工具推荐使用PostMan API Client,这里是下载地址: https://www.postman.com/product/api-client/
下面开始接入吧!

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
        "uid":3XX,//用户Id
        "account":"x_xxxx",//用户账号
        "nickName":null,//nick_name
        "gender":null,//性别
        "email":null,//邮箱
        "idCard":null,
        "json":null,
        "name":null,//名称
        "profilePhoto":null,
        "totalScore":20,
        "status":"enabled",//状态
        "temperatureType":null,温度类型
        "parkIds":null,
        "parkId":null,
        "did":"iXgvjQp5DZZMnqXXXXXXXXXXXXXXXXXXX"//did
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
        "id":8XX,//id
        "createTime":1590112952909,
        "updateTime":null,
        "deleted":false,
        "weight":null,
        "version":null,
        "tenantId":xxxxxxx,//租户id
        "json":null,
        "authenticatorType":1,//
        "mapType":null,
        "status":1,//健康状态
        "type":1,//打卡类型
        "type1":null,
        "authenticator":"iXgvjQp5DZZMnqXXXXXXXXXXXXXXXXXXXX",//证明人did
        "coordinates":null,
        "detail":"36.6",//详情
        "evidenceImage":null,//图片证明
        "didHash":null,
        "hash":null,
        "eidHash":null,
        "latitude":null,
        "location":null,//位置
        "address":null,//地址
        "longitude":null,
        "userId":3XX,//用户Id
        "remarks":null,/备注
        "parkName":null//组织名称
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
                "tenantId":xxxxxxx,
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
            "tenantId":xxxxxx,
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
            "lng":"3x.2xxxx91016",//维度
            "lat":"1xx.5xxxx42944"//经度
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
        "id":8XX,//id
        "createTime":1590118456000,
        "updateTime":null,
        "deleted":false,
        "weight":null,
        "version":null,
        "tenantId":xxxxx,
        "json":"",
        "authenticatorType":1,//打卡类型
        "mapType":1,//地图类型
        "status":1,//状态
        "type":1,//打卡类型
        "type1":null,
        "authenticator":null,
        "coordinates":"",
        "detail":"36.6",//温度
        "evidenceImage":"",
        "didHash":null,
        "hash":null,
        "eidHash":null,
        "latitude":null,
        "location":"",
        "address":"",
        "longitude":null,
        "userId":XX,//用户Id
        "remarks":null,/?备注
        "parkName":null//位置名称
    },
    "map":{}
    }
    
 #### 用户扫描地点码保存通行记录
  
 说明:
 
    扫描地点二维码,保存用户在该地点的通行记录
    
 请求地址:
 
    /third/1.0/verify/accessLog.json
 	
 请求方式:
 
    POST 
 	
 请求参数:
 
 参数名|   类型| 是否必填|   说明
 ---|   ---|    ---|    ---
tenantId|integer|是|租户id
userDid|String|是|用户did
location|String|是|位置
coordinates|String|是|坐标
parkId|Long|是|地点id
locationName|String|是|地点名称
healthStatus|Integer|是|健康状态((1,"Healthy"), (2,"Unhealthy"), (3, "Not tested yet");)
mapType|Integer|是|地图类型(1:百度地图;2:谷歌地图;3:高德地图;4:腾讯地图)
type|Integer|是|打卡类型(1:体温打卡2:试剂检测3:位置打卡4:其他打卡)
detail|String|是|打卡详情(温度、阴性阳性、地区)
 
 请求范例:
 
 
 响应参数:
 
    {
     "code": 0,
     "success": true,
     "msg": "Succeed",
     "data": null,
     "map": {}
    }
 
 
 #### 扫码通行
   
  说明:
  
     扫描用户二维码或者预约码保存通行记录.
     
  请求地址:
  
     /third/1.0/verify/save.do
  	
  请求方式:
  
     POST 
  	
  请求参数:
  
  参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
userDid|String|是|扫码验证用户did
did|String|否|普通应用内预约用户did(被验证的用户did)
appointmentId|Long|否|网页预约的预约id
tenantId|integer|是|租户id
location|String|否|位置
coordinates|String|否|坐标
mapType|Integer|否|地图类型(1:百度地图;2:谷歌地图;3:高德地图;4:腾讯地图)
isThird|Boolean|否|是否为帮其他人打卡

  
  请求范例:
  
  
  响应参数:
  
      {
          "code": 0,
          "success": true,
          "msg": "Succeed",
          "data": {
              "msg": "Test Negative_No temperature in last 24 hours",//结论
              "verifierType": 4,//打卡类型
              "tenantName": "Demo",//租户名称
              "healthStatus": 3,//健康状态
              "createTime": 1591597122713,
              "companyName": "鲜鲜测试位置",
              "tenantId": xxxxxxx,//租户id
              "name": "q",//名词
              "did": "imothunWhDWxxxxxxxxxx4GZKthJc9d6uS"//did
          },
          "map": {}
      }
  
    
 #### 通行记录
       
  说明:
  
     获取用户验证通行的历史记录
     
  请求地址:
  
     /third/1.0/verify/page.json
    
  请求方式:
  
     POST 
    
  请求参数:
  
  参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
userDid|String|是|扫码验证用户did
pageNum|Long|是|当前页码
pageSize|Long|是|条数
startTimeLong|Long|否|起始时间时间戳
endTimeLong|Long|否|结束日期时间戳

  
  请求范例:
  
  
  响应参数:
  
      {
          "code": 0,
          "success": true,
          "msg": "Succeed",
          "data": {
              "list": [
                  {
                      "id": 324,//id
                      "createTime": 1591597122000,//创建时间
                      "updateTime": null,//更新时间
                      "deleted": false,//删除标志
                      "weight": null,
                      "version": null,
                      "tenantId":xxxxxxx,//租户id
                      "json": null,
                      "healthStatus": 3,//健康状态
                      "mapType": null,//地图类型
                      "appointmentId": null,//预约id
                      "parkId": null,//组织id
                      "authenticatorId": 72,//验证者id
                      "coordinates": null,//坐标
                      "describe": "鲜鲜测试位置",//描述
                      "healthLogId": null,//健康日志id
                      "location": null,//位置
                      "verifierId": 330,//被验证Id
                      "verifierType": "4",//被验证类型
                      "verifyDate": 1591597123000,
                      "authIds": null,
                      "startTime": null,
                      "endTime": null,
                      "startTimeLong": null,
                      "endTimeLong": null,
                      "parkIdsSet": null,
                      "authenticatorName": "哈哈哈哈哈哈",//验证者名称
                      "verifierName": "q",//被验证者名称
                      "healthLogType": null,
                      "healthLogStatus": null,
                      "healthLogDetail": null,
                      "appointmentLocation": null,
                      "parkName": null,
                      "appointmentDescribe": null,
                      "tenantName": "Demo",//租户名称
                      "pname": "鲜鲜测试位置"//位置名称
                  },
                  ...
                       {
                      "id": 315,
                      "createTime": 1590978964000,
                      "updateTime": null,
                      "deleted": false,
                      "weight": null,
                      "version": null,
                      "tenantId": xxxxx,
                      "json": null,
                      "healthStatus": 1,
                      "mapType": null,
                      "appointmentId": null,
                      "parkId": null,
                      "authenticatorId": null,
                      "coordinates": "",
                      "describe": "1",
                      "healthLogId": 916,
                      "location": "",
                      "verifierId": 72,
                      "verifierType": "1",
                      "verifyDate": 1590978964000,
                      "authIds": null,
                      "startTime": null,
                      "endTime": null,
                      "startTimeLong": null,
                      "endTimeLong": null,
                      "parkIdsSet": null,
                      "authenticatorName": null,
                      "verifierName": "哈哈哈哈哈哈",
                      "healthLogType": 3,
                      "healthLogStatus": null,
                      "healthLogDetail": "",
                      "appointmentLocation": null,
                      "parkName": null,
                      "appointmentDescribe": null,
                      "tenantName": "GreenPass",
                      "pname": "Starrymedia2"
                  }
              ],
              "paras": {
                  "id": null,
                  "createTime": null,
                  "updateTime": null,
                  "deleted": null,
                  "weight": null,
                  "version": null,
                  "tenantId": xxxxx,
                  "json": null,
                  "healthStatus": null,
                  "mapType": null,
                  "appointmentId": null,
                  "parkId": null,
                  "authenticatorId": 72,
                  "coordinates": null,
                  "describe": null,
                  "healthLogId": null,
                  "location": null,
                  "verifierId": null,
                  "verifierType": null,
                  "verifyDate": null,
                  "authIds": null,
                  "startTime": null,
                  "endTime": null,
                  "startTimeLong": null,
                  "endTimeLong": null,
                  "parkIdsSet": [
                      55,
                      56,
                      61,
                      15
                  ]
              },
              "orderBy": null,
              "pageNumber": 1,
              "pageSize": 10,
              "totalPage": 15,
              "totalRow": 145,
              "lastPage": false,
              "firstPage": true
          },
          "map": {}
      }
  
  
  ### 组织相关    
#### 创建地点(组织)
说明:
  
     创建一处新的地点或者组织
     
  请求地址:
  
    /third/1.0/park/create.do
    
  请求方式:
  
     POST 
    
  请求参数:
  
  参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
name|String|是|名称
location|String|是|位置
mapType|Integer|否|地图类型(地图类型。1:百度地图;2:谷歌地图;3:高德地图;4:腾讯地图)
coordinates|String|是|坐标
receptionists|String|否|接待者did:["did","did","did"]
logo|String|否|组织logo地址
photo|String|否|组织图片地址
info|String|否|组织介绍
isPublic|integer|是|是否公开地点
isPublicLocation|integer|是|是否显示详细位置

  
  请求范例:
  响应参数:
        
        {
            "code": 0,
            "success": true,
            "msg": "Succeed",
            "data": null,
            "map": {}
        }
    
        
####  编辑地点(组织)
说明:

    通过此接口,您可以编辑修改您创建的组织或者地点

请求地址:

     /third/1.0/park/edit.do

请求方式:

    POST
        
请求参数:

  参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  id|Long|是|组织id
  name|String|是|组织名称
  isPublic|integer|是|是否公开地点
  isPublicLocation|integer|是|是否显示详细位置
  location|String|是|组织位置
  mapType|integer|是|地图类型
  coordinates|String|是|坐标
  receptionists|String|否|接待者did:["did","did","did"]
  
请求范例:
响应参数: 

    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }

####  删除地点(组织)
说明:

    通过此接口,您可以删除您创建的组织或者地点,所有跟此地点相关的信息都将不可用

请求地址:

     POST /third/1.0/park/del.do

请求方式:

    POST
        
请求参数:

  参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  parkId|Long|是|组织id
  
请求范例:
响应参数: 

    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }

     
  ####  查询组织信息详情
  说明:
  
      通过此接口,您可以查询您创建创建的组织或者地点的信息
  
  请求地址:
  
       /third/1.0/park/findById.json
  
  请求方式:
  
      POST
          
  请求参数:
  
    参数名|   类型| 是否必填|   说明
    ---|   ---|    ---|    ---
    userDid|String|是|用户did
    id|Long|是|组织id
    
  请求范例:
  响应参数: 
  
      {
          "code": 0,
          "success": true,
          "msg": "Succeed",
          "data": {
              "id": 83,//id
              "createTime": 1591600305000,
              "updateTime": 1591604215000,
              "deleted": false,
              "weight": null,
              "version": null,
              "tenantId": xxxxxx,//租户id
              "json": null,
              "mapType": 2,//地图类型
              "coordinates": "31.204008122876772,121.599235291026",//坐标
              "location": "上海",//地点
              "name": "鱼子酱",//名称
              "areaCode": "000086",//区域编码
              "userId": 330,//用户Id
              "logo": null,//地点logo
              "photo": null,//地点图片
              "info": null,//信息
              "phone": null,//电话
              "isPublic": 1,//是否公开地点
              "isPublicLocation": 1,//是否显示详细位置
              "receptionists": null,//接待者
              "isAdmin": 0,//
              "haveAppointment": false,
              "role": null,
              "provinceId": null,
              "province": null,
              "city": null
          },
          "map": {}
      }  
        
        
  ####  获取组织管理员列表
说明:

    通过此接口,您可以获取指定组织的管理员列表

请求地址:

    /third/1.0/park/page.json

请求方式:

    POST
        
请求参数:

  参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  pageNum|Long|是|页码
  pageSize|Long|是|条数
  
  
请求范例:
响应参数: 

    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "list": [
                {
                    "id": 83,//id
                    "createTime": 1591600305000,
                    "updateTime": 1591604215000,
                    "deleted": false,
                    "weight": null,
                    "version": null,
                    "tenantId": 1,
                    "json": null,
                    "mapType": 2,/地图类型
                    "coordinates": "31.204008122876772,121.599235291026",//坐标
                    "location": "上海",//位置
                    "name": "鱼子酱",//名称
                    "areaCode": "000086",//区域编码
                    "userId": 330,//用户ID
                    "logo": null,
                    "photo": null,
                    "info": null,
                    "phone": null,
                    "isPublic": 1,//是否公开地点
                    "isPublicLocation": 1,//是否显示详细位置
                    "receptionists": null,
                    "isAdmin": 1,//是否是管理员
                    "haveAppointment": false,
                    "role": [
                        3//角色
                    ],
                    "provinceId": null,
                    "province": null,
                    "city": null
                },
                {
                    "id": 82,
                    "createTime": 1590731612000,
                    "updateTime": 1590733390000,
                    "deleted": false,
                    "weight": null,
                    "version": null,
                    "tenantId": xxxxxx,
                    "json": null,
                    "mapType": 2,
                    "coordinates": "31.204001177379357,121.59918527668138",
                    "location": "上海市浦东新区博霞路11",
                    "name": "hhhhhh",
                    "areaCode": "000086",
                    "userId": 330,
                    "logo": "",
                    "photo": "",
                    "info": "test",
                    "phone": null,
                    "isPublic": 1,
                    "isPublicLocation": 0,
                    "receptionists": [
                        {
                            "uid": 330,//接待者yonhuid
                            "account": "zsxxxxxxn@163.com",//账户
                            "nickName": "q",//nick_name
                            "gender": null,
                            "email": "zsuxxxn@163.com",
                            "idCard": null,
                            "json": null,
                            "name": null,
                            "profilePhoto": null,
                            "totalScore": 82,//分
                            "status": "enabled",//状态
                            "temperatureType": null,
                            "parkIds": null,
                            "parkId": null,
                            "did": "imothunWhDxxxxxxxx4GZKthJc9d6uS"//did
                        }
                    ],
                    "isAdmin": 1,
                    "haveAppointment": false,
                    "role": [
                        3,
                        7
                    ],
                    "provinceId": null,
                    "province": null,
                    "city": null
                }
            ],
            "paras": {
                "parkIds": [
                    82,
                    61
                ],
                "tenantId": xxxxxxx,
                "userId": 330
            },
            "orderBy": null,
            "pageNumber": 1,
            "pageSize": 10,
            "totalPage": 1,
            "totalRow": 3,
            "lastPage": true,
            "firstPage": true
        },
        "map": {}
    }
 
     
     
####  根据用户did获取该用户下所有的组织相关信息
说明:

    通过此接口,您可以获取指定组织的管理员列表

请求地址:

       /third/1.0/park/parkInfo.json   

请求方式:

    POST
        
请求参数:

  参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did

  
请求范例:
响应参数:
 
        {
            "code": 0,
            "success": true,
            "msg": "Succeed",
            "data": [
                {
                    "id": 61,//id
                    "createTime": 1586568511000,
                    "updateTime": 1590739761000,
                    "deleted": false,
                    "weight": null,
                    "version": null,
                    "tenantId": xxxxxX,//租户id
                    "json": null,
                    "mapType": 2,//地图类型
                    "coordinates": "31.0769472,121.5958393",//坐标
                    "location": "上海市浦东新区航头镇鹤恒路鹤沙航城东茗苑",//地址
                    "name": "鲜鲜测试位置",//名称
                    "areaCode": "000086",//区域编码
                    "userId": 72,//用户id
                    "logo": "url",//图片的地址
                    "photo": null,//图片地址
                    "info": null,
                    "phone": null,
                    "isPublic": 0,//是否公开地点
                    "isPublicLocation": 0//是否显示详细位置
                },
                {
                    "id": 82,
                    "createTime": 1590731612000,
                    "updateTime": 1590733390000,
                    "deleted": false,
                    "weight": null,
                    "version": null,
                    "tenantId": xxxxxx,
                    "json": null,
                    "mapType": 2,
                    "coordinates": "31.204001177379357,121.59918527668138",
                    "location": "上海市浦东新区博霞路11",
                    "name": "hhhhhh",
                    "areaCode": "000086",
                    "userId": 330,
                    "logo": "",
                    "photo": "",
                    "info": "test",
                    "phone": null,
                    "isPublic": 1,//是否公开地点
                    "isPublicLocation": 0//是否显示详细位置
                }
            ],
            "map": {}
        }
           
  
####  模糊查询组织
说明:

    通过此接口,您可以输入任意内容模糊查询组织

请求地址:

       /third/1.0/park/searchName.do

请求方式:

    POST
        
请求参数:

  参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  name|String|是|组织名称

  
请求范例:
响应参数:
 
         {
             "code": 0,
             "success": true,
             "msg": "Succeed",
             "data": [
                 {
                     "id": 15,
                     "createTime": 1585563126000,
                     "updateTime": 1589016197000,
                     "deleted": false,
                     "weight": null,
                     "version": null,
                     "tenantId": xxxxxx,
                     "json": null,
                     "mapType": 2,
                     "coordinates": "31.20638902179997,121.5948918226798",
                     "location": "Zuchongzhi Road, Pudong",
                     "name": "Lizard Design Studio",
                     "areaCode": "000086",//区域编码
                     "userId": 80,//用户id
                     "logo": "url",//logo地址
                     "photo": null,
                     "info": null,
                     "phone": null,
                     "isPublic": 1,
                     "isPublicLocation": 0
                 }
             ],
             "map": {}
         }     
        
       
####  校验组织名称
说明:

    通过此接口,您可以校验组织名称是否已经存在

请求地址:

     /third/1.0/park/verifyName.do

请求方式:

    POST
        
请求参数:

  参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  name|String|是|组织名称

  
请求范例:
响应参数:
 
     {
       "code": 0,
       "data": true,
       "map": {},
       "msg": "string",
       "success": true
     }
    
          
 ###人员管理
#### 添加员工
说明:
    
    通过此接口,您可以为自己创建的组织(企业)添加成员(员工)

请求地址:

    /third/1.0/employee/add.do

请求方式:

    POST 
请求参数:

参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  parkId|Long|是|组织id
  did|String|是|要添加的成员did
  lang|String|否|语言参数
      
请求示例:
响应参数:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }
 
 
#### 编辑员工
说明:
    
    通过此接口,您可以编辑自己添加的员工

请求地址:

    /third/1.0/employee/edit.do

请求方式:

    POST 
请求参数:

参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  parkId|Long|是|组织id
  id|Long|是|要修改的成员id
  lang|String|否|语言参数
  email|String|否|邮箱
  id|Long|是|员工id
      
请求示例:
响应参数:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }
 
 
#### 删除员工
说明:
    
    通过此接口,您可以移除自己添加的员工(成员)

请求地址:

    /third/1.0/employee/delete.do

请求方式:

    POST 
请求参数:

参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  id|Long|是|员工id
      
请求示例:
响应参数:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }
  

#### 根据员工Id查询员工信息
说明:
    
    通过此接口,您可以查询己添加的员工(成员)信息

请求地址:

    /third/1.0/employee/findById.json

请求方式:

    POST 
请求参数:

参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  id|Long|是|员工id
      
请求示例:
响应参数:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "id": 105,//员工ID
            "createTime": 1591611094000,
            "updateTime": 1591612132000,
            "deleted": false,
            "weight": null,
            "version": null,
            "tenantId": Xxxxxx,//租户id
            "json": null,
            "gender": null,//性别
            "companyId": null,
            "employNo": null,
            "did": "iYQnfPtnLn8J4xxxxxxxxP3rs8SZf",//did
            "mobile": null,
            "email": null,
            "name": "小杨6",
            "organizeId": null,
            "parkId": 83,//组织id
            "genderSwitch": null,
            "healthStatus": null,
            "isAdmin": null,
            "companyName": null,
            "parkName": "鱼子酱"//组织名称
        },
        "map": {}
    }
 


#### 根据组织下员工列表
说明:
    
    通过此接口,您可以查询指定组织id下的所有成员

请求地址:

    /third/1.0/employee/page.json

请求方式:

    POST 
请求参数:

参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  parkId|Long|是|用户did
  pageNum|Long|是|当前页
  pageSize|Long|是|条数
      
请求示例:
响应参数:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "list": [
                {
                    "id": 105,//id
                    "createTime": 1591611094000,
                    "updateTime": 1591612132000,
                    "deleted": false,
                    "weight": null,
                    "version": null,
                    "tenantId": xxxxxxx,//租户id
                    "json": null,
                    "gender": null,
                    "companyId": null,
                    "employNo": null,
                    "did": "iYQnfPtnLn8J4kxxxxxxxxFUP3rs8SZf",//did
                    "mobile": null,
                    "email": null,
                    "name": "小杨6",
                    "organizeId": null,
                    "parkId": 83,//组织id
                    "genderSwitch": null,
                    "healthStatus": 3,//健康状态
                    "isAdmin": 0//是否是管理员
                }
            ],
            "paras": {//入参
                "id": null,
                "createTime": null,
                "updateTime": null,
                "deleted": false,
                "weight": null,
                "version": null,
                "tenantId": xxxxxx,
                "json": null,
                "gender": null,
                "companyId": null,
                "employNo": null,
                "did": null,
                "mobile": null,
                "email": null,
                "name": null,
                "organizeId": null,
                "parkId": 83,
                "genderSwitch": null,
                "healthStatus": null,
                "isAdmin": null
            },
            "orderBy": "employee.create_time desc",
            "pageNumber": 1,
            "pageSize": 10,
            "totalPage": 1,
            "totalRow": 1,
            "lastPage": true,
            "firstPage": true
        },
        "map": {}
    }
 
 
 ####  获取员工权限 
 说明:
     
     通过此接口,您可以获取员工的权限,从而可以对员工的权限进行操作
 
 请求地址:
 
     /third/1.0/employee/role.json
 
 请求方式:
 
     POST 
 请求参数:
 
 参数名|   类型| 是否必填|   说明
   ---|   ---|    ---|    ---
   userDid|String|是|用户did
   did|String|是|员工did
   parkId|Long|是|组织id
       
 请求示例:
 响应参数:
 
     {
         "code": 0,
         "success": true,
         "msg": "Succeed",
         "data": {
             "receptionist": 0,//是否是接待者(0否,1是)
             "root": 0,//是否是地点创建者(0否,1是)
             "admin": 0//是否是管理员(0否,1是)
         },
         "map": {}
     }
 
 
 #### 管理员添加
 
 说明:
      
      通过此接口,您可以将指定的员工添加为管理员,该员工可以获得更多权限
  
  请求地址:
  
     /third/1.0/employee/admin/add.do
  
  请求方式:
  
      POST 
  请求参数:
  
  参数名|   类型| 是否必填|   说明
    ---|   ---|    ---|    ---
    userDid|String|是|用户did
    did|String|是|员工did
    parkId|Long|是|组织id
        
  请求示例:
  响应参数:
  
     {
       "code": 0,
       "data": {},
       "map": {},
       "msg": "string",
       "success": true
     }
 
 
 #### 管理员删除
  
  说明:
       
       通过此接口,您可以将指定的员工的管理员权限移除
   
   请求地址:
   
      /third/1.0/employee/admin/delete.do
   
   请求方式:
   
       POST 
   请求参数:
   
   参数名|   类型| 是否必填|   说明
     ---|   ---|    ---|    ---
     userDid|String|是|用户did
     did|String|是|员工did
     parkId|Long|是|组织id
         
   请求示例:
   响应参数:
 
    {
      "code": 0,
      "data": {},
      "map": {},
      "msg": "string",
      "success": true
    }
 
 
 #### 接待者添加
   
说明:
        
     通过此接口,您可以将指定的员工的添加接待者权限
    
请求地址:

    /third/1.0/employee/receptionist/add.do

请求方式:

    POST 
请求参数:

参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  userDid|String|是|用户did
  did|String|是|员工did
  parkId|Long|是|组织id
      
请求示例:
响应参数:

     {
       "code": 0,
       "data": {},
       "map": {},
       "msg": "string",
       "success": true
     }
 
 
 #### 接待者删除
    
 说明:
         
      通过此接口,您可以将指定的员工的接待者权限移除
     
 请求地址:
 
     /third/1.0/employee/receptionist/delete.do
 
 请求方式:
 
     POST 
 请求参数:
 
 参数名|   类型| 是否必填|   说明
   ---|   ---|    ---|    ---
   userDid|String|是|用户did
   did|String|是|员工did
   parkId|Long|是|组织id
       
 请求示例:
 响应参数:
 
      {
        "code": 0,
        "data": {},
        "map": {},
        "msg": "string",
        "success": true
      }
      

#### 自己取消接待者身份
    
 说明:
         
      通过此接口,接待者可以取消自己为接待者的身份
     
 请求地址:
 
     /third/1.0/employee/receptionist/remove.do
 
 请求方式:
 
     POST 
 请求参数:
 
 参数名|   类型| 是否必填|   说明
   ---|   ---|    ---|    ---
   userDid|String|是|用户did
   did|String|是|员工did
   parkId|Long|是|组织id
       
 请求示例:
 响应参数:
 
      {
        "code": 0,
        "data": {},
        "map": {},
        "msg": "string",
        "success": true
      }
 
 
###   预约相关
####  保存预约信息     
 
 说明:
          
       通过此接口,可以保存预约登记信息
      
 请求地址:
  
      /third/1.0/appointment/save.do
  
 请求方式:
  
      POST 
 请求参数:
  
 参数名|   类型| 是否必填|   说明
    ---|   ---|    ---|    ---
    name|String|是|预约者名字
    email|String|是|预约者邮箱
    appointmentDescribe|String|是|拜访事项
    appointmentType|String|是|预约类型。1：健康码；2：预约码；3；健康预约码
    appointmentTime|Long|是|预约日期
    parkId|Long|是|预约组织的id
    timeZone|String|是|时区
    userDid|String|否|用户did,当预约类型为3的时候必须传
        
 请求示例:
 
        tenantId:Xxxxxx
        timestamp:1591761176173
        sign:3a6477843f29aae0c2014800b8f7efde5df25de5601d9807697c523f10ec695a
        name:zhangsan
        email:zzz@163.com
        appointmentDescribe:111
        appointmentType:1
        appointmentTime:1591844460000
        parkId:83
        timeZone:GMT+0800
        
 响应参数:
        
     {
         "code": 0,
         "success": true,
         "msg": "Succeed",
         "data": true,
         "map": {}
     }
 
 
 ####  我的预约列表    

说明:
       
    通过此接口,可以获取已经预约过的预约信息列表
   
请求地址:

    /third/1.0/appointment/myAppointmentPage.json

请求方式:

   POST 
请求参数:

参数名|   类型| 是否必填|   说明
 ---|   ---|    ---|    ---
 pageNum|Long|是|页码
 pageSize|Long|是|每页条数
 
     
请求示例:

    tenantId:xxxxxx
    timestamp:1591767713330
    sign:c7bcdcaa760b9e9c9cfa529b82c543da3c466435d9b8a3238d3233430c0b5148
    userDid:ihsRJhHzUPXXXXXXX9Dv1BS4ASipFqB
    pageNum:1
    pageSize:10
     
     
响应参数:
     
     {
         "code": 0,
         "success": true,
         "msg": "Succeed",
         "data": {
             "list": [
                 {
                     "id": 167,
                     "createTime": 1591761297000,
                     "updateTime": 1591761303000,
                     "deleted": false,
                     "weight": null,
                     "version": null,
                     "tenantId": xxxxxx,
                     "json": "{\"timeZone\":\"GMT+0800\"}",
                     "appointmentStatus": 7,//预约状态EXAMINE(1, "审核中"),AGREE(2, "同意"),REFUSE(3, "拒绝"),END(4, "已结束"),VERIFY(5, "已验证"),DISABLED(6, "不可用"),CANCELLED(7,"取消"),
                     "appointmentType": 3,//预约类型
                     "appointmentDescribe": "1",//预约描述
                     "email": "xyyz1207@163.com",//邮箱
                     "healthStatus": null,
                     "name": "11",//姓名
                     "parkId": 46,//预约组织Id
                     "userId": 72,//用户id
                     "appointmentDate": 1591844460000,//预约日期
                     "startTime": null,
                     "endTime": null,
                     "startTimeLong": null,
                     "endTimeLong": null,
                     "location": "中国广东省深圳市福田区桂花路3号",//地址
                     "parkName": "Shenzhen ",//预约组织名称
                     "coordinates": "22.51427136920438,114.056792087924",//坐标
                     "selected": false,
                     "isNotify": false
                 },
                 ...
                 {
                     "id": 154,
                     "createTime": 1589007641000,
                     "updateTime": 1589180145000,
                     "deleted": false,
                     "weight": null,
                     "version": null,
                     "tenantId": xxxxx,
                     "json": "{\"timeZone\":\"GMT+0800\"}",
                     "appointmentStatus": 4,//预约状态EXAMINE(1, "审核中"),AGREE(2, "同意"),REFUSE(3, "拒绝"),END(4, "已结束"),VERIFY(5, "已验证"),DISABLED(6, "不可用"),CANCELLED(7,"取消"),
                     "appointmentType": 3,//预约类型
                     "appointmentDescribe": "111",//描述
                     "email": "xyyz1207@163.com",//邮箱
                     "healthStatus": null,
                     "name": "666",//名称
                     "parkId": 76,//组织id
                     "userId": 72,//用户id
                     "appointmentDate": 1589014860000,
                     "startTime": null,
                     "endTime": null,
                     "startTimeLong": null,
                     "endTimeLong": null,
                     "location": "中国上海市浦东新区祖冲之路",//位置
                     "parkName": "CAT Coffe hkjd jrjeheue hw ieoei eheu fhdjdjfjfifjgjgkg",//组织名称
                     "coordinates": "31.20646260354803,121.5949145179426",
                     "selected": false,
                     "isNotify": false
                 }
             ],
             "paras": {
                 "id": null,
                 "createTime": null,
                 "updateTime": null,
                 "deleted": null,
                 "weight": null,
                 "version": null,
                 "tenantId": xxxxx,
                 "json": null,
                 "appointmentStatus": null,
                 "appointmentType": null,
                 "appointmentDescribe": null,
                 "email": null,
                 "healthStatus": null,
                 "name": null,
                 "parkId": null,
                 "userId": 72,
                 "appointmentDate": null,
                 "startTime": null,
                 "endTime": null,
                 "startTimeLong": null,
                 "endTimeLong": null
             },
             "orderBy": "t.create_time desc",
             "pageNumber": 1,
             "pageSize": 10,
             "totalPage": 3,
             "totalRow": 24,
             "lastPage": false,
             "firstPage": true
         },
         "map": {}
     }
  
 
 ####  查询单条预约信息详情    
 
 说明:
        
     通过此接口,查询单条预约信息详情
    
 请求地址:
 
     /third/1.0/appointment/findById.json
 
 请求方式:
 
    POST 
 请求参数:
 
 参数名|   类型| 是否必填|   说明
  ---|   ---|    ---|    ---
  id|Long|是|预约id
  userDid|String|是|用户did
  
      
 请求示例:
 
     tenantId:xxx
     timestamp:1591769448373
     sign:0dc395c6f75458d0254c29a1b35a0axxxxxxec68bd7d8426c642a034217210f1
     userDid:ihsRJhxxxxx1BS4ASipFqB
     id:166
      
      
 响应参数:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "id": 166,
            "createTime": 1591761294000,
            "updateTime": 1591761305000,
            "deleted": false,
            "weight": null,
            "version": null,
            "tenantId": xxx,
            "json": "{\"timeZone\":\"GMT+0800\"}",
            "appointmentStatus": 7,
            "appointmentType": 3,
            "appointmentDescribe": "1",
            "email": "xyyz1207@163.com",
            "healthStatus": null,
            "name": "11",
            "parkId": 46,
            "userId": 72,
            "appointmentDate": 1591844460000,
            "startTime": null,
            "endTime": null,
            "startTimeLong": null,
            "endTimeLong": null
        },
        "map": {}
    }
 
 
 ####  根据预约编号取消预约(当预约还没有审核时方可取消)   
  
  说明:
         
      通过此接口,您可以取消未审核的预约
     
  请求地址:
  
      /third/1.0/appointment/cancle.do
  
  请求方式:
  
     POST 
  请求参数:
  
  参数名|   类型| 是否必填|   说明
   ---|   ---|    ---|    ---
   id|Long|是|预约id
   userDid|String|是|用户did
   
       
  请求示例:
  
      tenantId:xxx
      timestamp:1591771010411
      sign:326534b3143c992e7xxxxxxxxccebe9590a0aa8d685ed6
      userDid:ihsRJhHzUxxxxxxxxxxx1BS4ASipFqB
      id:167
       
       
  响应参数:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }
 
 
####  访客列表  
  
  说明:
         
      通过此接口,您可以获取访客列表从而对访客的预约进行处理
     
  请求地址:
  
      /third/1.0/appointment/visitorPage.json
  
  请求方式:
  
     POST 
  请求参数:
  
  参数名|   类型| 是否必填|   说明
   ---|   ---|    ---|    ---
   pageNum|Long|是|页码
   pageSize|Long|是|每页条数
   parkId|String|是|组织(公司)id
   userDid|String|是|用户did
   
       
  请求示例:
  
      tenantId:xxxx
      timestamp:1591772889456
      sign:57b4ae16763ccxxxxxxa26604a23558407dccb4766b0c2c1c359b0
      userDid:ihsRJhHzUPxxxxxxs79Dv1BS4ASipFqB
      parkId:56
       
       
  响应参数:
  
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "list": [
                {
                    "id": 96,//预约列表id
                    "createTime": 1586504608000,
                    "updateTime": 1587073667000,
                    "deleted": false,
                    "weight": null,
                    "version": null,
                    "tenantId":xxxxx,
                    "json": null,
                    "appointmentStatus": 4,//预约状态
                    "appointmentType": 3,//预约类型
                    "appointmentDescribe": "Testmeeting",//预约描述
                    "email": null,
                    "healthStatus": "1",//健康状态
                    "name": "Weidong",//名称
                    "parkId": 56,//组织id
                    "userId": 313,//用户id
                    "appointmentDate": 1586504580000,
                    "startTime": null,
                    "endTime": null,
                    "startTimeLong": null,
                    "endTimeLong": null,
                    "location": "No.2966 Jinke Road ,Pudong",//位置
                    "parkName": "Starrymedia2",//预约组织名称
                    "coordinates": null,
                    "selected": true,
                    "isNotify": false
                }
            ],
            "paras": {
                "id": null,
                "createTime": null,
                "updateTime": null,
                "deleted": null,
                "weight": null,
                "version": null,
                "tenantId": xxxxxx,
                "json": null,
                "appointmentStatus": null,
                "appointmentType": null,
                "appointmentDescribe": null,
                "email": null,
                "healthStatus": null,
                "name": null,
                "parkId": 56,
                "userId": null,
                "appointmentDate": null,
                "startTime": null,
                "endTime": null,
                "startTimeLong": null,
                "endTimeLong": null
            },
            "orderBy": "t.create_time desc",
            "pageNumber": 1,
            "pageSize": 10,
            "totalPage": 1,
            "totalRow": 1,
            "lastPage": true,
            "firstPage": true
        },
        "map": {}
    }
  
 
####  预约审核(通过或者拒绝预约)   
  
  说明:
         
      通过此接口,您可以对来访的预约事件进行审核
     
  请求地址:
  
      /third/1.0/appointment/examine.do
  
  请求方式:
  
     POST 
  请求参数:
  
  参数名|   类型| 是否必填|   说明
   ---|   ---|    ---|    ---
   appointmentId|Long|是|预约表id
   examine|Boolean|是|审核结果
   userDid|String|是|用户did
   
       
  请求示例:
  
      tenantId:xxxx
      timestamp:1591771562423
      sign:4dba7fb0416bb9e19f2ccc6158dbb6af684c139224907c3a65d1da81518287ed
      userDid:ihsRJhHzUPnnxxxxxv1BS4ASipFqB
      appointmentId:168
      examine:1
       
       
  响应参数:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": true,
        "map": {}
    }
    
    
    
####  获取指定月份的日程信息  
  
  说明:
         
      通过此接口,您可以获取指定月份下的所有日程信息
     
  请求地址:
  
      /third/1.0/appointment/myscheduleinfo.json
  
  请求方式:
  
     POST 
  请求参数:
  
  参数名|   类型| 是否必填|   说明
   ---|   ---|    ---|    ---
   time|String|是|日期
   userDid|String|是|用户did
   
       
  请求示例:
  
      tenantId:xxxx
      timestamp:1591773828477
      sign:69c49c871e553d03571358b911bd21006d8adbf1265dd6038dc3d211f299347e
      userDid:ihsRJhHzUxxxxxs79Dv1BS4ASipFqB
      time:2020-06

       
       
  响应参数:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": [
            {
                "id": null,
                "createTime": null,
                "updateTime": null,
                "deleted": false,
                "weight": null,
                "version": null,
                "tenantId":xxxxxx,
                "json": null,
                "userId": null,
                "name": null,
                "purpose": "111",//预约目的
                "addressName": null,
                "location": null,
                "time": 1591844460000,
                "appointmentStatus": 2,//预约状态
                "appointmentType": 1,//预约类型
                "conditionType": null,
                "parkIdsSet": null,
                "type": "1",//当tpye为1是指的是别人来拜访我 2时我要去拜访别人
                "conditionTime": null
            }
        ],
        "map": {}
    }
 
 
 ####  获取指定一天的日程信息  
   
   说明:
          
       通过此接口,您可以获取指定某一天的日程信息
      
   请求地址:
   
       /third/1.0/appointment/myschedule.json
   
   请求方式:
   
      POST 
   请求参数:
   
   参数名|   类型| 是否必填|   说明
    ---|   ---|    ---|    ---
    pageNum|Long|是|页码
    pageSize|Long|是|每页条数
    time|String|是|日期
    userDid|String|是|用户did
    
        
   请求示例:
   
       tenantId:xxxx
       timestamp:1591773828477
       sign:69c49c871e553d03571358b911bd21006d8adbf1265dd6038dc3d211f299347e
       userDid:ihsRJhHzUxxxxxs79Dv1BS4ASipFqB
       pageNum:1
       pageSize:10
       time:2020-06-11
 
        
        
   响应参数:
    
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "list": [{
                "id": null,
                "createTime": null,
                "updateTime": null,
                "deleted": false,
                "weight": null,
                "version": null,
                "tenantId": xxxx,
                "json": null,
                "userId": null,
                "name": "zhangsan",
                "purpose": "111",
                "addressName": "Lizard Design Studio",
                "location": "Zuchongzhi Road, Pudong",
                "time": 1591844460000,
                "appointmentStatus": 2,
                "appointmentType": 1,
                "conditionType": null,
                "parkIdsSet": null,
                "type": "1",//访问类型
                "conditionTime": null
            }, {
                "id": null,
                "createTime": null,
                "updateTime": null,
                "deleted": false,
                "weight": null,
                "version": null,
                "tenantId": xxxx,
                "json": null,
                "userId": null,
                "name": "myself",
                "purpose": "111",
                "addressName": "Lizard Design Studio",
                "location": "Zuchongzhi Road, Pudong",
                "time": 1591844460000,
                "appointmentStatus": 2,
                "appointmentType": 1,
                "conditionType": null,
                "parkIdsSet": null,
                "type": "2",//日程类型
                "conditionTime": null
            }],
            "paras": {
                ...
            },
            "orderBy": "t.time",
            "pageNumber": 1,
            "pageSize": 10,
            "totalPage": 1,
            "totalRow": 2,
            "lastPage": true,
            "firstPage": true
        },
        "map": {}
    }
    
    
 ###  群组功能
 ####   创建群
 说明:
        
        通过此接口,您可以创建属于自己的群组
 请求地址:
 
    /third/1.0/group/create
 请求方式:
 
    POST 
 请求参数:
 
 参数名|   类型| 是否必填|   说明
     ---|   ---|    ---|    ---
     name|String|是|群组名称
     picture|String|是|群组图片地址
     notice|String|否|群公告
     status|Integer|是|是否公开该群(0不公开,1公开)
     applyType|Integer|是|审核类型(0不审核,1审核)
     userDid|String|是|用户did
     
 请求示例:
 响应结果:
 
    {
      "code": 0,
      "data": {},
      "map": {},
      "msg": "string",
      "success": true
    }
 
 
  ####   编辑群
  说明:
         
         通过此接口,您可以编辑自己创建的群组
  请求地址:
  
     /third/1.0/group/edit
  请求方式:
  
     POST 
  请求参数:
  
  参数名|   类型| 是否必填|   说明
      ---|   ---|    ---|    ---
      number|String|是|群编号
      name|String|是|群组名称
      picture|String|是|群组图片地址
      notice|String|否|群公告
      status|Integer|是|是否公开该群(0不公开,1公开)
      applyType|Integer|是|审核类型(0不审核,1审核)
      userDid|String|是|用户did
      
  请求示例:
  响应结果:
 
    {
      "code": 0,
      "data": {},
      "map": {},
      "msg": "string",
      "success": true
    }
 
 
 ####   根据群id获取群信息
   说明:
          
          通过此接口,您可以获取单个群信息从而对群信息进行编辑
   请求地址:
   
      /third/1.0/group/info
   请求方式:
   
      GET 
   请求参数:
   
   参数名|   类型| 是否必填|   说明
       ---|   ---|    ---|    ---
       groupId|String|是|群组id
       userDid|String|是|用户did
       
   请求示例:
   
    tenantId:xxx
    timestamp:1591843116577
    sign:59d2ce391aa8da9110b635cfe080a8bda7895dbb192116eb128563e09460f3b5
    userDid:ihsRJhHzUPnnFxxxxxxxxS4ASipFqB
    groupId:15
   响应结果:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "id": 15,
            "createTime": 1591342769000,
            "updateTime": 1591351344000,
            "deleted": false,
            "weight": null,
            "version": null,
            "tenantId": xxxxxxx,
            "json": null,
            "applyType": 1,
            "status": 1,
            "leaderDid": "ihsRJhHzUPnxxxxxxBS4ASipFqB",//群组did
            "leaderUserId": 72,//群组用户id
            "name": "鲜鲜创建的",//群名
            "notice": "1s",//群口号
            "number": "345244564",//群号
            "picture": "{url}",//群图片
            "totalScore": 0.00//群积分
        },
        "map": {}
    }
    
    
####   获取群列表
   说明:
          
          通过此接口,您可以获取群列表
   请求地址:
   
      /third/1.0/group/index
   请求方式:
   
      POST 
   请求参数:
   
   参数名|   类型| 是否必填|   说明
       ---|   ---|    ---|    ---
       isAll|Integer|否|是否显示所有群组(0显示全部1显示自己创建的群组)
       pageNum|Integer|是|页码
       pageSize|Integer|是|每页条数
       lastCheckTime|String|是|最后查看时间
       userDid|String|是|用户did
       
   请求示例:
   
    tenantId:xxxxx
    timestamp:1591844245602
    sign:6c02b3ac3e5a86aa1517709bf50b308160b995a4ea7f1422fd35e3e1b57399ae
    userDid:ihsRJhHzUPxxxxxxv1BS4ASipFqB
    isAll:0
    pageNum:1
    pageSize:10
    lastCheckTime:0
   响应结果:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "list": [
                {
                    "groupNumber": "345244564",//群编号
                    "groupName": "鲜鲜创建的",//群名称
                    "groupPicture": "{url}",
                    "groupNotice": "1s",//群口号
                    "groupLeaderDid": "ihsRJhHzUPxxxxxv1BS4ASipFqB",//群主did
                    "groupLeaderUid": 72,//群组用户Id
                    "groupScore": 0,//群积分
                    "groupHealthStatus": 1,//群健康状态
                    "groupHealthValue": 100.0000,//群健康度
                    "rank": 1,//排名
                    "totalGroup": 9,
                    "normalMember": 2,
                    "abnormalMember": 0,
                    "undetectedMember": 0,
                    "applySum": 0,
                    "status": 1,//状态
                    "normalHealthTime": 54900,//健康保持时间
                    "undetectedHealthTime": 0,
                    "memberList": null,
                    "isBelong": 1
                }
            ],
            "paras": null,
            "orderBy": null,
            "pageNumber": 1,
            "pageSize": 10,
            "totalPage": 1,
            "totalRow": 1,
            "lastPage": true,
            "firstPage": true
        },
        "map": {}
    }
    
    
    
####   获取群成员信息
   说明:
          
          通过此接口,您可以获取群成员信息
   请求地址:
   
      /third/1.0/group/detail
   请求方式:
   
      GET
   请求参数:
   
   参数名|   类型| 是否必填|   说明
       ---|   ---|    ---|    ---
       groupNumber|String|是|群number
       pageNum| Integer|是|页码
       pageSize|Integer|是|每页个数
       userDid|String|是|用户did
       
   请求示例:
   
    timestamp:1591845427619
    sign:06388a91f1780e59102c6466a17c2dddd63df137731e34a8a451b617d4e28a77
    userDid:ihsRJhHxxxxxx9Dv1BS4ASipFqB
    groupNumber:26xxxxx558
    tenantId:xxxx
    pageNum:1
    pageSize:10
   响应结果:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "list": [
                {
                    "did": "ihsRJhHzUPnxxxxxxDv1BS4ASipFqB",
                    "uid": 72,
                    "userName": "哈哈哈哈哈哈",
                    "userPhoto": "{url}",
                    "healthStatus": 1,
                    "pushTimeStamp": 1591788480000,
                    "reminded": 0
                },
                {
                    "did": "ijtWuxxxxxxxgkiyND2eQ1Ydr",
                    "uid": 313,
                    "userName": "xyyz1207_a",
                    "userPhoto": null,
                    "healthStatus": 1,
                    "pushTimeStamp": 1591788480000,
                    "reminded": 0
                },
                {
                    "did": "imothunWhxxxxGZKthJc9d6uS",
                    "uid": 330,
                    "userName": "q",
                    "userPhoto": null,
                    "healthStatus": 3,
                    "pushTimeStamp": 1591351499000,
                    "reminded": 0
                },
                {
                    "did": "iYebypjpJtHxxxxxxxReZEbCYqqXa",
                    "uid": 333,
                    "userName": "周鲜鲜新号",
                    "userPhoto": null,
                    "healthStatus": 3,
                    "pushTimeStamp": null,
                    "reminded": 0
                }
            ],
            "paras": null,
            "orderBy": null,
            "pageNumber": 1,
            "pageSize": 10,
            "totalPage": 1,
            "totalRow": 1,
            "lastPage": true,
            "firstPage": true
        },
        "map": {}
    }
    
    
####   获取入群审核列表
   说明:
          
          通过此接口,您可以拉去申请入群的列表
   请求地址:
   
      /third/1.0/group/list
   请求方式:
   
      GET 
   请求参数:
   
   参数名|   类型| 是否必填|   说明
       ---|   ---|    ---|    ---
       groupNumber|String|是|群number
       pageNum| Integer|是|页码
       pageSize|Integer|是|每页个数
       userDid|String|是|用户did
       
   请求示例:
   
    timestamp:1591845427619
    sign:06388a91f1780e59102c6466a17c2dddd63df137731e34a8a451b617d4e28a77
    userDid:ihsRJhHxxxxxx9Dv1BS4ASipFqB
    groupNumber:26xxxxx558
    tenantId:xxxx
    pageNum:1
    pageSize:10
   响应结果:
 
    {
      "code": 0,
      "data": {
        "firstPage": true,
        "lastPage": true,
        "list": [
          {
            "createTime": "2020-06-11T02:03:29.174Z",
            "deleted": false,
            "groupNumber": "string",//群编号
            "healthStatus": 0,//申请者健康状态(0健康1体温高2未检测)
            "id": 0,
            "inviterDid": "string",//邀请者did
            "inviterType": 0,//邀请类型
            "json": "string",
            "memberDid": "string",//成员did
            "nickName": "string",//别名
            "photo": "string",
            "remark": "string",//备注
            "status": 0,
            "tenantId": 0,
            "updateTime": "2020-06-11T02:03:29.174Z",
            "version": 0,
            "weight": 0
          }
        ],
        "orderBy": "string",
        "pageNumber": 0,
        "pageSize": 0,
        "paras": {},
        "totalPage": 0,
        "totalRow": 0
      },
      "map": {},
      "msg": "string",
      "success": true
    }
    
    
####   入群审核
   说明:
          
          通过此接口,您可以对入群的申请进行审批
   请求地址:
   
      /third/1.0/group/apply
   请求方式:
   
      GET 
   请求参数:
   
   参数名|   类型| 是否必填|   说明
       ---|   ---|    ---|    ---
       groupNumber|String|是|群number
       inviteLogId| Long|是|页码
       status|Integer|是|审核状态（0：不同意，1：同意）
       userDid|String|是|用户did
       
   请求示例:
   响应结果:
 
    {
      "code": 0,
      "data": {},
      "map": {},
      "msg": "string",
      "success": true
    }
    
 
 
