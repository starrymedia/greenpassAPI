# GreenPassAPI
GreenPass Open API Documentation

 [TOC] 

 ### Overview
 
Welcome to use GreenPass Open API! You can use APIs to create Elastos DID, manage your own health data, such as body temperature, test result, location, or more typrs of health related data), and obtain your overall health status QR code.

 ### Terms

Name | Description
---  |  ---
DID	| If there is no special note, the DID refers to Elastos DID by default.

 #### General note

 ##### Domain name
 
Test domain name: https://greenpass.starrymedia.com:4433

Production domain name:


 ### Access note

 #### Preparation:
First, you need to apply for the authorization code and tenant ID for the test environment. With the authorization code, you can start to use your API service, and test based on documents. Once all tests are passed in test environment, you can switch the address to our production environment, and use the authorization code to access to API service in production environment. 

 #### Note
Method to apply for authorization code:

Please send request to this email address: contact@mygreenpass.life

Attention:
Authorization code is the only ID for your connect to GreenPass service. Please keep in the safe place. If GreenPass detects the illegal access from your ID, GreenPass will suspend your access, and reserve the rights for further actions.

If you already have the authorization code, you can try to access GreenPass service now. We recommend you use PostMan API Client as debug tool. Here is the download address: https://www.postman.com/product/api-client/

Let's get started!

#### Error codes

Error code   |	Description
---  |  ---
10006 | Network busy
10007 | Parameter error
10005 | Request time out
10008 | Signature mismatch
10002 | System parameter error
10001 | Parameter error
20001 | Not authorized
20005 | User not exist
20009 | Authorization error
20005 | User not exist

 ##### Parameters:
A request includes both public parameters and specific service parameters. The public paramters are required. The service parameters depend on the details service interface. 

Public request parameters:

Name | Type | Description
---|---|---
timestamp|	string|	time stamp
sign|	string	| signature
tenantId|	int	| tenant id

Note:

 ##### Signature generation:
 
The service parameters are listed in the certain sorting order rule, and then combine with the authorization code and be encrypted via AES encryption to generate the digital signature.

Sorting order rule:

Sort strings by ASCII code from small to large

    public   String sign(Map<String, String> map, String secret) {

     String result = "";
    try {
        List<Map.Entry<String, String>> infoIds = new ArrayList<Map.Entry<String, String>>(map.entrySet());
        //sort the input parameters by ASCII code, small to large  (by disctionary)
        infoIds.sort(new Comparator<Map.Entry<String, String>>() {
            public int compare(Map.Entry<String, String> o1, Map.Entry<String, String> o2) {
                return (o1.getKey()).compareTo(o2.getKey());
            }
        });
        // construct signature key format
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
        //delete the end &
        sb.deleteCharAt(sb.length() - 1);

        //signature
        result = HMACSHA256.sha256_HMAC(sb.toString(), secret);

    } catch (Exception e) {
        return null;
    }
    return result;
    }
    
 ##### request parameters example  
    Map<String, String> param = new HashMap<>();
    param.put("timestamp", String.valueOf(System.currentTimeMillis()));
    param.put("tenantId", tenantId);
    param.put("userDid", did);
    param.put("pageNum", String.valueOf(page));
    param.put("pageSize", String.valueOf(size));
    param.put("sign", sign(param, secret));

 
 #### Return format
 ##### No return data
 Demo code:

    {
    "code":10006,
    "map":{},
    "msg":"",
    "success":false
    }

 ##### With return data
 Demo code:

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

 #### Parameter description: 
 


NAme | Type | Description
---|---|---
code|	Int	| return status code
success| 	Boolean	| return result
msg	| String	| return message
map	| JSONObject|	reserved
data|	Object|	response result



 #### Create your DID

Note:
All data connected to GreenPass needs a DID as a unique identifier for storage or retrieval. Through this interface you will get your did


Request Address:
	/third/1.0/user/thirdAppCreate.do
	
Request Method:
	POST
	
Request Parameters:
	
	
Name |	Type |	Required  |	Description
 ---  |  --- |  --- |  ---
userId | 	string | Yes  | User id	from Third-place application 


Request Example:
	

Response parameters:
	
    {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":"iXgvjQp5DZZMnqXXXXXXXXXXXXXXXXXXXX",
    "map":{}
    }



 #### Save health information

Note:
	Get user information
	
Request address:

	/third/1.0/user/userInfo.json
	
Request method:
		POST
		
Request parameters:
	
	
Name |	Type |	Required  |	Description
 ---  |  --- |  --- |  ---
userDid | 	String |  Yes  | User id


Request example:
	

Response parameters:

    {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":{
        "uid":3XX,//user Id
        "account":"x_xxxx",//user account 
        "nickName":null,//nick_name
        "gender":null,//gender
        "email":null,//email 
        "idCard":null,
        "json":null,
        "name":null,//name
        "profilePhoto":null,
        "totalScore":20,
        "status":"enabled",//status
        "temperatureType":null,temperature type
        "parkIds":null,
        "parkId":null,
        "did":"iXgvjQp5DZZMnqXXXXXXXXXXXXXXXXXXX"//did
    },
    "map":{}
    }





 #### Save health information

Note:
	Record health information
	
Request address:

	/third/1.0/health/save.do
	
Request method:
		POST
		
Request parameters:
	
Name |	Type |	Required  |	Description
 ---  |  --- |  --- |  ---
type | 	Int	 | Yes | Type (1: temperature check 2: reagent detection 3: position check 4: other check)
detail | 	String | Yes | 	Detailed content
userDid	 | String | Yes | 	User DID
temperatureType(Optional) | 	String | No | 	Temperature type (C: Celsius F: Fahrenheit)
authenticatorType | 	Int | Yes | 	Data submission type
evidenceImage(Optional)	 | String	 | No | Picture
location(Optional) | 	String | No | Location
address(Optional) | 	String |  No | 	Location
mapType(Optional) | 	Int	 |  No | Map type
latitude(Optional) | 	BigDecimal	 | No | latitude
longitude(Optional) | 	BigDecimal | No| longitude

Note:
When submitting body temperature, the temperature is required. When submitting reagent testing, the proof image is required. When submitting location , the map type、latitude and longitude are required.
	

Request example:

Response parameters:

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
        "tenantId":xxxxxxx,//tenant id
        "json":null,
        "authenticatorType":1,//
        "mapType":null,
        "status":1,//health status
        "type":1,//type
        "type1":null,
        "authenticator":"iXgvjQp5DZZMnqXXXXXXXXXXXXXXXXXXXX",//witness or subimtter did
        "coordinates":null,
        "detail":"36.6",//details
        "evidenceImage":null,//image to inprove
        "didHash":null,
        "hash":null,
        "eidHash":null,
        "latitude":null,
        "location":null,//location
        "address":null,//adderess
        "longitude":null,
        "userId":3XX,//user Id
        "remarks":null,//note
        "parkName":null//organization name
    },
    "map":{}
    }



 #### Get my health history

Note:

Get my health history
	
Request address:

	/third/1.0/health/page.json
	
Request method:	POST
	
Request parameters:
	
	
Name |	Type |	Required  |	Description
 ---  |  --- |  --- |  ---
pageNum |	Long | Yes | Page Number
pageSize |	Long |No | The items number per page, default is 10
userDid |	String |Yes | User's did	

Request example:

Response parameters:

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



 #### Get my health history

Note:

	Get my health history
	
Request address:

	/third/1.0/health/trail.json
	
Request method:
	GET
	
Request parameters:
Name |	Type |	Required  |	Description
 ---  |  --- |  --- |  ---
userDid	 | String |Yes  | 	User did

Request example:


Response parameters:

    {
    "code":0,
    "success":true,
    "msg":"Succeed",
    "data":[
        {
            "lng":"3x.2xxxx91016",//longitude
            "lat":"1xx.5xxxx42944"//latitude
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
     



  #### Get my last health record
 
 Note:
	Get my last health record
	
Request address:

	/third/1.0/health/findByNowDay.json
	
 Request method:
	POST
	
Request parameters:
Name |	Type |	Required  |	Description
 ---  |  --- |  --- |  ---
userDid	|String	|Yes| User did

Request example:


Response parameters:

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
        "authenticatorType":1,//type
        "mapType":1,//map type
        "status":1,//status
        "type":1,//type
        "type1":null,
        "authenticator":null,
        "coordinates":"",
        "detail":"36.6",//temperature
        "evidenceImage":"",
        "didHash":null,
        "hash":null,
        "eidHash":null,
        "latitude":null,
        "location":"",
        "address":"",
        "longitude":null,
        "userId":XX,//user Id
        "remarks":null,//note
        "parkName":null//location name
    },
    "map":{}
    }
    
 #### Scan the location code to save the location record
  
 Note:
 
    Scan the the location QR code  to save the user's location record at the location
    
 Request address:
 
    /third/1.0/verify/accessLog.json
 	
 Request method:
 
    POST 
 	
 Request parameters:
 
 Name |	Type |	Required  |	Description
 ---|   ---|    ---|    ---
tenantId|integer|Yes|Tenant id
userDid|String|Yes|User did
location|String|Yes|location
coordinates|String|Yes|coordinates
parkId|Long|Yes|location id
locationName|String|Yes|location name
healthStatus|Integer|Yes|Health status((1."Healthy"), (2."Unhealthy"), (3."Not tested yet");)
mapType|Integer|Yes|Map type (1.Baidu map;2.Google map;3.Gaode map;4.Tencent map)
type|Integer|Yes|type (1.temperature 2.reagent detection 3.location  4.others)
detail|String|Yes|details (Temperature, negative or positive, area)
 
 Request example:
 
 
 Response parameters:
 
    {
     "code": 0,
     "success": true,
     "msg": "Succeed",
     "data": null,
     "map": {}
    }
 
 
 #### Scan to access
   
  Note:
  
     Scan the user's QR code or reservation code to save the access record.
     
  Request address:
  
     /third/1.0/verify/save.do
  	
  Request method:
  
     POST 
  	
  Request parameters:
  
  Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
userDid|String|Yes|Scan code to verify user did
did|String|No|user ID
appointmentId|Long|No|Reservation id for web reservation
tenantId|integer|Yes|Tenant id
location|String|No|location
coordinates|String|No|coordinates
mapType|Integer|No|Map type (1.Baidu map;2.Google map;3.Gaode map;4.Tencent map)
isThird|Boolean|No|YesNoSubmit for others

  
  Request example:
  
  
  Response parameters:
  
      {
          "code": 0,
          "success": true,
          "msg": "Succeed",
          "data": {
              "msg": "Test Negative_No temperature in last 24 hours",//conclusion
              "verifierType": 4,//type
              "tenantName": "Demo",//tenant name 
              "healthStatus": 3,//health status
              "createTime": 1591597122713,
              "companyName": "Alex club",
              "tenantId": xxxxxxx,//tenant id
              "name": "q",//name
              "did": "imothunWhDWxxxxxxxxxx4GZKthJc9d6uS"//did
          },
          "map": {}
      }
  
    
 #### Access record
       
  Note:
  
     Get the history of user verification
     
  Request address:
  
     /third/1.0/verify/page.json
    
  Request method:
  
     POST 
    
  Request parameters:
  
  Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
userDid|String|Yes|Scan code to verify user did
pageNum|Long|Yes|Current page number
pageSize|Long|Yes|Number
startTimeLong|Long|No|Start-time stamp
endTimeLong|Long|No|End-time stamp

  
  Request example:
  
  
  Response parameters:
  
      {
          "code": 0,
          "success": true,
          "msg": "Succeed",
          "data": {
              "list": [
                  {
                      "id": 324,//id
                      "createTime": 1591597122000,//time of creation
                      "updateTime": null,//update time
                      "deleted": false,//delete
                      "weight": null,
                      "version": null,
                      "tenantId":xxxxxxx,//tenant id
                      "json": null,
                      "healthStatus": 3,//health status 
                      "mapType": null,//map type
                      "appointmentId": null,//appoinment id
                      "parkId": null,//organization id
                      "authenticatorId": 72,//Validator id
                      "coordinates": null,//coordinates
                      "describe": "Alex club",//description
                      "healthLogId": null,//health log id
                      "location": null,//location 
                      "verifierId": 330,//verified Id
                      "verifierType": "4",//verified type
                      "verifyDate": 1591597123000,
                      "authIds": null,
                      "startTime": null,
                      "endTime": null,
                      "startTimeLong": null,
                      "endTimeLong": null,
                      "parkIdsSet": null,
                      "authenticatorName": "barney",//Validator name
                      "verifierName": "q",//Verified name
                      "healthLogType": null,
                      "healthLogStatus": null,
                      "healthLogDetail": null,
                      "appointmentLocation": null,
                      "parkName": null,
                      "appointmentDescribe": null,
                      "tenantName": "Demo",//tenant name 
                      "pname": "bubbling-well shop"//loaction name
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
                      "verifierName": "barney",
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
  
  
  ### Organization related   
#### Create Location (Organization)
Note:
  
     Create a new location or organization
     
  Request address:
  
    /third/1.0/park/create.do
    
  Request method:
  
     POST 
    
  Request parameters:
  
  Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
userDid|String|Yes|user did
name|String|Yes|Name
location|String|Yes|Location
mapType|Integer|No|Map type (map type. 1: Baidu map; 2: Google map; 3: Gaode map; 4: Tencent map)
coordinates|String|Yes|Coordinates
receptionists|String|No|did:["did","did","did"]
logo|String|No|address of location logo 
photo|String|No|address of location picture 
info|String|No|Organization Introduction
isPublic|integer|Yes|YesNo public location
isPublicLocation|integer|Yes|YesNo shows detailed location

  
  Request example:
  Response parameters:
        
        {
            "code": 0,
            "success": true,
            "msg": "Succeed",
            "data": null,
            "map": {}
        }
    
        
####  Edit location (organization)
Note:

    Through this interface, you can edit and modify the organization or location you created

Request address:

     /third/1.0/park/edit.do

Request method:

    POST
        
Request parameters:

  Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
userDid|String|Yes|user did
id|Long|Yes|Organization id
name|String|Yes|Organization name
isPublic|integer|Yes|YesNo public location
isPublicLocation|integer|Yes|YesNo shows detailed location
location|String|Yes|Organization location
mapType|integer|Yes|Map type
coordinates|String|Yes|Coordinates
receptionists|String|No|did:["did","did","did"]
  
Request example:
Response parameters: 

    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }

####  Delete location (organization)
Note:

    Through this interface, you can delete the organization or place you created, all information related to this place will be unavailable

Request address:

     POST /third/1.0/park/del.do

Request method:

    POST
        
Request parameters:

  Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
  userDid|String|Yes|User did
  parkId|Long|Yes|Organization id
  
Request example:
Response parameters: 

    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }

     
  ####  Query details of organization information
  Note:
  
      Through this interface, you can query the information of the organization or location you created
  
  Request address:
  
       /third/1.0/park/findById.json
  
  Request method:
  
      POST
          
  Request parameters:
  
    Name |	Type |	Required  |	Description
    ---|   ---|    ---|    ---
    userDid|String|Yes|User did
    id|Long|Yes|Organization id
    
  Request example:
  Response parameters: 
  
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
              "tenantId": xxxxxx,//tenantid
              "json": null,
              "mapType": 2,//map type
              "coordinates": "31.204008122876772,121.599235291026",//coordinates
              "location": "Shanghai",//location
              "name": "Cathy",//name
              "areaCode": "000086",// area code
              "userId": 330,//userId
              "logo": null,//location logo
              "photo": null,//location picture
              "info": null,//information
              "phone": null,//telephone
              "isPublic": 1,//YesNo public location
              "isPublicLocation": 1,//YesNo detailed location
              "receptionists": null,//receptionists
              "isAdmin": 0,//
              "haveAppointment": false,
              "role": null,
              "provinceId": null,
              "province": null,
              "city": null
          },
          "map": {}
      }  
        
        
  ####  Get list of organization administrators
Note:

    Through this interface, you can get the list of administrators of the specified organization

Request address:

    /third/1.0/park/page.json

Request method:

    POST
        
Request parameters:

  Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
  userDid|String|Yes|User did
  pageNum|Long|Yes|Page number 
  pageSize|Long|Yes|Items number
  
  
Request example:
Response parameters: 

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
                    "mapType": 2,/map type
                    "coordinates": "31.204008122876772,121.599235291026",//coordinates
                    "location": "Shanghai",//location
                    "name": "Cathy",//name
                    "areaCode": "000086",//area code
                    "userId": 330,//userID
                    "logo": null,
                    "photo": null,
                    "info": null,
                    "phone": null,
                    "isPublic": 1,//YesNopubliclocation
                    "isPublicLocation": 1,//YesNo show detailed location
                    "receptionists": null,
                    "isAdmin": 1,//YesNoYes administrator
                    "haveAppointment": false,
                    "role": [
                        3//role
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
                    "location": "Shanghaijinke road 11",
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
                            "uid": 330,//receptionistsyonhuid
                            "account": "zsxxxxxxn@163.com",// account
                            "nickName": "q",//nick_name
                            "gender": null,
                            "email": "zsuxxxn@163.com",
                            "idCard": null,
                            "json": null,
                            "name": null,
                            "profilePhoto": null,
                            "totalScore": 82,//points
                            "status": "enabled",// status
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
 
     
     
####  According to the user DID get all the organization-related information under the user
Note:

   Through this interface, you can get the list of administrators of the specified organization

Request address:

       /third/1.0/park/parkInfo.json   

Request method:

    POST
        
Request parameters:

  Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
  userDid|String|Yes|User did

  
Request example:
Response parameters:
 
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
                    "tenantId": xxxxxX,// tenantid
                    "json": null,
                    "mapType": 2,//map type
                    "coordinates": "31.0769472,121.5958393",//coordinates
                    "location": "Shanghai jinke road ",//address
                    "name": "鲜鲜测试location",//name
                    "areaCode": "000086",//area code
                    "userId": 72,//userid
                    "logo": "url",//picture address
                    "photo": null,//picture address
                    "info": null,
                    "phone": null,
                    "isPublic": 0,//YesNopubliclocation
                    "isPublicLocation": 0//YesNo show detailed location
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
                    "location": "Shanghai Jinke Road11",
                    "name": "hhhhhh",
                    "areaCode": "000086",
                    "userId": 330,
                    "logo": "",
                    "photo": "",
                    "info": "test",
                    "phone": null,
                    "isPublic": 1,//YesNopubliclocation
                    "isPublicLocation": 0//YesNo show detailed location
                }
            ],
            "map": {}
        }
           
  
####  Fuzzy query organization
Note:

    Through this interface, you can enter any content for fuzzy query organization

Request address:

       /third/1.0/park/searchName.do

Request method:

    POST
        
Request parameters:

  Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
  userDid|String|Yes|User did
  name|String|Yes|Organization name

  
Request example:
Response parameters:
 
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
                     "areaCode": "000086",//area code
                     "userId": 80,//userid
                     "logo": "url",//logoaddress
                     "photo": null,
                     "info": null,
                     "phone": null,
                     "isPublic": 1,
                     "isPublicLocation": 0
                 }
             ],
             "map": {}
         }     
        
       
####  Verify organization name
Note:

    Through this interface, you can verify that the organization name YesNo already exists

Request address:

     /third/1.0/park/verifyName.do

Request method:

    POST
        
Request parameters:

  Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
  userDid|String|Yes|User did
  name|String|Yes|Organization name

  
Request example:
Response parameters:
 
     {
       "code": 0,
       "data": true,
       "map": {},
       "msg": "string",
       "success": true
     }
    
          
 #### Staff  management
#### Add staffs
Note:
    
    Through this interface, you can add members (staffs) to the organization (location) you created

Request address:

    /third/1.0/employee/add.do

Request method:

    POST 
Request parameters:

Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
userDid|String|Yes|user did
parkId|Long|Yes|Organization id
did|String|Yes|member did to add
lang|String|No|Language parameters
      
Request example:
Response parameters:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }
 
 
#### Edit staff
Note:
    
    Through this interface, you can edit the staffs you added

Request address:

    /third/1.0/employee/edit.do

Request method:

    POST 
Request parameters:

Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
userDid|String|Yes|user did
parkId|Long|Yes|Organization id
id|Long|Yes|member id to be modified
lang|String|No|Language parameters
email|String|No|Mailbox
id|Long|Yes|Employee id
      
Request example:
Response parameters:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }
 
 
#### Delete staff
Note:
    
    Through this interface, you can remove the staffs (members) you added

Request address:

    /third/1.0/employee/delete.do

Request method:

    POST 
Request parameters:

Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
  userDid|String|Yes|User did
  id|Long|Yes|Staff id
      
Request example:
Response parameters:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }
  

#### Query employee information based on staff ID
Note:
    
    Through this interface, you can query the information of the added staffs (members)

Request address:

    /third/1.0/employee/findById.json

Request method:

    POST 
Request parameters:

Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
  userDid|String|Yes|User did
  id|Long|Yes|Staff id
      
Request example:
Response parameters:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "id": 105,//member ID
            "createTime": 1591611094000,
            "updateTime": 1591612132000,
            "deleted": false,
            "weight": null,
            "version": null,
            "tenantId": Xxxxxx,// tenantid
            "json": null,
            "gender": null,//gender
            "companyId": null,
            "employNo": null,
            "did": "iYQnfPtnLn8J4xxxxxxxxP3rs8SZf",//did
            "mobile": null,
            "email": null,
            "name": " David",
            "organizeId": null,
            "parkId": 83,// Organizationid
            "genderSwitch": null,
            "healthStatus": null,
            "isAdmin": null,
            "companyName": null,
            "parkName": "Cathy"// Organizationname
        },
        "map": {}
    }
 


#### According to the list of members under the organization
Note:
    
    Through this interface, you can query all members under the specified organization id

Request address:

    /third/1.0/employee/page.json

Request method:

    POST 
Request parameters:

Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
userDid|String|Yes|user did
parkId|Long|Yes|user did
pageNum|Long|Yes|Current page
pageSize|Long|Yes|Items Number
      
Request example:
Response parameters:
 
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
                    "tenantId": xxxxxxx,// tenantid
                    "json": null,
                    "gender": null,
                    "companyId": null,
                    "employNo": null,
                    "did": "iYQnfPtnLn8J4kxxxxxxxxFUP3rs8SZf",//did
                    "mobile": null,
                    "email": null,
                    "name": " David",
                    "organizeId": null,
                    "parkId": 83,// Organizationid
                    "genderSwitch": null,
                    "healthStatus": 3,// health status
                    "isAdmin": 0//YesNoYes administrator
                }
            ],
            "paras": {//
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
 
 
 ####  Get Staff permissions
 Note:
     
     Through this interface, you can obtain the permissions of staffs, so that you can operate the permissions of employees
 
 Request address:
 
     /third/1.0/employee/role.json
 
 Request method:
 
     POST 
 Request parameters:
 
 Name |	Type |	Required  |	Description
   ---|   ---|    ---|    ---
   userDid|String|Yes|User did
   did|String|Yes|Staff did
   parkId|Long|Yes|Organization id
       
 Request example:
 Response parameters:
 
     {
         "code": 0,
         "success": true,
         "msg": "Succeed",
         "data": {
             "receptionist": 0,//YesNoYesreceptionists(0No,1Yes)
             "root": 0,//YesNoYeslocation creator(0No,1Yes)
             "admin": 0//YesNoYes administrator(0No,1Yes)
         },
         "map": {}
     }
 
 
 #### Add admin 
 
 Note:
      
      Through this interface, you can add the designated staff as an administrator, the employee can get more permissions
  
  Request address:
  
     /third/1.0/employee/admin/add.do
  
  Request method:
  
      POST 
  Request parameters:
  
  Name |	Type |	Required  |	Description
    ---|   ---|    ---|    ---
    userDid|String|Yes|User did
    did|String|Yes|Staff did
    parkId|Long|Yes|Organization id
        
  Request example:
  Response parameters:
  
     {
       "code": 0,
       "data": {},
       "map": {},
       "msg": "string",
       "success": true
     }
 
 
 #### Delete administrator 
  
  Note:
       
       Through this interface, you can remove the administrator rights of designated employees
   
   Request address:
   
      /third/1.0/employee/admin/delete.do
   
   Request method:
   
       POST 
   Request parameters:
   
   Name |	Type |	Required  |	Description
     ---|   ---|    ---|    ---
    userDid|String|Yes|User did
    did|String|Yes|Staff did
    parkId|Long|Yes|Organization id
         
   Request example:
   Response parameters:
 
    {
      "code": 0,
      "data": {},
      "map": {},
      "msg": "string",
      "success": true
    }
 
 
 #### Add receptionist 
   
Note:
        
     Through this interface, you can add the receptionist permission of the designated employee
    
Request address:

    /third/1.0/employee/receptionist/add.do

Request method:

    POST 
Request parameters:

Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
    userDid|String|Yes|User did
    did|String|Yes|Staff did
    parkId|Long|Yes|Organization id

Request example:
Response parameters:

     {
       "code": 0,
       "data": {},
       "map": {},
       "msg": "string",
       "success": true
     }
 
 
 #### Delete receptionist 
    
 Note:
         
      Through this interface, you can remove the receptionist authority of the designated employee
     
 Request address:
 
     /third/1.0/employee/receptionist/delete.do
 
 Request method:
 
     POST 
 Request parameters:
 
 Name |	Type |	Required  |	Description
   ---|   ---|    ---|    ---
userDid|String|Yes|User did
did|String|Yes|Employee did
parkId|Long|Yes|Organization id
       
 Request example:
 Response parameters:
 
      {
        "code": 0,
        "data": {},
        "map": {},
        "msg": "string",
        "success": true
      }
      

#### Proactively cancel the identity of the receptionist
    
 Note:
         
      Through this interface, the receptionist can cancel his identity as a receptionist
     
 Request address:
 
     /third/1.0/employee/receptionist/remove.do
 
 Request method:
 
     POST 
 Request parameters:
 
 Name |	Type |	Required  |	Description
   ---|   ---|    ---|    ---
userDid|String|Yes|User did
did|String|Yes|Employee did
parkId|Long|Yes|Organization id
       
 Request example:
 Response parameters:
 
      {
        "code": 0,
        "data": {},
        "map": {},
        "msg": "string",
        "success": true
      }
 
 
###   Appointment related
####  Save appointment information   
 
 Note:
          
       Through this interface, you can save appointment registration information
      
 Request address:
  
      /third/1.0/appointment/save.do
  
 Request method:
  
      POST 
 Request parameters:
  
 Name |	Type |	Required  |	Description
    ---|   ---|    ---|    ---
    name|String|Yes|who made the appoinment
    email|String|Yes|Contact information
    appointmentDescribe|String|Yes|apoinment 
    appointmentType|String|Yes|The appointment type. 1: health code; 2: appointment code; 3; health appointment code
    appointmentTime|Long|Yes|Date of appointment
    parkId|Long|Yes|The id of the reservation organization
    timeZone|String|Yes|Time zone
    userDid|String|No|The user did, must be Required when the appointment type is 3
    
        
 Request example:
 
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
        
 Response parameters:
        
     {
         "code": 0,
         "success": true,
         "msg": "Succeed",
         "data": true,
         "map": {}
     }
 
 
 ####  My appointment list    

Note:
       
    Through this interface, you can get a list of reservation information that has been reserved
   
Request address:

    /third/1.0/appointment/myAppointmentPage.json

Request method:

   POST 
Request parameters:

Name |	Type |	Required  |	Description
 ---|   ---|    ---|    ---
 pageNum|Long|Yes|Page number
 pageSize|Long|Yes|Items per page
 
     
Request example:

    tenantId:xxxxxx
    timestamp:1591767713330
    sign:c7bcdcaa760b9e9c9cfa529b82c543da3c466435d9b8a3238d3233430c0b5148
    userDid:ihsRJhHzUPXXXXXXX9Dv1BS4ASipFqB
    pageNum:1
    pageSize:10
     
     
Response parameters:
     
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
                     "appointmentStatus": 7,// appointment status EXAMINE(1, " reviewing"),AGREE(2, " accept"),REFUSE(3, " reject"),END(4, " end"),VERIFY(5, " verified"),DISABLED(6, " unavailable"),CANCELLED(7," cancelled"),
                     "appointmentType": 3,// appointment type
                     "appointmentDescribe": "1",// appointment description
                     "email": "xyyz1207@163.com",// email
                     "healthStatus": null,
                     "name": "11",// name
                     "parkId": 46,// appointment OrganizationId
                     "userId": 72,//userid
                     "appointmentDate": 1591844460000,// appointment date
                     "startTime": null,
                     "endTime": null,
                     "startTimeLong": null,
                     "endTimeLong": null,
                     "location": "yongquan road 1013",//address
                     "parkName": "Shenzhen ",// appointment Organizationname
                     "coordinates": "22.51427136920438,114.056792087924",//coordinates
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
                     "appointmentStatus": 4,// appointment statusEXAMINE(1, " reviewing"),AGREE(2, " accept"),REFUSE(3, " reject"),END(4, " end"),VERIFY(5, " verified"),DISABLED(6, " unavailable"),CANCELLED(7," cancelled"),
                     "appointmentType": 3,// appointment type
                     "appointmentDescribe": "111",// description
                     "email": "xyyz1207@163.com",// email
                     "healthStatus": null,
                     "name": "666",//name
                     "parkId": 76,// Organizationid
                     "userId": 72,//userid
                     "appointmentDate": 1589014860000,
                     "startTime": null,
                     "endTime": null,
                     "startTimeLong": null,
                     "endTimeLong": null,
                     "location": "yongquan road 1013",//location
                     "parkName": "CAT Coffe",// Organizationname
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
  
 
 ####  Query the details of single appointment    
 
 Note:
        
     Through this interface, query the details of a single appointment
    
 Request address:
 
     /third/1.0/appointment/findById.json
 
 Request method:
 
    POST 
 Request parameters:
 
 Name |	Type |	Required  |	Description
  ---|   ---|    ---|    ---
id|Long|Yes|appointment id
userDid|String|Yes|user did
  
      
 Request example:
 
     tenantId:xxx
     timestamp:1591769448373
     sign:0dc395c6f75458d0254c29a1b35a0axxxxxxec68bd7d8426c642a034217210f1
     userDid:ihsRJhxxxxx1BS4ASipFqB
     id:166
      
      
 Response parameters:
 
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
 
 
 ####  Cancel the appointment according to the appointment number (can only be cancelled when the appointment has not been reviewed)
  
  Note:
         
      Through this interface, you can cancel unapproved appointments
     
  Request address:
  
      /third/1.0/appointment/cancle.do
  
  Request method:
  
     POST 
  Request parameters:
  
  Name |	Type |	Required  |	Description
   ---|   ---|    ---|    ---
id|Long|Yes|appointment id
userDid|String|Yes|user did
   
       
  Request example:
  
      tenantId:xxx
      timestamp:1591771010411
      sign:326534b3143c992e7xxxxxxxxccebe9590a0aa8d685ed6
      userDid:ihsRJhHzUxxxxxxxxxxx1BS4ASipFqB
      id:167
       
       
  Response parameters:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": null,
        "map": {}
    }
 
 
####  Visitor list 
  
  Note:
         
      Through this interface, you can get the visitor list to process the visitor's appointment
     
  Request address:
  
      /third/1.0/appointment/visitorPage.json
  
  Request method:
  
     POST 
  Request parameters:
  
  Name |	Type |	Required  |	Description
   ---|   ---|    ---|    ---
   pageNum|Long|Yes|page number
   pageSize|Long|Yes|items per page
   parkId|String|Yes|organization (location) id
   userDid|String|Yes|user did
       
  Request example:
  
      tenantId:xxxx
      timestamp:1591772889456
      sign:57b4ae16763ccxxxxxxa26604a23558407dccb4766b0c2c1c359b0
      userDid:ihsRJhHzUPxxxxxxs79Dv1BS4ASipFqB
      parkId:56
       
       
  Response parameters:
  
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "list": [
                {
                    "id": 96,// appointment list id
                    "createTime": 1586504608000,
                    "updateTime": 1587073667000,
                    "deleted": false,
                    "weight": null,
                    "version": null,
                    "tenantId":xxxxx,
                    "json": null,
                    "appointmentStatus": 4,// appointment status
                    "appointmentType": 3,// appointment type
                    "appointmentDescribe": "Testmeeting",// appointment description
                    "email": null,
                    "healthStatus": "1",// health status
                    "name": "Weidong",//name
                    "parkId": 56,// Organizationid
                    "userId": 313,//userid
                    "appointmentDate": 1586504580000,
                    "startTime": null,
                    "endTime": null,
                    "startTimeLong": null,
                    "endTimeLong": null,
                    "location": "No.2966 Jinke Road ,Pudong",//location
                    "parkName": "Starrymedia2",// appointment Organizationname
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
  
 
####  Appointment review (accept or reject appointment) 
  
  Note:
         
      Through this interface, you can review the visit appointment events
     
  Request address:
  
      /third/1.0/appointment/examine.do
  
  Request method:
  
     POST 
  Request parameters:
  
  Name |	Type |	Required  |	Description
   ---|   ---|    ---|    ---
appointmentId|Long|Yes|reservation id
examine|Boolean|Yes|review results
userDid|String|Yes|user did
   
       
  Request example:
  
      tenantId:xxxx
      timestamp:1591771562423
      sign:4dba7fb0416bb9e19f2ccc6158dbb6af684c139224907c3a65d1da81518287ed
      userDid:ihsRJhHzUPnnxxxxxv1BS4ASipFqB
      appointmentId:168
      examine:1
       
       
  Response parameters:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": true,
        "map": {}
    }
    
    
    
####  Get schedule information for a specified month 
  
  Note:
         
      Through this interface, you can get all the schedule information under the specified month
     
  Request address:
  
      /third/1.0/appointment/myscheduleinfo.json
  
  Request method:
  
     POST 
  Request parameters:
  
  Name |	Type |	Required  |	Description
   ---|   ---|    ---|    ---
   time|String|Yes|Date
   userDid|String|Yes|User did
   
       
  Request example:
  
      tenantId:xxxx
      timestamp:1591773828477
      sign:69c49c871e553d03571358b911bd21006d8adbf1265dd6038dc3d211f299347e
      userDid:ihsRJhHzUxxxxxs79Dv1BS4ASipFqB
      time:2020-06

       
       
  Response parameters:
 
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
                "purpose": "111",// appointment purpose
                "addressName": null,
                "location": null,
                "time": 1591844460000,
                "appointmentStatus": 2,// appointment status
                "appointmentType": 1,// appointment type
                "conditionType": null,
                "parkIdsSet": null,
                "type": "1",//When tpye is 1Yes, Yes means someone else came to visit me 2 when I was going to visit someone
                "conditionTime": null
            }
        ],
        "map": {}
    }
 
 
 ####  Get schedule information of a specific day  
   
   Note:
          
       Through this interface, you can get the schedule information of a specific day
      
   Request address:
   
       /third/1.0/appointment/myschedule.json
   
   Request method:
   
      POST 
   Request parameters:
   
Name |Type |	Required  |Description

---|   ---|    ---|    ---

pageNum|Long|Yes|page number
pageSize|Long|Yes|items per page
time|String|Yes|date
userDid|String|Yes|user did
    
        
   Request example:
   
       tenantId:xxxx
       timestamp:1591773828477
       sign:69c49c871e553d03571358b911bd21006d8adbf1265dd6038dc3d211f299347e
       userDid:ihsRJhHzUxxxxxs79Dv1BS4ASipFqB
       pageNum:1
       pageSize:10
       time:2020-06-11
 
        
        
   Response parameters:
    
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
                "type": "1",//访问 type
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
                "type": "2",// schedule type
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
    
    
 ###  Group function
 ####   Create Group
 Note:
        
    Through this interface, you can create your own group
        
 Request address:
 
    /third/1.0/group/create
 Request method:
 
    POST 
 Request parameters:
 
 Name |	Type |	Required  |	Description
     ---|   ---|    ---|    ---
name|String|Yes|Group name
picture|String|Yes|Address of group picture 
notice|String|No|Group announcement
status|Integer|Yes|YesNo reveals the group (0 not public, 1 public)
applyType|Integer|Yes|Audit type (0 not audit, 1 audit)
userDid|String|Yes|user did
     
 Request example:
 Result Response:
 
    {
      "code": 0,
      "data": {},
      "map": {},
      "msg": "string",
      "success": true
    }
 
 
  ####   Edit Group
  Note:
         
    Through this interface, you can edit the group you created
         
  Request address:
  
     /third/1.0/group/edit
     
  Request method:
  
     POST 
  Request parameters:
  
  Name |	Type |	Required  |	Description
      ---|   ---|    ---|    ---
number|String|Yes|Group number
name|String|Yes|Group name
picture|String|Yes|Address of group picture 
notice|String|No|Group announcement
status|Integer|Yes|YesNo reveals the group (0 not public, 1 public)
applyType|Integer|Yes|Audit type (0 not audit, 1 audit)
userDid|String|Yes|user did
      
  Request example:
  Result Response:
 
    {
      "code": 0,
      "data": {},
      "map": {},
      "msg": "string",
      "success": true
    }
 
 
 ####   Get group information based on group id
   Note:
          
          Through this interface, you can obtain single group information to edit the group information
          
   Request address:
   
      /third/1.0/group/info
      
   Request method:
   
      GET 
   Request parameters:
   
   Name |	Type |	Required  |	Description
       ---|   ---|    ---|    ---
       groupId|String|Yes|group id
       userDid|String|Yes|user did
       
   Request example:
   
    tenantId:xxx
    timestamp:1591843116577
    sign:59d2ce391aa8da9110b635cfe080a8bda7895dbb192116eb128563e09460f3b5
    userDid:ihsRJhHzUPnnFxxxxxxxxS4ASipFqB
    groupId:15
   Result Response:
 
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
            "leaderDid": "ihsRJhHzUPnxxxxxxBS4ASipFqB",// groupdid
            "leaderUserId": 72,// groupuserid
            "name": " ellie's group ",// group name
            "notice": "1s",// group slogan
            "number": "345244564",// group id
            "picture": "{url}",// group picture
            "totalScore": 0.00// group points
        },
        "map": {}
    }
    
    
####   Get group list
   Note:
          
          Through this interface, you can get the group list
          
   Request address:
   
      /third/1.0/group/index
   Request method:
   
      POST 
   Request parameters:
   
   Name |	Type |	Required  |	Description
       ---|   ---|    ---|    ---
       isAll|Integer|No|YesNoShow all groups (0 shows all 1 shows groups created by yourself)
       pageNum|Integer|Yes|page number
       pageSize|Integer|Yes|items per page
       lastCheckTime|String|Yes|last viewed time
       userDid|String|Yes|user did
       
   Request example:
   
    tenantId:xxxxx
    timestamp:1591844245602
    sign:6c02b3ac3e5a86aa1517709bf50b308160b995a4ea7f1422fd35e3e1b57399ae
    userDid:ihsRJhHzUPxxxxxxv1BS4ASipFqB
    isAll:0
    pageNum:1
    pageSize:10
    lastCheckTime:0
   Result Response:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "list": [
                {
                    "groupNumber": "345244564",// group number
                    "groupName": " ellie's group ",// group name
                    "groupPicture": "{url}",
                    "groupNotice": "1s",// group slogan
                    "groupLeaderDid": "ihsRJhHzUPxxxxxv1BS4ASipFqB",// group creator did
                    "groupLeaderUid": 72,// groupuserId
                    "groupScore": 0,// group points
                    "groupHealthStatus": 1,// group  health status
                    "groupHealthValue": 100.0000,// group health 
                    "rank": 1,// ranks
                    "totalGroup": 9,
                    "normalMember": 2,
                    "abnormalMember": 0,
                    "undetectedMember": 0,
                    "applySum": 0,
                    "status": 1,// status
                    "normalHealthTime": 54900,// health duration
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
    
    
    
####   Get group member information
   Note:
          
          Through this interface, you can get group member information
          
   Request address:
   
      /third/1.0/group/detail
   Request method:
   
      GET
   Request parameters:
   
   Name |	Type |	Required  |	Description
       ---|   ---|    ---|    ---
       groupNumber|String|Yes|group number
       pageNum| Integer|Yes|page
       pageSize|Integer|Yes|items per page
       userDid|String|Yes|user did
       
   Request example:
   
    timestamp:1591845427619
    sign:06388a91f1780e59102c6466a17c2dddd63df137731e34a8a451b617d4e28a77
    userDid:ihsRJhHxxxxxx9Dv1BS4ASipFqB
    groupNumber:26xxxxx558
    tenantId:xxxx
    pageNum:1
    pageSize:10
   Result Response:
 
    {
        "code": 0,
        "success": true,
        "msg": "Succeed",
        "data": {
            "list": [
                {
                    "did": "ihsRJhHzUPnxxxxxxDv1BS4ASipFqB",
                    "uid": 72,
                    "userName": " Fred",
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
                    "userName": " Fred",
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
    
    
####   Get the group audit list
   Note:
          
          Through this interface, you can pull the application list
          
   Request address:
   
      /third/1.0/group/list
   Request method:
   
      GET 
   Request parameters:
   
   Name |	Type |	Required  |	Description
       ---|   ---|    ---|    ---
       groupNumber|String|Yes|group number
       pageNum| Integer|Yes|page
       pageSize|Integer|Yes|items per page
       userDid|String|Yes|user did
       
   Request example:
   
    timestamp:1591845427619
    sign:06388a91f1780e59102c6466a17c2dddd63df137731e34a8a451b617d4e28a77
    userDid:ihsRJhHxxxxxx9Dv1BS4ASipFqB
    groupNumber:26xxxxx558
    tenantId:xxxx
    pageNum:1
    pageSize:10
   Result Response:
 
    {
      "code": 0,
      "data": {
        "firstPage": true,
        "lastPage": true,
        "list": [
          {
            "createTime": "2020-06-11T02:03:29.174Z",
            "deleted": false,
            "groupNumber": "string",// group  number
            "healthStatus": 0,// applicant health status(0 health1 Hyperthermia2 Not detected)
            "id": 0,
            "inviterDid": "string",// inviter did
            "inviterType": 0,// invite type
            "json": "string",
            "memberDid": "string",//member did
            "nickName": "string",// nickname
            "photo": "string",
            "remark": "string",// note
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
    
    
####   Group audit
   Note:
          
          Through this interface, you can approve the application for joining the group
          
   Request address:
   
      /third/1.0/group/apply
   Request method:
   
      GET 
   Request parameters:
   
   Name |	Type |	Required  |	Description
       ---|   ---|    ---|    ---
       groupNumber|String|Yes|group number
       inviteLogId| Long|Yes|page number
       status|Integer|Yes|Audit status (0: reject, 1: accept)
       userDid|String|Yes|user did
       
   Request example:
   Result Response:
 
    {
      "code": 0,
      "data": {},
      "map": {},
      "msg": "string",
      "success": true
    }
    
 
 
