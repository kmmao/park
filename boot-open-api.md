# boot-open-api 接口文档

## 一、接口规范

### 请求地址

测试环境：请求域名：http://test-gateway.qcyuns.com/park-data-api/data/centre/api/v1
正式环境：联系对接技术

---

### 1. 字符集与编码

#### 1.1 字符集和编码

- 所有报文采用UTF-8编码方式，传输报文时，应注意报文中不能包含UTF-8编码的BOM header（即EF BB BF）。

- 在整个开放平台的字段命名格式要求采用驼峰方式(parkId)进行字段命名。


---

### 2. 通讯规范


####  2.1 通讯请求
- 开放平台的通讯协议为：HTTPS POST请求，HTTPS表示单向SSL请求，保证链路通讯层安全。
- 请求URL规则为：https:// 域名/api/服务名称/版本号；
- 请求URL示例：	http://test-gateway.qcyuns.com/park-data-api/data/centre/api/v1

#### 2.2 通讯规范

- 请求方式采用结构化数据（JSON）进行传递
- 所有API接口请求，参数均在POST的body中传输
- Content-Type：application/json


---

### 3. 请求报文结构

#### 3.1 请求报文结构

-  公共请求参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	           |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（每次请求唯一，用于追踪某个请求报文，可以使用uuid生成）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

###开始对接开始前找技术人员分配测试参数：机构ID，机构密钥，车场ID

**签名生成规则**
 - **过滤空值**
 - 将公共参数按ASCII码从小到大排序按`|`拼接后得到字符串A
 - 将业务参数ASCII码从小到大排序按`|`拼接后得到字符串B
 - A+B+ 机构密钥，组成新的字符串C
 - 最后将字符串C进行md5加密，最终得到签名sign

**示例**

 - 假设请求参数如下：
 ``` 
	{
	&quot;service&quot;: &quot;getOwner&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;f719b06d-210b-4989-9c7f-02e85f22fe01&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;phone&quot;: &quot;13148762240&quot;,
		&quot;name&quot;: &quot;pasika&quot;,
		&quot;address&quot;: &quot;测试&quot;
	},
	&quot;sign&quot;: null
}
 ```
 - 生成签名流程如下
 ``` 
	1. 字符串A：msgId=f719b06d-210b-4989-9c7f-02e85f22fe01|orgId=BTTEST01|service=getOwner|version=01|
	2. 字符串B：address=测试|name=pasika|parkId=11609|phone=13148762240|
	3. MD5(字符串C): md5(字符串A+字符串B+秘钥)
 ```
 - 最终请求参数
 ``` 
	{
	&quot;service&quot;: &quot;getOwner&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;f719b06d-210b-4989-9c7f-02e85f22fe01&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;phone&quot;: &quot;13148762240&quot;,
		&quot;name&quot;: &quot;pasika&quot;,
		&quot;address&quot;: &quot;测试&quot;
	},
	&quot;sign&quot;: &quot;cc8eec3bf5b68571d8d7e8b608157808&quot;
}
 ```

---

### 4. 返回报文结构

#### 3.1 返回报文结构

-  返回报文参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|retCode  |int	|否	|状态码	 |	0-正常，其他-异常       |
|retMsg   |String 	|是	|状态码处理描述  	|错误描述	|
|retData  |Object	|是	|业务响应内容   |	根据业务接口返回不同内容 ||
 
**示例**

``` 
 {
    &quot;retCode&quot;: -4,
    &quot;retMsg&quot;: &quot;无该停车场权限&quot;,
    &quot;retData&quot;: null
}
``` 

 ``` 
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;parkId&quot;: 10102,
        &quot;parkName&quot;: &quot;Boot测试车场&quot;,
        &quot;plate&quot;: &quot;粤GHT4TR&quot;,
        &quot;carType&quot;: 1,
        &quot;carInfo&quot;: {
            &quot;inTime&quot;: &quot;2021-10-28 17:48:13&quot;,
            &quot;stopTime&quot;: &quot;142小时35分钟&quot;,
            &quot;stopTimeSecond&quot;: &quot;513299&quot;,
            &quot;amount&quot;: 72.0,
            &quot;totalFee&quot;: 72.0,
            &quot;beforePay&quot;: 0.0,
            &quot;discountVal&quot;: &quot;无&quot;,
            &quot;discountType&quot;: null,
            &quot;laneId&quot;: null,
            &quot;recordId&quot;: 422571,
            &quot;localId&quot;: null,
            &quot;inPic&quot;: &quot;http://boot-park.oss-cn-beijing.aliyuncs.com/picture/10102/02E1702201409536/20211028/17/20211028_174813_138.jpg&quot;
        }
    }
}
 ``` 


---

### 5. SDK与DEMO下载


#### 5.1 SDK与DEMO下载
|  平台和语言 | 版本 | 时间  | 操作  |
| ------------ | --- | ------------ |------------ |
| JAVA  | V1  | 2021年11月4日 |[park-api-v1.zip](https://www.showdoc.com.cn/server/api/attachment/visitfile/sign/b5ee42bbd88dff51ed80a8d968fb9d83 &quot;[park-api-v1.zip&quot;)|
| JAVA  | V1  | 2021年12月12日 |[park-api-v1.zip](https://www.showdoc.com.cn/server/api/attachment/visitfile/sign/292a5b348a68357ebbfaaec586e27320 &quot;[park-api-v1.zip&quot;)|



#### 5.2 版本介绍
- 2021年11月4日
	- 获取车辆信息接口（新增）
	- 续费通知接口（新增）
	
- 2021年12月12日
	- 进出场数据推送接口（新增）
	- 获取车场通道信息接口（新增）
	- 通道二维码接口（新增）
  
#### 5.3 DEMO示例

![](https://www.showdoc.com.cn/server/api/attachment/visitfile/sign/ec091b590af0d82fb4cd71364e76eb71)

- **特别提示**： 测试用例在****test.example****目录中

---

## 二、前言

### 说明

本文档主要用于帮助合作方相关技术人员了解与停车开放平台对接的流程和开发规范。


---

### 更新日志

    
-  2021年11月

|  日期 | 修改人 | 涉及接口   |  修改内容 | 版本  |
| ------------ | ------------ | ------------ | ------------ |
| 11月2日  | 星辰  | 获取车辆信息接口  | 新增 | V1    |
| 11月3日  | 星辰  | 续费通知接口  	 | 新增 |  V1  |
| 12月12日 | 星辰 | 进出场数据接口(推送)  	 | 新增 |  V1  |
| 12月12日  | 星辰  | 获取车场通道信息  	 | 新增 |  V1  |
| 12月12日  | 星辰  | 通道二维码接口  	 | 新增 |  V1  |










---

## 三、上行接口（停车系统主动推送）

### 1. 进出场记录推送（无需解密-无签名）

**简要描述：** 

- 车场车辆进出数据推送接口（该接口是入场后，和出场后进行推送）

**请求URL：** 
- `由对接方提供给技术支持，后台配置。`（所有推送都是同一个地址推送）
  
**请求方式：**
- POST 



**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 inout |
|version  |String 	|否	| 版本  	|	V1	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  ||

**业务请求参数** 


|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |   |
|parkName  |String	|否	|车场名称	 |   |
|recordId  |int 	|否	|平台进出场标识	|	|
|plate  |String	|否	|车牌号	 |   |
|plateNo  |String	|否	|车牌号（去除省份）	 |   |
|carType  |int	|否	|车型 (已弃用)	 |   |
|inTime  |String	|否	|入场时间	 |   |
|inLane  |int	|否	|入场通道ID	 |   |
|inRemark  |String	|否	|入场描述	 |   |
|inLaneName  |String	|否	|入场通道名称	 |   |
|inPic  |String	|否	|入场图片	 |   |
|status  |int	|否	|进出状态 0-在场 1-出场	 |   |
|totalFee  |double	|否	|总应收（单位/元）	 |   |
|cashFee  |double	|否	|现金（单位/元）	 |   |
|epayFee  |double	|否	|电子支付（单位/元）	 |   |
|discountType  |int	|否	|优惠类型 	 | 1-时间 2-金额 3-折扣 4-全免 |
|discountVal |String 	|否	|单位：分钟/元  	||
|outTime |String 	|否	|出场时间  	|		|
|outLane |int 	|否	|出场通道ID  	|		|
|outLaneName |String 	|否	|出场通道名称  	|		|
|outPic |String 	|否	|出场图片  	|		|
|outRemark |String 	|否	|出场描述  	|		|
|admin |String 	|否	|管理员  	|	|
|totalSpace |String 	|否	|总车位  	|	|
|emptySpace |String 	|否	|剩余车位  	|	|
|plateColor |int 	|否	| 车牌颜色 	|	|
|vehicleType |int 	|否	| 车辆类型 	|	|
|type |int 	|否	| 类型（1-按临时车计费 2-按固定车计费3-储值车计费） 	|	|
|chargeType |int 	|否	| 计费类型 	|	|
|chargeTypeName |int 	|否	| 计费类型名称 	|	|
|isMainGarage |Boolean 	|否	| 是否主车库 	|true：主车库 false：子车库	||

**车牌颜色类型** 

|plateColor|描述
|:----    |:------- |
|0  |未知	|
|1  |蓝色	|
|2  |黄色	|
|3  |白色	|
|4  |黑色	|
|5  |绿色	|
|6  |黄绿色	|

**车辆类型** 

|vehicleType|描述
|:----    |:------- |
|0  |不区分	|
|1  |蓝牌车	|
|2  |黄牌车	|
|3  |新能源小车	|
|4  |新能源大车	|
|5  |军警车	|
|6  |黑牌车	|
|7  |其它	|
|8  |客2(8-19座)	|
|9  |客3(20座以上)	|
|10  |蓝牌货车	|
|11  |货2(6~9米)	|
|12  |货3(9~13米)	|
|13  |货4(12~18米)	|
|14  |货5(18米以上)	|
|15  |拖头车	|
|16  |访客车	|
|17  |港澳车	|
|18  |澳门本地车牌	|
|19  |香港本地车牌	|

 **请求示例**
```
{
	&quot;service&quot;: &quot;inout&quot;,
	&quot;version&quot;: &quot;V1&quot;,
	&quot;msgId&quot;: &quot;25f33674-1136-4e21-ada1-ed2a14ebdeb6&quot;,
	&quot;orgId&quot;: &quot;1&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 14850,
		&quot;parkName&quot;: &quot;xxx&quot;,
		&quot;recordId&quot;: 2641608,
		&quot;plate&quot;: &quot;冀A60M8C&quot;,
		&quot;carType&quot;: 0,
		&quot;inTime&quot;: &quot;2023-09-11 10:09:16&quot;,
		&quot;inLane&quot;: 14272,
		&quot;inRemark&quot;: &quot;有效期内&quot;,
		&quot;inLaneName&quot;: &quot;入口1&quot;,
		&quot;inPic&quot;: &quot;http://boot-park2.oss-cn-qingdao.aliyuncs.com//picture/14856/d4e4c399-1653c8d4/20230911/10/20230911_100916_945.jpg&quot;,
		&quot;status&quot;: 1,
		&quot;totalFee&quot;: 0.0,
		&quot;cashFee&quot;: 0.0,
		&quot;epayFee&quot;: 0.0,
		&quot;discountType&quot;: 0,
		&quot;discountVal&quot;: &quot;0.0&quot;,
		&quot;outTime&quot;: &quot;2023-09-11 10:41:40&quot;,
		&quot;outLane&quot;: 14273,
		&quot;outLaneName&quot;: &quot;出口1&quot;,
		&quot;outRemark&quot;: null,
		&quot;outPic&quot;: &quot;http://boot-park2.oss-cn-qingdao.aliyuncs.com//picture/14856/05029671-4cf133d9/20230911/10/20230911_104141_097.jpg&quot;,
		&quot;admin&quot;: &quot;baoan&quot;,
		&quot;plateColor&quot;:&quot;1&quot;,
		&quot;vehicleType&quot;:&quot;1&quot;,
		&quot;isMainGarage&quot;:&quot;true&quot;
	}
}
 ```

**返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|retCode  |String	|否	|状态码	 |	0-正常，其他-异常       |
|retMsg   |String 	|是	|状态码处理描述  	|错误描述	|
|retData  |Object	|是	|业务响应内容   |	根据业务接口返回不同内容 ||

**返回示例**
 
 ``` 
 	{
		&quot;retCode&quot;:0,
		&quot;retMsg&quot;:&quot;成功&quot;,
		&quot;retData&quot;:null
	}
 ``` 






---

### 2. 车辆续费推送（无需解密-无签名）

**简要描述：** 

- 使用停车系统进行产生充值记录会进行推送
- 第一次添加登记车、（现金续费，电子支付续费）
- 下行接口操作登记车（添加、更新）不会进行推送

**请求URL：** 
- `由对接方提供给技术支持，后台配置。`（所有推送都是同一个地址推送）
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 renewalRecharge |
|version  |String 	|否	| 版本  	|	V1	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  ||


**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |   |
|plate  |String	|否	|车牌号	 |   |
|payType  |int	|否	|支付类型 1-现金 2-电子支付 3-退款 4-充正	 |   |
|operationType  |int	|否	|操作类型：1-添加 2-修改	 |   |
|beginDate  |String	|否	|开始时间	 |   |
|endDate |String 	|否	|结束时间  	|		|
|amount |double 	|否	|支付金额  	|		|
|plateChargeType |String 	|否	|计费车型id  	|		|
|admin |String 	|否	|管理员  	|	||

 **请求示例**
```
{
	&quot;admin&quot;: &quot;北区前台&quot;,
	&quot;amount&quot;: 250.0,
	&quot;beginDate&quot;: &quot;2022-02-09&quot;,
	&quot;endDate&quot;: &quot;2022-02-09&quot;,
	&quot;operationType&quot;: 2,
	&quot;parkId&quot;: 11367,
	&quot;payType&quot;: 1,
	&quot;plate&quot;: &quot;粤B0J0P5&quot;,
	&quot;plateChargeType&quot;: 11367
}
 ```

**返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|retCode  |String	|否	|状态码	 |	0-正常，其他-异常       |
|retMsg   |String 	|是	|状态码处理描述  	|错误描述	|
|retData  |Object	|是	|业务响应内容   |	根据业务接口返回不同内容 ||

**返回示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: &quot;成功&quot;,
	&quot;retData&quot;: null
}
 ``` 






---

## 四、下行接口（第三方主动调用）

### 1. 获取车辆信息

**简要描述：** 

- 根据车牌号获取车辆信息（临时车，月租车，储值车）

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 CarInfo          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|plate  |String 	|否	|车牌号  	|		||

 **请求示例**
```
{
	&quot;service&quot;: &quot;CarInfo&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;plate&quot;: &quot;粤GHT4TR&quot;
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|parkName  |String 	|否	|车场名称  	|		|
|plate  |String 	|否	|车牌号  	|		|
|carType  |int 	|否	|车型  	|1-临时车 2-月租车 3-储值车|
|carInfo  |Object 	|否	|车辆信息  	|根据车型显示不同内容 ||

 **车辆信息（carInfo）详情**
- carType=1时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|stopTime  |String 	|否	|停车时长  	|		|
|stopTimeSecond  |String 	|否	|停车时间戳  |	|
|amount  |Double 	|否	|需交费  	|单位：分钟/元|
|totalFee	  |Double	|否	|总应收	 |   单位：分钟/元    |
|beforePay  |Double 	|否	|已缴费用  	|	单位：分钟/元	|
|discountVal  |String 	|否	|已优惠  	|	单位：分钟/元	|
|discountType  |int 	|否	|优惠券类型  	|1.金额（分钟） 2.时间（元）|
|recordId  |int 	|是	|平台进出场ID  	|	recordId和localId必须一个不为空	|
|localId  |int 	|是	|本地进出场ID  	|	recordId和localId必须一个不为空	|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

- carType=2，carType=3时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|carId  |int	|否	|车辆唯一ID	 |       |
|chargeType  |int	|否	|计费类型ID	 |       |
|endDate  |String 	|否	|有效期止  	|	2021-10-03	|
|spaces  |int 	|否	|车位数  	|	|
|phone  |String 	|是	|电话  	|	|
|address  |String 	|是	|地址  	|	|
|lockStatus  |int 	|是	|锁车状态  	|1-锁车 2-未锁车|
|status  |int 	|是	|状态  	|1-启用 2-未生效 3-暂停 4-过期|
|carSeatInfoList  |List&lt;CarSeatInfo&gt; 	|是	|车位信息List  	|储值车可为空 ||

**车位信息（CarSeatInfo）详情**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|seatNo  |String	|否	|车位编号	 |       |
|chargeType  |Integer 	|否	|计费车型ID  	|	|
|chargeName  |String 	|否	|计费车型name  	|	|
|dayCharge  |double 	|是	|日费用  	|	|
|monthCharge  |double	|是	|月费用  	|	|
|quarterCharge  |double |是	|季度费用  	|	|
|halfYearCharge  |double |是	|半年费用  	|	|
|yearCharge  |double|是	|年费用  	|储值车可为空 ||

**返回示例**
 
 ``` 
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;parkId&quot;: 10102,
        &quot;parkName&quot;: &quot;Boot测试车场&quot;,
        &quot;plate&quot;: &quot;粤GHT4TR&quot;,
        &quot;carType&quot;: 1,
        &quot;carInfo&quot;: {
            &quot;inTime&quot;: &quot;2021-10-28 17:48:13&quot;,
            &quot;stopTime&quot;: &quot;142小时35分钟&quot;,
            &quot;stopTimeSecond&quot;: &quot;513299&quot;,
            &quot;amount&quot;: 72.0,
            &quot;totalFee&quot;: 72.0,
            &quot;beforePay&quot;: 0.0,
            &quot;discountVal&quot;: &quot;无&quot;,
            &quot;discountType&quot;: null,
            &quot;laneId&quot;: null,
            &quot;recordId&quot;: 422571,
            &quot;localId&quot;: null,
            &quot;inPic&quot;: &quot;http://boot-park.oss-cn-beijing.aliyuncs.com/picture/10102/02E1702201409536/20211028/17/20211028_174813_138.jpg&quot;
        }
    }
}
 ``` 





---

### 2.获取车辆信息v2（公众号）


**简要描述：** 

- 根据车牌号获取车辆信息（临时车，月租车，储值车）
- 只支持私有云对接
- 该接口由于全平台检索场内车辆车辆，耗时较高（3s），不可作为高流量出口缴费进行查询

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 CarInfoV2          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|plate  |String 	|否	|车牌号  	|		||

 **请求示例**
```
{
	&quot;service&quot;: &quot;CarInfo&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;plate&quot;: &quot;粤GHT4TR&quot;
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|parkName  |String 	|否	|车场名称  	|		|
|plate  |String 	|否	|车牌号  	|		|
|carType  |int 	|否	|车型  	|1-临时车 2-月租车 3-储值车|
|carInfo  |Object 	|否	|车辆信息  	|根据车型显示不同内容 ||

 **车辆信息（carInfo）详情**
- carType=1时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|stopTime  |String 	|否	|停车时长  	|		|
|stopTimeSecond  |String 	|否	|停车时间戳  |	|
|amount  |Double 	|否	|需交费  	|单位：分钟/元|
|totalFee	  |Double	|否	|总应收	 |   单位：分钟/元    |
|beforePay  |Double 	|否	|已缴费用  	|	单位：分钟/元	|
|discountVal  |String 	|否	|已优惠  	|	单位：分钟/元	|
|discountType  |int 	|否	|优惠券类型  	|1.金额（分钟） 2.时间（元）|
|recordId  |int 	|是	|平台进出场ID  	|	recordId和localId必须一个不为空	|
|localId  |int 	|是	|本地进出场ID  	|	recordId和localId必须一个不为空	|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

- carType=2，carType=3时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|carId  |int	|否	|车辆唯一ID	 |       |
|chargeType  |int	|否	|计费类型ID	 |       |
|endDate  |String 	|否	|有效期止  	|	2021-10-03	|
|spaces  |int 	|否	|车位数  	|	|
|phone  |String 	|是	|电话  	|	|
|address  |String 	|是	|地址  	|	|
|lockStatus  |int 	|是	|锁车状态  	|1-锁车 2-未锁车|
|status  |int 	|是	|状态  	|1-启用 2-未生效 3-暂停|
|carSeatInfoList  |List&lt;CarSeatInfo&gt; 	|是	|车位信息List  	|储值车可为空 ||

**车位信息（CarSeatInfo）详情**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|seatNo  |String	|否	|车位编号	 |       |
|chargeType  |Integer 	|否	|计费车型ID  	|	|
|chargeName  |String 	|否	|计费车型name  	|	|
|dayCharge  |double 	|是	|日费用  	|	|
|monthCharge  |double	|是	|月费用  	|	|
|quarterCharge  |double |是	|季度费用  	|	|
|halfYearCharge  |double |是	|半年费用  	|	|
|yearCharge  |double|是	|年费用  	|储值车可为空 ||

**返回示例**
 
 ``` 
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;parkId&quot;: 10102,
        &quot;parkName&quot;: &quot;Boot测试车场&quot;,
        &quot;plate&quot;: &quot;粤GHT4TR&quot;,
        &quot;carType&quot;: 1,
        &quot;carInfo&quot;: {
            &quot;inTime&quot;: &quot;2021-10-28 17:48:13&quot;,
            &quot;stopTime&quot;: &quot;142小时35分钟&quot;,
            &quot;stopTimeSecond&quot;: &quot;513299&quot;,
            &quot;amount&quot;: 72.0,
            &quot;totalFee&quot;: 72.0,
            &quot;beforePay&quot;: 0.0,
            &quot;discountVal&quot;: &quot;无&quot;,
            &quot;discountType&quot;: null,
            &quot;laneId&quot;: null,
            &quot;recordId&quot;: 422571,
            &quot;localId&quot;: null,
            &quot;inPic&quot;: &quot;http://boot-park.oss-cn-beijing.aliyuncs.com/picture/10102/02E1702201409536/20211028/17/20211028_174813_138.jpg&quot;
        }
    }
}
 ``` 





---

### 3.获取车辆临停缴费信息V3


**简要描述：** 

- 根据车牌号获取车辆信息（临时车，月租车，储值车），不区分车辆类型（统一返回 1-临时车）
- 统一返回临停缴费信息,包含平台id

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 CarInfoV3         |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer 	|否	|车场ID  	|		|
|plate  |String 	|否	|车牌号  	|		||

 **请求示例**
```
{
	&quot;service&quot;: &quot;CarInfo&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;plate&quot;: &quot;粤GHT4TR&quot;
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|parkName  |String 	|否	|车场名称  	|		|
|plate  |String 	|否	|车牌号  	|		|
|carType  |int 	|否	|车型  	|统一返回 1-临时车|
|carInfo  |Object 	|否	|车辆信息  	|根据车型显示不同内容 ||

 **车辆信息（carInfo）详情**
- carType=1时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|stopTime  |String 	|否	|停车时长  	|		|
|stopTimeSecond  |String 	|否	|停车时间戳  |	|
|amount  |Double 	|否	|需交费  	|单位：分钟/元|
|totalFee	  |Double	|否	|总应收	 |   单位：分钟/元    |
|beforePay  |Double 	|否	|已缴费用  	|	单位：分钟/元	|
|discountVal  |String 	|否	|已优惠  	|	单位：分钟/元	|
|discountType  |int 	|否	|优惠券类型  	|1.金额（分钟） 2.时间（元）|
|recordId  |int 	|是	|平台进出场ID  	|	recordId和localId必须一个不为空	|
|localId  |int 	|是	|本地进出场ID  	|	recordId和localId必须一个不为空	|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||



**返回示例**
 
 ``` 
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;parkId&quot;: 10102,
        &quot;parkName&quot;: &quot;Boot测试车场&quot;,
        &quot;plate&quot;: &quot;粤GHT4TR&quot;,
        &quot;carType&quot;: 1,
        &quot;carInfo&quot;: {
            &quot;inTime&quot;: &quot;2021-10-28 17:48:13&quot;,
            &quot;stopTime&quot;: &quot;142小时35分钟&quot;,
            &quot;stopTimeSecond&quot;: &quot;513299&quot;,
            &quot;amount&quot;: 72.0,
            &quot;totalFee&quot;: 72.0,
            &quot;beforePay&quot;: 0.0,
            &quot;discountVal&quot;: &quot;无&quot;,
            &quot;discountType&quot;: null,
            &quot;laneId&quot;: null,
            &quot;recordId&quot;: 422571,
            &quot;localId&quot;: null,
            &quot;inPic&quot;: &quot;http://boot-park.oss-cn-beijing.aliyuncs.com/picture/10102/02E1702201409536/20211028/17/20211028_174813_138.jpg&quot;
        }
    }
}
 ``` 






---

### 4. 获取通道信息


**简要描述：** 

- 根据车场ID获取通道信息列表，作为入场出场开闸标记。

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 LaneInfoList          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       ||

 **请求示例**
```
{
	&quot;service&quot;: &quot;LaneInfoList&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;6d5e55c9-96e0-4c98-93a9-a156b777a5e5&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102
	},
	&quot;sign&quot;: &quot;b58023cc394e4801b57c6e62cb79fe08&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|parkName  |String 	|否	|车场名称  	|		|
|laneList  |List&lt;LaneInfo&gt; 	|否	|通道信息List  	| ||

 **通道信息（LaneInfo）详情**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|laneId	  |int	|否	|通道ID	 |       |
|laneName  |String 	|否	|通道名称  	|		|
|laneType  |laneType 	|否	|通道类型  | 1-入 2-出	||
 
**返回示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;parkName&quot;: &quot;Boot测试车场&quot;,
		&quot;laneList&quot;: [{
			&quot;laneId&quot;: 944,
			&quot;laneName&quot;: &quot;测试地库入口&quot;,
			&quot;laneType&quot;: 1
		}, {
			&quot;laneId&quot;: 1168,
			&quot;laneName&quot;: &quot;测试地库出口&quot;,
			&quot;laneType&quot;: 2
		}]
	}
}
 ``` 





---

### 5. 获取车辆计费类型

**简要描述：** 

- 根据车场车辆管理类型（临时车，月租车，储值车）

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 getChargeType          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |      10102 |
 **请求示例**
```
{
	&quot;service&quot;: &quot;getChargeType&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|name  |String 	|否	|计费类型名称  	|		|
|chargeType  |integer 	|否	|计费类型  	|		|
|type  |integer 	|否	|类型（1-按临时车计费 2-按固定车计费3-储值车计费）  	| |
|monthCharge  |Double 	|否	|包月费用  	| |
|quarterCharge  |Double 	|否	|包季费用  	| |
|halfYearCharge  |Double 	|否	|半年费用  	| |
|yearCharge  |Double 	|否	|包年费用  	| |
|status  |Object 	|否	|状态（1-启用 2-禁用）  	| ||


**返回示例**
 
 ``` 
{
  &quot;retCode&quot;: 0,
  &quot;retMsg&quot;: null,
  &quot;retData&quot;: [
    {
      &quot;parkId&quot;: 66068,
      &quot;name&quot;: &quot;临时车&quot;,
      &quot;chargeType&quot;: 1,
      &quot;type&quot;: 1,
      &quot;monthCharge&quot;: 0,
      &quot;quarterCharge&quot;: 0,
      &quot;halfYearCharge&quot;: 0,
      &quot;yearCharge&quot;: 0,
      &quot;status&quot;: 1
    },
    {
      &quot;parkId&quot;: 66068,
      &quot;name&quot;: &quot;月租车&quot;,
      &quot;chargeType&quot;: 2,
      &quot;type&quot;: 2,
      &quot;monthCharge&quot;: 100,
      &quot;quarterCharge&quot;: 100,
      &quot;halfYearCharge&quot;: 0,
      &quot;yearCharge&quot;: 0,
      &quot;status&quot;: 1
    },
    {
      &quot;parkId&quot;: 66068,
      &quot;name&quot;: &quot;储值车&quot;,
      &quot;chargeType&quot;: 3,
      &quot;type&quot;: 3,
      &quot;monthCharge&quot;: 0,
      &quot;quarterCharge&quot;: 0,
      &quot;halfYearCharge&quot;: 0,
      &quot;yearCharge&quot;: 0,
      &quot;status&quot;: 1
    },
    {
      &quot;parkId&quot;: 66068,
      &quot;name&quot;: &quot;免费车&quot;,
      &quot;chargeType&quot;: 4,
      &quot;type&quot;: 2,
      &quot;monthCharge&quot;: 0,
      &quot;quarterCharge&quot;: 0,
      &quot;halfYearCharge&quot;: 0,
      &quot;yearCharge&quot;: 0,
      &quot;status&quot;: 1
    },
    {
      &quot;parkId&quot;: 66068,
      &quot;name&quot;: &quot;固定临停车&quot;,
      &quot;chargeType&quot;: 5,
      &quot;type&quot;: 3,
      &quot;monthCharge&quot;: 0,
      &quot;quarterCharge&quot;: 0,
      &quot;halfYearCharge&quot;: 0,
      &quot;yearCharge&quot;: 0,
      &quot;status&quot;: 1
    },
    {
      &quot;parkId&quot;: 66068,
      &quot;name&quot;: &quot;时段月租车&quot;,
      &quot;chargeType&quot;: 6,
      &quot;type&quot;: 3,
      &quot;monthCharge&quot;: 300,
      &quot;quarterCharge&quot;: 900,
      &quot;halfYearCharge&quot;: 0,
      &quot;yearCharge&quot;: 3600,
      &quot;status&quot;: 1
    }
  ]
}
 ``` 





---

### 6.根据序列号获取通道信息


**简要描述：** 

- 根据设备序列号获取通道信息

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 GetParkLaneBySN          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       ||
|cameraSn  |String	|否	|序列号	 |       ||

 **请求示例**
```
{
	&quot;service&quot;: &quot;GetParkLaneBySN&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;7b1d9dec-46b0-45cd-a034-50e7c2c89355&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 66014,
		&quot;cameraSn&quot;: &quot;f0fdbdcb-76c29c33-3331&quot;
	},
	&quot;sign&quot;: &quot;4d8e3ecd0daf200cfaee444570a51905&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|laneId  |int 	|否	|通道id  	|		|
|laneName | String 	|否	|通道信息  	| |
|cameraSn | String 	|否	|序列号  	| ||

 **通道信息（LaneInfo）详情**

 
**返回示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;parkId&quot;: 66014,
		&quot;laneId&quot;: 5706,
		&quot;laneName&quot;: &quot;入口1&quot;,
		&quot;cameraSn&quot;: &quot;f0fdbdcb-76c29c33-333&quot;
	}
}
 ``` 





---

### 0. 获取车场信息

**简要描述：** 

- 根据停车场ID获取车场信息

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 ParkInfo          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|停车场ID	 |       ||


 **请求示例**
```
{
	&quot;service&quot;:&quot;ParkInfo&quot;,
	&quot;version&quot;:&quot;01&quot;,
	&quot;msgId&quot;:&quot;be4cba27-97ab-4a90-b423-60c1e8176932&quot;,
	&quot;orgId&quot;:&quot;BTTEST01&quot;,
	&quot;data&quot;:{
		&quot;parkId&quot;:10485
	},
	&quot;sign&quot;:&quot;ed29219053303542eed1bda88e9b49fe&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|示例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|停车场ID	 |       |
|parkName  |String 	|否	| 车场名称 	||
|companyName  |String 	|否	| 物业名称 	||
|principal  |String 	|否	| 联系人 	||
|phone  |int 	|否	| 联系人电话 	||
|province  |String 	|否	| 省 	||
|city  |String 	|否	| 市 	||
|district  |String 	|否	| 区 	||
|totalSpace  |int 	|否	|总车位  	|10000|
|emptySpace  |int 	|否	|剩余车位  	|10000|
| positionX |Double 	|否	|经度  	|113.96519|
| positionY |Double 	|否	|纬度  	|22.59042|

**返回示例**
 
 ``` 
{
	&quot;retCode&quot;:0,
	&quot;retMsg&quot;:null,
	&quot;retData&quot;:{
		&quot;parkId&quot;: 60808,
		&quot;parkCarInfo&quot;:{
			&quot;parkId&quot;: 60808,
			&quot;parkName&quot;: &quot;薄荷测试停车场&quot;,
			&quot;companyName&quot;: &quot;薄荷服务商&quot;,
			&quot;principal&quot;: &quot;薄荷&quot;,
			&quot;phone&quot;: 15200758890,
			&quot;province&quot;: &quot;广东省&quot;,
			&quot;city&quot;: &quot;深圳市&quot;,
			&quot;district&quot;: &quot;福田区&quot;,
			&quot;totalSpace&quot;: 10000,
			&quot;emptySpace&quot;: 10000,
			&quot;positionX&quot;:113.96519,
			&quot;positionY&quot;:22.59042
		}
	}
}
 ``` 






---

### 1. 获取车位

#### 1.1 获取车场车位信息（总车位、总剩余车位）

**简要描述：** 

- 获取车场余位接口

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 getParkSpace          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |   |
|code  |String	|否	|通讯码	 |   ||

 **请求示例**
```
{
	&quot;service&quot;: &quot;getParkSpace&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;code&quot;:&quot;xxxxxxxxxxxxx&quot;
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
```
**返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
| code  |String	|否	|状态码	 |	0-正常，其他-异常       |
| msg   |String 	|是	|状态码处理描述  	|错误描述	|
|data     |Object	|否   | 具体业务接口json对象   |	  |
| totalNum  |int	|是	|总车位    ||
| emptyNum  |int	|是	|剩余车位   | ||
**返回示例**
````
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;totalNum&quot;: 2500,
		&quot;emptyNum&quot;: 1995
	}
}
````

```





---

#### 1.2 获取车场不同车库的车位


**简要描述：** 

- 获取车场不同车库的车位，（如，地下车库和地面车库不同的车位数）

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 getParkGarageSpace          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |   |
|code  |String	|否	|通讯码	 |   ||

 **请求示例**
```
{
	&quot;service&quot;: &quot;getParkSpace&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;code&quot;:&quot;xxxxxxxxxxxxx&quot;
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
```
**返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
| code  |String	|否	|状态码	 |	0-正常，其他-异常       |
| msg   |String 	|是	|状态码处理描述  	|错误描述	|
|retData     |Object	|否   | 具体业务接口json对象   |	  |
| parkId  |int	|否	|车场id    ||
| parkName  |String	|否	|车场名称    ||
| name  |String	|否	|车库名称    ||
| totalNum  |int	|否	|当前车库总车位    ||
| emptyNum  |int	|否	|当前车库剩余车位    ||
| parkGarageNames  |String	|是	|从属车库    ||
| type  |int	|否	|1.主车库，2.子车库    |||



**返回示例**
````
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: [{
		&quot;parkId&quot;: 10102,
		&quot;parkName&quot;: &quot;河西xxx停车场&quot;,
		&quot;name&quot;: &quot;地面车库&quot;,
		&quot;totalNum&quot;: 10000,
		&quot;emptyNum&quot;: 7419,
		&quot;parkGarageNames&quot;: null,
		&quot;type&quot;: 1
	}, {
		&quot;parkId&quot;: 10102,
		&quot;parkName&quot;: &quot;河西xxx停车场&quot;,
		&quot;name&quot;: &quot;地下车库&quot;,
		&quot;totalNum&quot;: 200,
		&quot;emptyNum&quot;: 20,
		&quot;parkGarageNames&quot;: &quot;地面车库&quot;,
		&quot;type&quot;: 2
	}]
}
````

```





---

### 2. 临停缴费

#### 业务流程

![](https://www.showdoc.com.cn/server/api/attachment/visitFile?sign=69752b137279a45cc66a1cad08fa460e&amp;file=file.jpg)

---

#### 2.1 获取车辆信息接口

**简要描述：** 

- 根据车牌号获取车辆信息（临时车，月租车，储值车）
- 兼容业主临停费用获取：
产生临停费用时参考——carType=1时,响应参数
未产生临停费用时参考——carType=2,carType=3时,响应参数

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 CarInfo          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|plate  |String 	|否	|车牌号  	|		||

 **请求示例**
```
{
	&quot;service&quot;: &quot;CarInfo&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;plate&quot;: &quot;粤GHT4TR&quot;
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|parkName  |String 	|否	|车场名称  	|		|
|plate  |String 	|否	|车牌号  	|		|
|carType  |int 	|否	|车型  	|1-临时车 2-月租车 3-储值车|
|carInfo  |Object 	|否	|车辆信息  	|根据车型显示不同内容 ||

 **车辆信息（carInfo）详情**
- carType=1时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|stopTime  |String 	|否	|停车时长  	|		|
|stopTimeSecond  |String 	|否	|停车时间戳  |	|
|amount  |Double 	|否	|需交费  	|单位：分钟/元|
|totalFee	  |Double	|否	|总应收	 |   单位：分钟/元    |
|beforePay  |Double 	|否	|已缴费用  	|	单位：分钟/元	|
|discountVal  |String 	|否	|已优惠  	|	单位：分钟/元	|
|discountType  |int 	|否	|优惠券类型  	|1.金额（分钟） 2.时间（元）|
|recordId  |int 	|是	|平台进出场ID  	|		|
|localId  |int 	|是	|本地进出场ID  	|		|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

- carType=2，carType=3时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|carId  |int	|否	|车辆标识	 |       |
|chargeType  |int	|否	|计费类型ID	 |       |
|endDate  |String 	|否	|有效期止  	|	2021-10-03	|
|spaces  |int 	|否	|车位数  	|	|
|phone  |String 	|是	|电话  	|	|
|address  |String 	|是	|地址  	|	|
|lockStatus  |int 	|是	|锁车状态  	|1-锁车 2-未锁车|
|status  |int 	|是	|状态  	|1-启用 2-未生效 3-暂停|
|carSeatInfoList  |List&lt;CarSeatInfo&gt; 	|是	|车位信息List  	|储值车可为空 ||

**车位信息（CarSeatInfo）详情**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|seatNo  |String	|否	|车位编号	 |       |
|chargeType  |Integer 	|否	|计费车型ID  	|	|
|chargeName  |String 	|否	|计费车型name  	|	|
|dayCharge  |double 	|是	|日费用  	|	|
|monthCharge  |double	|是	|月费用  	|	|
|quarterCharge  |double |是	|季度费用  	|	|
|halfYearCharge  |double |是	|半年费用  	|	|
|yearCharge  |double|是	|年费用  	|储值车可为空 ||

**返回示例**
 
 ``` 
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;parkId&quot;: 10102,
        &quot;parkName&quot;: &quot;Boot测试车场&quot;,
        &quot;plate&quot;: &quot;粤GHT4TR&quot;,
        &quot;carType&quot;: 1,
        &quot;carInfo&quot;: {
            &quot;inTime&quot;: &quot;2021-10-28 17:48:13&quot;,
            &quot;stopTime&quot;: &quot;142小时35分钟&quot;,
            &quot;stopTimeSecond&quot;: &quot;513299&quot;,
            &quot;amount&quot;: 72.0,
            &quot;totalFee&quot;: 72.0,
            &quot;beforePay&quot;: 0.0,
            &quot;discountVal&quot;: &quot;无&quot;,
            &quot;discountType&quot;: null,
            &quot;laneId&quot;: null,
            &quot;recordId&quot;: 422571,
            &quot;localId&quot;: null,
            &quot;inPic&quot;: &quot;http://boot-park.oss-cn-beijing.aliyuncs.com/picture/10102/02E1702201409536/20211028/17/20211028_174813_138.jpg&quot;
        }
    }
}
 ``` 





---

#### 2.2 支付后通知接口（电子支付）

**简要描述：** 

- 续费通知接口（临时车，月租车，储值车）

**请求URL：** 
- ` /data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 PayNotify          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|plate  |String	|否	|车牌号	 |       |
|outTradeNo  |String	|否	|外部订单号（唯一标识，不同订单，不可重复）	 |       |
|amount  |Double	|否	|金额（元）	 |       |
|payType  |Integer	|否	|支付类型 	 |0-现金，1-电子支付 |
|carType  |Integer	|否	|车型	 | 1-按临时车计费 2-按固定车计费 3-储值车计费  |
|carId  |Integer	|是	|固定车车主键ID	 |       |
|chargeType  |Integer	|否	|	计费车类型 |0-代表临时车，其他-对应(登记车型表)  |
|laneId  |Integer	|是	|通道id	 | （注意：无牌车的情况下，laneId必传）      |
|recordId  |Integer	|否	|平台进出场ID（纯云车场），recordId和localId必传一个，判断是否为空即可	 | |
|localId  |Integer	|否	|本地进出场ID（本地车场，recordId和localId必传一个，判断是否为空即可）	 | |
|endDate  |Integer	|是	|月租车截止日期	 | carType=2时，必传      |
|number  |Integer	|是	|月租车充值月数	 |  carType=2时，必传     |
|sourceType  |Integer	|否	|支付方式	 |电子支付方式 1-微信 2-支付宝 |
|openId  |Integer	|是	|电子支付标识	 |       |
|payRemark  |String 	|是	|支付详情  	|		|
|departurePay  |Integer 	|是	|0.无感支付 1.先离后付   	|		|
|departurePayOrderId  |String 	|是	|无感支付业务id  	|		||
	


 **请求示例**
```
{
	&quot;service&quot;: &quot;PayNotify&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;82a99bc2-f6be-4abc-8a84-d4b50ea1714c&quot;,
	&quot;orgId&quot;: &quot;qzrd&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 12742,
		&quot;plate&quot;: &quot;闽C656JB&quot;,
		&quot;outTradeNo&quot;: &quot;1649252074543779841&quot;,
		&quot;amount&quot;: 3.0,
		&quot;payType&quot;: 1,
		&quot;carType&quot;: 1,
		&quot;chargeType&quot;: 0,
		&quot;sourceType&quot;: 1,
		&quot;localId&quot;: 3524
	},
	&quot;sign&quot;: &quot;7663dd033ef342adff852f498d7fbbb0&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|retCode  |String	|否	|状态码	 |	0-正常，其他-异常       |
|retMsg   |String 	|是	|状态码处理描述  	|错误描述	|
|retData  |Object	|是	|业务响应内容   |	根据业务接口返回不同内容 ||

**返回示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;通知成功&quot;
}
 ``` 





---

#### 2.3. 无牌车进出场、出口支付



**简要描述：** 

- 用于无牌车入场，无牌车出场，有牌车出场。

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 scanLaneQrCode          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |   |
|laneId  |int 	|否	|通道ID  	|通道信息接口中获取	|
|laneType |int 	|否	|通道类型  	|1-入 2-出|
|noPlate |String 	|否	|无牌车  	|	无ABC123	||

 **请求示例**
```
{
	&quot;service&quot;: &quot;scanLaneQrCode&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;82346f42-1244-46db-a752-64becd230229&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;laneId&quot;: 219,
		&quot;laneType&quot;: 2,
		&quot;noPlate&quot;: &quot;无ABC123&quot;
	},
	&quot;sign&quot;: &quot;5d4b219406c4992de133f21f8544ae05&quot;
}
 ```

**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |  |
|laneId  |int 	|否	|通道ID  	|		|
|laneType  |int 	|否	|通道类型  	|1-入 2-出|
|plate  |int 	|否	|车牌号  	|无牌车入场，出场车牌号|
|inQrResponse  |Object 	|否	|入场响应  	|laneType=1时，返回入场信息 |
|outQrResponse  |Object 	|否	|出场响应  	|laneType=2时，返回停车信息||

**入场响应（inQrResponse）详情**
- laneType=1时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |String	|否	|入场时间	 |       |
|inLaneId  |int 	|否	|入场通道ID  	|		|
|inLaneName  |String 	|否	|入场通道名称  |	|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

**出场响应（outQrResponse）详情**
- laneType=2时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|stopTime  |String 	|否	|停车时长  	|		|
|stopTimeSecond  |String 	|否	|停车时间戳  |	|
|amount  |Double 	|否	|需交费  	|单位：分钟/元|
|totalFee	  |Double	|否	|总应收	 |   单位：分钟/元    |
|beforePay  |Double 	|否	|已缴费用  	|	单位：分钟/元	|
|discountVal  |String 	|否	|已优惠  	|	单位：分钟/元	|
|discountType  |int 	|否	|优惠券类型  	|1.金额（分钟） 2.时间（元）|
|recordId  |String 	|是	|平台进出场ID  	|		|
|localId  |String 	|是	|本地进出场ID  	|		|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

**返回示例**
 
 ``` 
	{&quot;retCode&quot;:400,&quot;retMsg&quot;:&quot;未查询到该车辆入场&quot;,&quot;retData&quot;:null}
 ``` 





---

#### 2.4 无牌车进出场，出口支付（V2）




**简要描述：** 

- 用于无牌车入场，无牌车出场，有牌车出场。
- 区别V1版本不用传递laneType。

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 scanLaneQrCode2          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |   |
|laneId  |int 	|否	|通道ID  	|通道信息接口中获取	|
|noPlate  |String 	|否	|无牌车   	|无ABC123 	|

 **请求示例**
```
{
	&quot;service&quot;: &quot;scanLaneQrCode2&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;82346f42-1244-46db-a752-64becd230229&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;laneId&quot;: 219
	},
	&quot;sign&quot;: &quot;5d4b219406c4992de133f21f8544ae05&quot;
}
 ```

**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |  |
|laneId  |int 	|否	|通道ID  	|		|
|laneType  |int 	|否	|通道类型  	|1-入 2-出|
|plate  |int 	|否	|车牌号  	|无牌车入场，出场车牌号|
|inQrResponse  |Object 	|否	|入场响应  	|laneType=1时，返回入场信息 |
|outQrResponse  |Object 	|否	|出场响应  	|laneType=2时，返回停车信息||

**入场响应（inQrResponse）详情**
- laneType=1时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|inLaneId  |String 	|否	|入场通道ID  	|		|
|inLaneName  |int 	|否	|入场通道名称  |	|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

**出场响应（outQrResponse）详情**
- laneType=2时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|stopTime  |String 	|否	|停车时长  	|		|
|stopTimeSecond  |String 	|否	|停车时间戳  |	|
|amount  |Double 	|否	|需交费  	|单位：分钟/元|
|totalFee	  |Double	|否	|总应收	 |   单位：分钟/元    |
|beforePay  |Double 	|否	|已缴费用  	|	单位：分钟/元	|
|discountVal  |String 	|否	|已优惠  	|	单位：分钟/元	|
|discountType  |int 	|否	|优惠券类型  	|1.金额（分钟） 2.时间（元）|
|recordId  |String 	|是	|平台进出场ID  	|		|
|localId  |String 	|是	|本地进出场ID  	|		|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

**出口支付返回示例**
 
 ``` 
	{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;laneId&quot;: 4761,
		&quot;laneType&quot;: 2,
		&quot;plate&quot;: &quot;粤B123991&quot;,
		&quot;inQrResponse&quot;: null,
		&quot;outQrResponse&quot;: {
			&quot;inTime&quot;: &quot;2023-10-21 11:15:59&quot;,
			&quot;stopTime&quot;: &quot;579小时1分钟&quot;,
			&quot;stopTimeSecond&quot;: &quot;2084415&quot;,
			&quot;amount&quot;: 580.0,
			&quot;totalFee&quot;: 580.0,
			&quot;beforePay&quot;: 0.0,
			&quot;discountVal&quot;: &quot;无&quot;,
			&quot;discountType&quot;: null,
			&quot;laneId&quot;: 4761,
			&quot;recordId&quot;: 3149852,
			&quot;localId&quot;: null,
			&quot;inPic&quot;: null
		}
	}
}
 ``` 





---

#### 入口追缴费用查询

**简要描述：** 

- 入场追缴，获取追缴费用。

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 EntranceRecovery          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车库ID	 |   |
|laneId  |String	|否	|通道ID	 |   |
|plate  |String	|否	|车牌	 |   |



**返回成功示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;parkId&quot;: 61582,
		&quot;parkName&quot;: &quot;时光、纯云&quot;,
		&quot;plate&quot;: &quot;粤QW1234&quot;,
		&quot;inTime&quot;: &quot;2025-03-12 17:13:04&quot;,
		&quot;outTime&quot;: &quot;2025-03-12 17:27:39&quot;,
		&quot;remark&quot;: &quot;停车场内未支付订单：于2025年03月12日17时13分入场，并在2025年03月12日17时27分离开，导致逃费金额共计0.01元&quot;,
		&quot;stopTime&quot;: &quot;15分钟&quot;,
		&quot;stopTimeSecond&quot;: &quot;875&quot;,
		&quot;amount&quot;: 0.01,
		&quot;totalFee&quot;: 0.01,
		&quot;beforePay&quot;: 0.0,
		&quot;discountVal&quot;: &quot;0.0&quot;,
		&quot;discountType&quot;: 0,
		&quot;laneId&quot;: 5212,
		&quot;recordId&quot;: null,
		&quot;localId&quot;: null,
		&quot;inPic&quot;: &quot;https://qc-park-bucket.oss-cn-shenzhen.aliyuncs.com//default/no_plate_in.png&quot;,
		&quot;outPic&quot;: &quot;https://qc-park-bucket.oss-cn-shenzhen.aliyuncs.com//default/no_plate_out.png&quot;
	}
}
 ``` 







---

### 3. 优惠减免

#### 业务流程

![](https://www.showdoc.com.cn/server/api/attachment/visitFile?sign=3704b2b00d7eccdd2a52cc8aea667671&amp;file=file.jpg)


---

#### 3.1 获取车辆信息接口

**简要描述：** 

- 根据车牌号获取车辆信息（临时车，月租车，储值车）

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 CarInfo          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|plate  |String 	|否	|车牌号  	|		||

 **请求示例**
```
{
	&quot;service&quot;: &quot;CarInfo&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;plate&quot;: &quot;粤GHT4TR&quot;
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|parkName  |String 	|否	|车场名称  	|		|
|plate  |String 	|否	|车牌号  	|		|
|carType  |int 	|否	|车型  	|1-临时车 2-月租车 3-储值车|
|carInfo  |Object 	|否	|车辆信息  	|根据车型显示不同内容 ||

 **车辆信息（carInfo）详情**
- carType=1时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|stopTime  |String 	|否	|停车时长  	|		|
|stopTimeSecond  |String 	|否	|停车时间戳  |	|
|amount  |Double 	|否	|需交费  	|单位：分钟/元|
|totalFee	  |Double	|否	|总应收	 |   单位：分钟/元    |
|beforePay  |Double 	|否	|已缴费用  	|	单位：分钟/元	|
|discountVal  |String 	|否	|已优惠  	|	单位：分钟/元	|
|discountType  |int 	|否	|优惠券类型  	|1.金额（分钟） 2.时间（元）|
|recordId  |int 	|是	|平台进出场ID  	|		|
|localId  |int 	|是	|本地进出场ID  	|		|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

- carType=2，carType=3时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|carId  |int	|否	|车场ID	 |       |
|chargeType  |int	|否	|计费类型ID	 |       |
|endDate  |String 	|否	|有效期止  	|	2021-10-03	|
|spaces  |int 	|否	|车位数  	|	|
|phone  |String 	|是	|电话  	|	|
|address  |String 	|是	|地址  	|	|
|lockStatus  |int 	|是	|锁车状态  	|1-锁车 2-未锁车|
|status  |int 	|是	|状态  	|1-启用 2-未生效 3-暂停|
|carSeatInfoList  |array 	|是	|车位信息List  	|储值车可为空 ||

**车位信息（CarSeatInfo）详情**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|seatNo  |String	|否	|车位编号	 |       |
|chargeType  |Integer 	|否	|计费车型ID  	|	|
|chargeName  |String 	|否	|计费车型name  	|	|
|dayCharge  |double 	|是	|日费用  	|	|
|monthCharge  |double	|是	|月费用  	|	|
|quarterCharge  |double |是	|季度费用  	|	|
|halfYearCharge  |double |是	|半年费用  	|	|
|yearCharge  |double|是	|年费用  	|储值车可为空 ||

**返回示例**
 
 ``` 
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;parkId&quot;: 10102,
        &quot;parkName&quot;: &quot;Boot测试车场&quot;,
        &quot;plate&quot;: &quot;粤GHT4TR&quot;,
        &quot;carType&quot;: 1,
        &quot;carInfo&quot;: {
            &quot;inTime&quot;: &quot;2021-10-28 17:48:13&quot;,
            &quot;stopTime&quot;: &quot;142小时35分钟&quot;,
            &quot;stopTimeSecond&quot;: &quot;513299&quot;,
            &quot;amount&quot;: 72.0,
            &quot;totalFee&quot;: 72.0,
            &quot;beforePay&quot;: 0.0,
            &quot;discountVal&quot;: &quot;无&quot;,
            &quot;discountType&quot;: null,
            &quot;laneId&quot;: null,
            &quot;recordId&quot;: 422571,
            &quot;localId&quot;: null,
            &quot;inPic&quot;: &quot;http://boot-park.oss-cn-beijing.aliyuncs.com/picture/10102/02E1702201409536/20211028/17/20211028_174813_138.jpg&quot;
        }
    }
}
 ``` 





---

#### 3.2 商家减免


**简要描述：** 

- 优惠减免接口（临时车，月租车，储值车）
- 优惠卷流程是，获取车辆信息- 商家减免，两个接口
- **该接口调用后，无需再调用支付通知接口**
**请求URL：** 
- ` /data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 DiscountCreate          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|parkingSerial  |String	|否	|停车流水, 标识具体某次停车事件, 需保证该停车场下唯一 ，该字段对应获取车牌接口recordId和localId必须一个不为空|       |
|plate  |String	|否	|车牌号	 |       |
|grantSerial  |String	|否	|优惠券派发流水，该字段写入接入方的UUId）	 |       |
|type  |Integer	|否	|优惠类型: 1.金额, 2.时长, 3.全免 |
|value  |Integer	|否	|当type=1时单位为分;当type=2时单位为分钟;	 |   |
|reason  |string	|是	|优惠给予原因, 例如:购物满300, 免费停车2小时. |       |
|storeName  |string	|否	|	商家名称 | 某某对接方，比如：某某充电减免 ||


** 请求示例**
```
{
	&quot;service&quot;: &quot;DiscountCreate&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;ee0be9c0-3c18-4b59-80ba-0551323570d5&quot;,
	&quot;orgId&quot;: &quot;Bootjyt&quot;,
	&quot;data&quot;: {
		&quot;grantSerial&quot;: &quot;44020400600000062304211200090110&quot;,
		&quot;plate&quot;: &quot;粤BA01096&quot;,
		&quot;storeName&quot;: &quot;中顺颐泉充电站-奥特迅&quot;,
		&quot;parkingSerial&quot;: &quot;2334823&quot;,
		&quot;type&quot;: 2,
		&quot;value&quot;: 120,
		&quot;parkId&quot;: 12105
	},
	&quot;sign&quot;: &quot;b9384fb5bf79c5422235c45bb5fde822&quot;
}
```
**返回示例**
```
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;减免成功&quot;
}
```


---

#### 3.3 查询优惠抵扣金额


**简要描述：** 

- 折扣后价格：表示在享受优惠后需要支付的金额
- 该接口是预减免接口（实际不会减免）
**请求URL：** 
- ` /data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 CarInfoDiscountDestory          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|plate  |String	|否	|车牌号	 |       |
|grantSerial  |String	|否	|优惠券派发流水，该字段写入接入方的唯一id即可	 |       |
|type  |Integer	|否	|优惠类型: 1.金额, 2.时长, 3.全免 |
|value  |Integer	|否	|当type=1时单位为分;当type=2时单位为分钟;	 |   |
|reason  |string	|是	|优惠给予原因, 例如:购物满300, 免费停车2小时. |       ||


** 请求示例**
````
{
	&quot;data&quot;: {
		&quot;grantSerial&quot;: &quot;9b06aa01-d365-4c5b-8535-0f9d9433b454&quot;,
		&quot;reason&quot;: &quot;测试&quot;,
		&quot;plate&quot;: &quot;粤B123JKJ&quot;,
		&quot;type&quot;: 1,
		&quot;value&quot;: 500,
		&quot;parkId&quot;: &quot;11609&quot;
	},
	&quot;service&quot;: &quot;CarInfoDiscountDestory&quot;,
	&quot;sign&quot;: &quot;5e75fb09536c3db7e61cceecdcb8a743&quot;,
	&quot;msgId&quot;: &quot;e78d0d69-1a31-4ffe-add2-49b4557235a5&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;
}
````
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|parkName  |String 	|否	|车场名称  	|		|
|plate  |String 	|否	|车牌号  	|		|
|carType  |int 	|否	|车型  	|1-临时车 2-月租车 3-储值车|
|carInfo  |Object 	|否	|车辆信息  	|根据车型显示不同内容 ||

 **车辆信息（carInfo）详情**
- carType=1时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|stopTime  |String 	|否	|停车时长  	|		|
|stopTimeSecond  |String 	|否	|停车时间戳  |	|
|amount  |Double 	|否	|需交费  	|单位：分钟/元|
|totalFee	  |Double	|否	|总应收	 |   单位：分钟/元    |
|beforePay  |Double 	|否	|已缴费用  	|	单位：分钟/元	|
|discountVal  |String 	|否	|已优惠  	|	单位：分钟/元	|
|discountType  |int 	|否	|优惠券类型  	|1.金额（分钟） 2.时间（元）|
|recordId  |int 	|是	|平台进出场ID  	|		|
|localId  |int 	|是	|本地进出场ID  	|		|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

- carType=2，carType=3时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|carId  |int	|否	|车场ID	 |       |
|chargeType  |int	|否	|计费类型ID	 |       |
|endDate  |String 	|否	|有效期止  	|	2021-10-03	|
|spaces  |int 	|否	|车位数  	|	|
|phone  |String 	|是	|电话  	|	|
|address  |String 	|是	|地址  	|	|
|lockStatus  |int 	|是	|锁车状态  	|1-锁车 2-未锁车|
|status  |int 	|是	|状态  	|1-启用 2-未生效 3-暂停|
|address  |List&lt;CarSeatInfo&gt; 	|是	|车位信息List  	|储值车可为空 ||

**车位信息（CarSeatInfo）详情**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|seatNo  |String	|否	|车位编号	 |       |
|chargeType  |Integer 	|否	|计费车型ID  	|	|
|chargeName  |String 	|否	|计费车型name  	|	|
|dayCharge  |double 	|是	|日费用  	|	|
|monthCharge  |double	|是	|月费用  	|	|
|quarterCharge  |double |是	|季度费用  	|	|
|halfYearCharge  |double |是	|半年费用  	|	|
|yearCharge  |double|是	|年费用  	|储值车可为空 ||

````
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;parkName&quot;: &quot;对外Api测试车场&quot;,
		&quot;plate&quot;: &quot;粤B123JKJ&quot;,
		&quot;carType&quot;: 1,
		&quot;carInfo&quot;: {
			&quot;inTime&quot;: &quot;2023-05-26 14:16:35&quot;,
			&quot;stopTime&quot;: &quot;483小时10分钟&quot;,
			&quot;stopTimeSecond&quot;: &quot;1739363&quot;,
			&quot;amount&quot;: 5.0,
			&quot;totalFee&quot;: 10.0,
			&quot;beforePay&quot;: 0.0,
			&quot;discountVal&quot;: &quot;5.0元&quot;,
			&quot;discountType&quot;: 2,
			&quot;laneId&quot;: null,
			&quot;recordId&quot;: 1855262,
			&quot;localId&quot;: null,
			&quot;inPic&quot;: null
		}
	}
}
````



---

#### 3.4 取消商家减免


**简要描述：** 

- 优惠卷流程是，取消商家减免
**请求URL：** 
- ` /data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 DiscountDestory          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|grantSerial  |String	|否	|优惠券派发流水）	 |       ||



** 请求示例**
```
{
	&quot;service&quot;: &quot;DiscountDestory&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;3bc1689d-fb2c-476a-b88a-2af26e0d4d8e&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: &quot;11609&quot;,
		&quot;grantSerial&quot;: &quot;A89611191ACE4291BFD2BB60BF874DF7&quot;
	},
	&quot;sign&quot;: &quot;61c68b4844c2b1aec801a1b23473f0d9&quot;
}
```
**返回示例**
```
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;减免劵销毁成功&quot;
}
```


---

### 4. 登记车

#### 4.1 查询月租车

**简要描述：** 

- 月租车模块，查询一条月租车/储值车信息

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:------------------------------    |:-------    |:--- |-- -|------      |
|service  |String	|否|服务名称	 |	 getParkCar          |
|version  |String 	|否| 版本  	|	01	|
|msgId    |String	|否|消息ID（唯一）   |	 |
|orgId    |String 	|否|机构ID（分配）    |	 		 |
|data     |Object	|否| 具体业务接口json对象   |	  |
|sign     |String 	|否| 请求签名  |请查看签名生成规则||

&lt;br/&gt;&lt;br/&gt;
**查询月租车/储值车业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |      10102 |
|plate  |String	|否	|车牌号	 |      粤B12345 |

 **请求示例**

 ``` 
 {
  &quot;service&quot;: &quot;getParkCar&quot;,
  &quot;version&quot;: &quot;01&quot;,
  &quot;msgId&quot;: &quot;f719b06d-210b-4989-9c7f-02e85f22fe01&quot;,
  &quot;orgId&quot;: &quot;BOOT01&quot;,
  &quot;data&quot;: {
    &quot;parkId&quot;: 11480,
    &quot;plate&quot;: &quot;京B12782&quot;
  },
  &quot;sign&quot;: &quot;b0f7956c1733121a4ab641c730d30c8f&quot;
} 
 ```
 **返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
| code  |String	|否	|状态码	 |	0-正常，其他-异常       |
| msg   |String 	|是	|状态码处理描述  	|错误描述	|
| retData     |Object	|否   | 具体业务接口json对象   |	  |
| parkId  |int	|是	|总车位    |	|
| parkName  |String	|是	|车场名称    |	|
| name  |String	|是	|车主姓名    |	|
| phone  |String	|是	|车主电话    |	|
| address  |String	|是	|车主地址    |	|
| plate  |String	|是	|车牌号    |	|
| chargeType  |int	|是	|计费类型    |	|
| chargeTypeName  |String	|是	|计费类型名称    |	|
| type  |int	|是	|1.临时车 ，2.月租车 ，3.储值车   |	|
| admin  |String	|是	|操作员    |	|
| monthCharge  |double	|是	|余额（用于储值车）    |	|
| beginDate  |String	|是	|有效期开始时间    |	|
| endDate  |String	|是	|有效期结束时间   | 	|
| status  |String	|是	| 1 正常启用 2 未生效 3 暂停 4 过期  5 表示线上注册   | ||

  **返回示例（月租车）**
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;parkName&quot;: &quot;对外Api测试车场&quot;,
		&quot;name&quot;: &quot;大C&quot;,
		&quot;phone&quot;: &quot;&quot;,
		&quot;address&quot;: &quot;&quot;,
		&quot;plate&quot;: &quot;粤B88866&quot;,
		&quot;chargeType&quot;: 4,
		&quot;chargeTypeName&quot;: &quot;VIP月&quot;,
		&quot;type&quot;: 2,
		&quot;admin&quot;: &quot;第三方对接&quot;,
		&quot;monthCharge&quot;: 0.0,
		&quot;beginDate&quot;: &quot;2023-07-14&quot;,
		&quot;endDate&quot;: &quot;2023-09-13&quot;,
		&quot;status&quot;: 3
	}
}
 ``` 
   **返回示例（储值车）**
 ``` 
 {
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;parkName&quot;: &quot;对外Api测试车场&quot;,
		&quot;name&quot;: &quot;测试&quot;,
		&quot;phone&quot;: &quot;12345645654564&quot;,
		&quot;address&quot;: &quot;1&quot;,
		&quot;plate&quot;: &quot;粤A88888&quot;,
		&quot;chargeType&quot;: 3,
		&quot;chargeTypeName&quot;: &quot;储值车&quot;,
		&quot;type&quot;: 3,
		&quot;admin&quot;: &quot;系统管理员&quot;,
		&quot;monthCharge&quot;: 18.0,
		&quot;beginDate&quot;: &quot;&quot;,
		&quot;endDate&quot;: &quot;&quot;
	}
}
 ``` 









---

#### 4.2 编辑月租车（添加，删除，修改）

**简要描述：** 

- 月租车模块，该接口根据业务字段：operationType区分操作类型（增删改）

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:------------------------------    |:-------    |:--- |-- -|------      |
|service  |String	|否|服务名称	 |	 parkCar          |
|version  |String 	|否| 版本  	|	01	|
|msgId    |String	|否|消息ID（唯一）   |	 |
|orgId    |String 	|否|机构ID（分配）    |	 		 |
|data     |Object	|否| 具体业务接口json对象   |	  |
|sign     |String 	|否| 请求签名  |请查看签名生成规则||

&lt;br/&gt;&lt;br/&gt;
**删除月租车/储值车业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|operationType  |String	|否	|操作类型	 | DELETE |
|parkId  |Integer	|否	|车场ID	 |      10102 |
|plate  |String	|是	|车牌号	 |      粤B12345 |

 **请求示例**
```
{
	&quot;service&quot;: &quot;parkCar&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;f719b06d-210b-4989-9c7f-02e85f22fe01&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11480,
		&quot;plate&quot;: &quot;沪C22222&quot;,
		&quot;operationType&quot;: &quot;DELETE&quot;
	},
	&quot;sign&quot;: &quot;eaacf1b18ddf057f557e948cb9bd557b&quot;
}
 ```

**返回实例**
```
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;删除成功&quot;
}
```
&lt;br/&gt;&lt;br/&gt;&lt;br/&gt;&lt;br/&gt;
**新增月租车/储值车业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|operationType  |String	|否	|操作类型	 | INSERT |
|parkId  |Integer	|否	|车场ID	 |      11480 |
|plate  |String	|否	|车牌号	 |      粤B12345 |
|name  |String	|否	|车主姓名	 |      陈先生 |
|address  |String	|否	|地址	 |      广东省深圳市南山区 |
|phone  |String	|否	|手机号	 |      13145206630 |
|chargeType  |Integer	|否	|计费类型（获取计费类型接口）	 |   1    |
|monthCharge  |Double	|否	|（月租车：月卡费用，储值车：余额）	 |      110.0 |
|beginDate  |String	|否	|开始时间	 |      2022-08-31 |
|endDate  |String	|否	|结束时间	 |      2022-09-31 |
|seatType  |String	|否	| 1-公共车位 2-私家车位	 |      1 |
|carType  |Integer	|否	|1-小车 2-大车 3-超大车 4-摩托车 5-其它车）	 |      1 |
|isPayOnline  |Integer	|否	|1-允许在线缴费  0-不允许在线缴费	 |     默认： 1 |
**请求实例**
 
 ``` 
{
	&quot;service&quot;: &quot;parkCar&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;f719b06d-210b-4989-9c7f-02e85f22fe01&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11480,
		&quot;plate&quot;: &quot;京B12782&quot;,
		&quot;name&quot;: &quot;pasika&quot;,
		&quot;address&quot;: &quot;深圳市&quot;,
		&quot;phone&quot;: &quot;13148762240&quot;,
		&quot;chargeType&quot;: 3,
		&quot;monthCharge&quot;: 100.0,
		&quot;beginDate&quot;: &quot;2022-08-31&quot;,
		&quot;endDate&quot;: &quot;2022-09-31&quot;,
		&quot;seatType&quot;: &quot;1&quot;,
		&quot;carType&quot;: 1,
		&quot;operationType&quot;: &quot;INSERT&quot;,
		&quot;isPayOnline&quot;: 1
	},
	&quot;sign&quot;: &quot;c3b950ed5170db5523619bb65c8ec849&quot;
}
 ``` 
**返回实例**
```
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;新增成功&quot;
}
```

**修改月租车/储值车业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|:----    |:-------    |:--- |-- -|------      |
|operationType  |String	|否	|操作类型	 | UPDATE |
|parkId  |Integer	|否	|车场ID	 |      11480 |
|plate  |String	|否	|车牌号	 |      粤B12345 |
|name  |String	|否	|车主姓名	 |      陈先生 |
|address  |String	|否	|地址	 |      广东省深圳市南山区 |
|phone  |String	|否	|手机号	 |      13145206630 |
|chargeType  |Integer	|否	|计费类型	 |      计费类接口返回 |
|monthCharge  |Double	|否	|（月租车：月卡费用，储值车：余额）	 |      110.0 |
|beginDate  |String	|否	|开始时间	 | 2022-08-31 |
|endDate  |String	|否	|结束时间	 | 2022-09-31 |
|seatType  |Integer	|否	|1-公共车位 2-私家车位	 |      1 |
|carType  |Integer	|否	|1-小车 2-大车 3-超大车 4-摩托车 5-其它）	 |      1 |
|isPayOnline  |Integer	|否	|1-允许在线缴费  0-不允许在线缴费	 |     默认： 1 |
**请求实例**
 
 ``` 
{
	&quot;service&quot;: &quot;parkCar&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;f719b06d-210b-4989-9c7f-02e85f22fe01&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11480,
		&quot;plate&quot;: &quot;京B12782&quot;,
		&quot;name&quot;: &quot;pasika&quot;,
		&quot;address&quot;: &quot;深圳市&quot;,
		&quot;phone&quot;: &quot;13148762240&quot;,
		&quot;chargeType&quot;: 3,
		&quot;monthCharge&quot;: 2000.0,
		&quot;beginDate&quot;: &quot;2022-08-31&quot;,
		&quot;endDate&quot;: &quot;2022-09-31&quot;,
		&quot;seatType&quot;: &quot;1&quot;,
		&quot;carType&quot;: 1,
		&quot;operationType&quot;: &quot;UPDATE&quot;,
		&quot;isPayOnline&quot;: 1
	},
	&quot;sign&quot;: &quot;6952fc99e92c3a7dbd0f8e784d6e4040&quot;
}
 ``` 
**返回实例**
```
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;修改成功&quot;
}
```




---

### 5. 预约车

#### 业务流程


![](https://www.showdoc.com.cn/server/api/attachment/visitFile?sign=980ac65d10cd5b4a9d7f0f0dceb1d48e&amp;file=file.jpg)

---

#### 5.1.添加车场业主信息

**简要描述：**

*   添加车场业主信息（配合添加预约记录配合使用）

**请求URL：**

*   `/data/centre/api/v1`

**请求方式：**

*   POST

**公共请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| service | String | 否 | 服务名称 | addOwner |
| version | String | 否 | 版本 | 01 |
| msgId | String | 否 | 消息ID（唯一） |  |
| orgId | String | 否 | 机构ID（分配） |  |
| data | Object | 否 | 具体业务接口json对象 |  |
| sign | String | 否 | 请求签名 | 请查看签名生成规则 |

**业务请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| parkId | Integer | 否 | 车场ID | 10000 |
| phone | String | 否 | 手机号 | 131485266330 |
| name | String | 否 | 业主姓名 | 某总 |
| address | String | 否 | 地址 | 广东省深圳市世纪广场 |


**请求示例**

````
{
	&quot;service&quot;: &quot;getOwner&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;phone&quot;: &quot;131485266330&quot;,
		&quot;name&quot;: &quot;某总&quot;,
		&quot;address&quot;: &quot;广东省深圳市世纪广场&quot;
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
````
    

**返回参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| parkId | int | 否 | 车场ID | 10000 |
| name | String | 否 | 车场名称 | 陈太太 |
| phone | String | 否 | 手机号 | 131485266330 |
| address | int | 否 | 地址 | 广东省 深圳市 龙岗区 |
| startDate | String | 否 | 预约开始时段 | Null |
| endDate | String | 否 | 预约结束时段 | Null |

**返回成功实例**


 ``` 
 {
  &quot;retCode&quot;: 0,
  &quot;retMsg&quot;: null,
  &quot;retData&quot;: {
    &quot;parkId&quot;: 11481,
    &quot;name&quot;: &quot;pasika&quot;,
    &quot;phone&quot;: &quot;13148762240&quot;,
    &quot;address&quot;: &quot;Luohu, Shenzhen City, Guangdong Province&quot;,
    &quot;startDate&quot;: null,
    &quot;endDate&quot;: null
  }
} 

 ```

**返回失败实例**
 ``` 
{
	&quot;retCode&quot;: 400,
	&quot;retMsg&quot;: &quot;手机号为空&quot;,
	&quot;retData&quot;: null
}
 ```

---

#### 5.2 添加预约记录

**简要描述：**

*   添加预约记录

**请求URL：**

*   `/data/centre/api/v1`

**请求方式：**

*   POST

**公共请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| service | String | 否 | 服务名称 | addvisitor |
| version | String | 否 | 版本 | 01 |
| msgId | String | 否 | 消息ID（唯一） |  |
| orgId | String | 否 | 机构ID（分配） |  |
| data | Object | 否 | 具体业务接口json对象 |  |
| sign | String | 否 | 请求签名 | 请查看签名生成规则 |

**业务请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| parkId | Integer | 否 | 车场ID | 10000 |
| plate | String | 否 | 车牌号 | 粤B12345 |
| visitorName | String | 否 | 预约人姓名 | 访客 |
| visitorPhone | String | 否 | 预约人手机号 | |
| name | String | 否 | 业主姓名 | 该字段要与新增业主信息姓名匹配 |
| phone	 | String | 否 | 业主手机号 | 该字段要与新增业主信息手机号匹配 |
| reason | String | 是 | 备注 |  |
| startTime | String | 否 | 预约开始时间 | 2022-08-15 11:56:32 |
| endTime | String | 否 | 预约结束时间 | 2022-08-15 19:56:32 |

**请求示例**
```
{
	&quot;service&quot;: &quot;addvisitor&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;0212c897-aa60-4d04-9cb0-bd1eac1fed38&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11480,
		&quot;plate&quot;: &quot;粤B12350&quot;,
		&quot;visitorName&quot;: &quot;小甘&quot;,
		&quot;visitorPhone&quot;: &quot;1866965524&quot;,
		&quot;name&quot;: &quot;pasika&quot;,
		&quot;phone&quot;: &quot;13148762240&quot;,
		&quot;reason&quot;: null,
		&quot;startTime&quot;: &quot;2022-08-15 11:56:32&quot;,
		&quot;endTime&quot;: &quot;2022-08-15 23:59:59&quot;
	},
	&quot;sign&quot;: &quot;a0b879c76bd89bdd7c961739953b0a07&quot;
}
```
    


**返回成功实例**


 ``` 
 {
  &quot;retCode&quot;: 0,
  &quot;retMsg&quot;: null,
  &quot;retData&quot;: &quot;成功&quot;
} 

 ```



**返回失败实例**

 ``` 
 {
  &quot;retCode&quot;: -4,
  &quot;retMsg&quot;: &quot;业主信息不存在&quot;,
  &quot;retData&quot;: &quot;null&quot;
} 

 ```




---

#### 5.3 添加预约记录V2（返回预约id）


**简要描述：**

*   添加预约记录（返回预约id）,需要取消预约接入该接口

**请求URL：**

*   `/data/centre/api/v1`

**请求方式：**

*   POST

**公共请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| service | String | 否 | 服务名称 | addvisitorV2 |
| version | String | 否 | 版本 | 01 |
| msgId | String | 否 | 消息ID（唯一） |  |
| orgId | String | 否 | 机构ID（分配） |  |
| data | Object | 否 | 具体业务接口json对象 |  |
| sign | String | 否 | 请求签名 | 请查看签名生成规则 |

**业务请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| parkId | Integer | 否 | 车场ID | 10000 |
| plate | String | 否 | 车牌号 | 粤B12345 |
| visitorName | String | 否 | 预约人姓名 | 访客 |
| visitorPhone | String | 否 | 预约人手机号 | |
| name | String | 否 | 业主姓名 | 该字段要与新增业主信息姓名匹配 |
| phone	 | String | 否 | 业主手机号 | 该字段要与新增业主信息手机号匹配 |
| reason | String | 是 | 备注 |  |
| startTime | String | 否 | 预约开始时间 | 2022-08-15 11:56:32 |
| endTime | String | 否 | 预约结束时间 | 2022-08-15 19:56:32 |

**请求示例**
```
{
	&quot;service&quot;: &quot;addvisitorV2&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;b34f8b67-e7ab-4ad5-8aa4-32408f6dde61&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 13646,
		&quot;plate&quot;: &quot;粤B12345&quot;,
		&quot;visitorName&quot;: &quot;小甘&quot;,
		&quot;visitorPhone&quot;: &quot;13148762240&quot;,
		&quot;name&quot;: &quot;pasika&quot;,
		&quot;phone&quot;: &quot;13148762240&quot;,
		&quot;reason&quot;: &quot;测试&quot;,
		&quot;startTime&quot;: &quot;2023-06-14 17:58:11&quot;,
		&quot;endTime&quot;: &quot;2023-06-14 17:58:11&quot;
	},
	&quot;sign&quot;: &quot;7c3489ec832617e6a97b8a2a8f8211a5&quot;
}
```
    

**返回参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| businessId | int | 否 | 业务id（取消预约记录时需要） | 10 |
| plate | int | 否 | 车牌号 | 粤B12345 |
| parkId | String | 否 | 车场id | 13646 |




**返回成功实例**


 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;businessId&quot;: 10,
		&quot;plate&quot;: &quot;粤B12345&quot;,
		&quot;parkId&quot;: 13646
	}
}

 ```



**返回失败实例**

 ``` 
 {
  &quot;retCode&quot;: -4,
  &quot;retMsg&quot;: &quot;业主信息不存在&quot;,
  &quot;retData&quot;: &quot;null&quot;
} 

 ```




---

#### 5.4 添加/修改预约记录V3


**简要描述：**

*   添加预约记录（返回预约id）,需要取消预约接入该接口
*   修改预约记录，区别于业务请求参数多传一个id(与返回businessId一致)

**请求URL：**

*   `/data/centre/api/v1`

**请求方式：**

*   POST

**公共请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| service | String | 否 | 服务名称 | addvisitorV3 |
| version | String | 否 | 版本 | 01 |
| msgId | String | 否 | 消息ID（唯一） |  |
| orgId | String | 否 | 机构ID（分配） |  |
| data | Object | 否 | 具体业务接口json对象 |  |
| sign | String | 否 | 请求签名 | 请查看签名生成规则 |

**业务请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| id | Integer | 是 | ID | 10000,与返回businessId一致，不为空表示修改 |
| parkId | Integer | 否 | 车场ID | 10000 |
| plate | String | 否 | 车牌号 | 粤B12345 |
| visitorType | Integer | 否 | 预约类型 | 1：访客 2：业主 |
| visitorName | String | 是 | 访客姓名 | visitorType=1，不能为空 |
| visitorPhone | String | 是 | 访客手机号 | visitorType=1，不能为空 |
| name | String | 是 | 业主姓名 | 该字段要与新增业主信息姓名匹配，visitorType=2，不能为空 |
| phone	 | String | 是 | 业主手机号 | 该字段要与新增业主信息手机号匹配，visitorType=2，不能为空 |
| reason | String | 是 | 备注 |  |
| startTime | String | 否 | 预约开始时间 | 2022-08-15 11:56:32 |
| endTime | String | 否 | 预约结束时间 | 2022-08-15 19:56:32 |

**请求示例(访客预约)**
```
{
    &quot;service&quot;: &quot;addvisitorV3&quot;,
    &quot;version&quot;: &quot;01&quot;,
    &quot;msgId&quot;: &quot;b34f8b67-e7ab-4ad5-8aa4-32408f6dde61&quot;,
    &quot;orgId&quot;: &quot;BTTEST01&quot;,
    &quot;data&quot;: {
        &quot;parkId&quot;: 11609,
        &quot;plate&quot;: &quot;粤B12345&quot;,
        &quot;visitorType&quot;: 1,
        &quot;visitorName&quot;: &quot;小甘&quot;,
        &quot;visitorPhone&quot;: &quot;13148762240&quot;,
        &quot;reason&quot;: &quot;测试&quot;,
        &quot;startTime&quot;: &quot;2023-06-14 17:58:11&quot;,
        &quot;endTime&quot;: &quot;2023-06-14 17:58:11&quot;
    },
    &quot;sign&quot;: &quot;0f28b4af2ac8c0327d3692c82cfe3126&quot;
}
```
**请求示例(业主预约)**
```
{
    &quot;service&quot;: &quot;addvisitorV3&quot;,
    &quot;version&quot;: &quot;01&quot;,
    &quot;msgId&quot;: &quot;b34f8b67-e7ab-4ad5-8aa4-32408f6dde61&quot;,
    &quot;orgId&quot;: &quot;BTTEST01&quot;,
    &quot;data&quot;: {
        &quot;parkId&quot;: 11609,
        &quot;plate&quot;: &quot;粤B12345&quot;,
        &quot;visitorType&quot;: 2,
        &quot;name&quot;: &quot;pasika&quot;,
        &quot;phone&quot;: &quot;13148762240&quot;,
        &quot;reason&quot;: &quot;测试&quot;,
        &quot;startTime&quot;: &quot;2023-06-14 17:58:11&quot;,
        &quot;endTime&quot;: &quot;2023-06-14 17:58:11&quot;
    },
    &quot;sign&quot;: &quot;0f28b4af2ac8c0327d3692c82cfe3126&quot;
}
```
    

**返回参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| businessId | int | 否 | 业务id（取消预约记录时需要） | 10 |
| plate | int | 否 | 车牌号 | 粤B12345 |
| parkId | String | 否 | 车场id | 13646 |




**返回成功实例**


 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;businessId&quot;: 10,
		&quot;plate&quot;: &quot;粤B12345&quot;,
		&quot;parkId&quot;: 13646
	}
}

 ```



**返回失败实例**

 ``` 
 {
  &quot;retCode&quot;: -4,
  &quot;retMsg&quot;: &quot;业主信息不存在&quot;,
  &quot;retData&quot;: &quot;null&quot;
} 

 ```





---

#### 5.5 查询预约记录

**简要描述：**

*   查询预约记录

**请求URL：**

*   `/data/centre/api/v1`

**请求方式：**

*   POST

**公共请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| service | String | 否 | 服务名称 | checkVisitor |
| version | String | 否 | 版本 | 01 |
| msgId | String | 否 | 消息ID（唯一） |  |
| orgId | String | 否 | 机构ID（分配） |  |
| data | Object | 否 | 具体业务接口json对象 |  |
| sign | String | 否 | 请求签名 | 请查看签名生成规则 |

**业务请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| parkId | Integer | 否 | 车场ID | 13646 |


**请求示例**
```
{
	&quot;service&quot;: &quot;checkVisitor&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;5f7922af-978a-4f08-847d-4376092d1ab2&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 13646
	},
	&quot;sign&quot;: &quot;19213aa24b2d93fc1beb5d841193b962&quot;
}
```
    


**返回参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| id | int | 否 | 车场ID | 业务Id |
| parkId | int | 否 | 车场ID | 10000 |
| plate | String | 否 | 车牌号 | 粤B123456 |
| name | String | 否 | 业主姓名 | pasika |
| phone | int | 否 | 手机号 | 131485266330 |
| address | String | 否 | 预约人地址 | 测试 |
| reason | String | 否 | 来访说明 | 登门拜访 |
| startTime | String | 否 | 预约开始时段 | 2023-06-13 09:52:16 |
| endTime | String | 否 | 预约结束时段 | 2023-06-14 09:52:16 |
| visitorName | String | 否 | 预约人姓名 | 刘总 |
| visitorPhone | String | 否 | 预约人手机号 | 123123123567 |

**返回成功实例**

 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: [{
		&quot;id&quot;: 8,
		&quot;parkId&quot;: 13646,
		&quot;createTime&quot;: &quot;2023-06-13 09:52:32&quot;,
		&quot;plate&quot;: &quot;津C12312&quot;,
		&quot;name&quot;: &quot;pasika&quot;,
		&quot;phone&quot;: &quot;13148762240&quot;,
		&quot;address&quot;: null,
		&quot;reason&quot;: &quot;&quot;,
		&quot;startTime&quot;: &quot;2023-06-13 09:52:16&quot;,
		&quot;endTime&quot;: &quot;2023-06-13 23:59:59&quot;,
		&quot;visitorName&quot;: &quot;1&quot;,
		&quot;visitorPhone&quot;: &quot;13148762240&quot;
	}, {
		&quot;id&quot;: 9,
		&quot;parkId&quot;: 13646,
		&quot;createTime&quot;: &quot;2023-06-13 10:12:15&quot;,
		&quot;plate&quot;: &quot;粤A12366&quot;,
		&quot;name&quot;: &quot;pasika&quot;,
		&quot;phone&quot;: &quot;13148762240&quot;,
		&quot;address&quot;: null,
		&quot;reason&quot;: &quot;&quot;,
		&quot;startTime&quot;: &quot;2023-06-13 10:11:58&quot;,
		&quot;endTime&quot;: &quot;2023-06-13 23:59:59&quot;,
		&quot;visitorName&quot;: &quot;xg&quot;,
		&quot;visitorPhone&quot;: &quot;12312312&quot;
	}]
}

 ```





---

#### 5.6 取消预约记录


**简要描述：**

*   取消预约记录

**请求URL：**

*   `/data/centre/api/v1`

**请求方式：**

*   POST

**公共请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| service | String | 否 | 服务名称 | delVisitor |
| version | String | 否 | 版本 | 01 |
| msgId | String | 否 | 消息ID（唯一） |  |
| orgId | String | 否 | 机构ID（分配） |  |
| data | Object | 否 | 具体业务接口json对象 |  |
| sign | String | 否 | 请求签名 | 请查看签名生成规则 |

**业务请求参数**

| 字段 | 类型 | 空 | 描述 | 实例 |
| --- | --- | --- | --- | --- |
| parkId | Integer | 否 | 车场ID | 13646 |
| plate | String | 否 | 车牌号 | 津C12312 |
| businessId | String | 否 | 预约业务Id（查询预约记录接口） | 8 |


**请求示例**
```
{
	&quot;service&quot;: &quot;delVisitor&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;234c572e-0007-4809-a9a1-83483545289c&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 13646,
		&quot;businessId&quot;: 8,
		&quot;plate&quot;: &quot;津C12312&quot;
	},
	&quot;sign&quot;: &quot;5c4e47d8855294d1f146ead41582e6c7&quot;
}
```
    


**返回成功实例**


 ``` 
 {
  &quot;retCode&quot;: 0,
  &quot;retMsg&quot;: null,
  &quot;retData&quot;: &quot;删除成功&quot;
} 

 ```

**返回失败实例**

 ``` 
 {
  &quot;retCode&quot;: -4,
  &quot;retMsg&quot;: &quot;删除失败&quot;,
  &quot;retData&quot;: &quot;null&quot;
} 

 ```




---

### 6. 黑名单

#### 6.1 查询黑名单

**简要描述：** 

- 黑名单模块，查询所有黑名单车辆

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:------------------------------    |:-------    |:--- |-- -|------      |
|service  |String	|否|服务名称	 |	 QueryParkBlacklist          |
|version  |String 	|否| 版本  	|	01	|
|msgId    |String	|否|消息ID（唯一）   |	 |
|orgId    |String 	|否|机构ID（分配）    |	 		 |
|data     |Object	|否| 具体业务接口json对象   |	  |
|sign     |String 	|否| 请求签名  |请查看签名生成规则||


|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |      10102 |
|code  |String	|是	|code	 |      粤B12345 |

 **请求示例**

 ``` 
{
	&quot;service&quot;: &quot;QueryParkBlacklist&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;2c03b6f9-6e9c-4f50-8976-15dbc562ddf6&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;code&quot;: &quot;1658395029&quot;
	},
	&quot;sign&quot;: &quot;0cf59b21502599a7511d54aded0ec142&quot;
}
 ```
 **返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
| blackListId  |String	|否	|业务id	 |	黑名单业务id（删除携带）       |
| plate  |String	|是	|车牌号    |	|
| createTime  |int	|是	|创建时间    |	|
| admin  |String	|是	|操作员    |	|
| reason  |double	|是	|原因    |	||

  **返回示例**
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: [{
		&quot;parkId&quot;: 11609,
		&quot;blackListId&quot;: 24578,
		&quot;plate&quot;: &quot;粤B12345&quot;,
		&quot;createTime&quot;: &quot;2023-08-28 10:39:12&quot;,
		&quot;admin&quot;: &quot;测试&quot;,
		&quot;reason&quot;: &quot;测试&quot;
	}, {
		&quot;parkId&quot;: 11609,
		&quot;blackListId&quot;: 24584,
		&quot;plate&quot;: &quot;粤B123457&quot;,
		&quot;createTime&quot;: &quot;2023-08-28 10:54:53&quot;,
		&quot;admin&quot;: &quot;测试&quot;,
		&quot;reason&quot;: &quot;测试&quot;
	}, {
		&quot;parkId&quot;: 11609,
		&quot;blackListId&quot;: 24496,
		&quot;plate&quot;: &quot;粤BN1231&quot;,
		&quot;createTime&quot;: &quot;2023-08-27 00:26:57&quot;,
		&quot;admin&quot;: &quot;44063464&quot;,
		&quot;reason&quot;: &quot;测试啊&quot;
	}]
}
 ``` 











---

#### 6.2 编辑黑名单（添加，删除）

**简要描述：** 

- 黑名单模块，该接口根据业务字段：operationType区分操作类型（增删改）

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:------------------------------    |:-------    |:--- |-- -|------      |
|service  |String	|否|服务名称	 |	 ControlsParkBlacklist          |
|version  |String 	|否| 版本  	|	01	|
|msgId    |String	|否|消息ID（唯一）   |	 |
|orgId    |String 	|否|机构ID（分配）    |	 		 |
|data     |Object	|否| 具体业务接口json对象   |	  |
|sign     |String 	|否| 请求签名  |请查看签名生成规则||


**新增黑名单业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |      10102 |
|plate  |String	|否	|车牌号	 |      粤B123456 |
|operationType  |String	|否	|操作类型	 | INSERT |
|reason  |Integer	|是	|拉黑原因	 |      10102 |
|admin  |String	|是	|操作员	 |      测试 |

**请求实例**
 
 ``` 
{
	&quot;service&quot;: &quot;ControlsParkBlacklist&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;ad4e0908-d996-40db-bbe6-ce733e5cc572&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;plate&quot;: &quot;粤B123457&quot;,
		&quot;operationType&quot;: &quot;INSERT&quot;,
		&quot;reason&quot;: &quot;测试&quot;,
		&quot;admin&quot;: &quot;第三方对接&quot;
	},
	&quot;sign&quot;: &quot;85ae1fcd4a3da5f2a397819408066e64&quot;
}
 ``` 
  **返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
| blackListId  |String	|否	|业务id	 |	黑名单业务id（删除携带）       |
| plate  |String	|是	|车牌号    |	|
| parkId  |String	|是	|车场id    |	||
**返回实例**
```
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;blackListId&quot;: 24584,
		&quot;plate&quot;: &quot;粤B123457&quot;,
		&quot;parkId&quot;: &quot;11609&quot;
	}
}
```
&lt;br/&gt;&lt;br/&gt;&lt;br/&gt;&lt;br/&gt;

**删除黑名单业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |      10102 |
|blackListId  |Integer	|否	|黑名单业务Id	 |      24579 |
|plate  |String	|否	|车牌号	 |      粤B123456 |
|operationType  |String	|否	|操作类型	 | DELETE |
|reason  |Integer	|是	|车场ID	 |      10102 |
|admin  |String	|是	|保证	 |      测试 |

 **请求示例**
```
{
	&quot;service&quot;: &quot;ControlsParkBlacklist&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;f0053c36-88ad-4e40-a0a8-3949a5455a54&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;blackListId&quot;: 24579,
		&quot;parkId&quot;: 11609,
		&quot;plate&quot;: &quot;粤B123456&quot;,
		&quot;operationType&quot;: &quot;DELETE&quot;,
		&quot;reason&quot;: null,
		&quot;admin&quot;: &quot;测试&quot;
	},
	&quot;sign&quot;: &quot;eb237f8afc93ecd60c4063bf09204184&quot;
}
 ```

**返回实例**
```
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;删除成功&quot;
}
```




---

### 7. 操作相机

#### 7.1 远程开闸

**简要描述：** 

- 远程开闸（注意该接口是强制开闸，不会进入进出流程）

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 RemoteOpenGate          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|laneId  |String 	|否	|通道ID  	|		|
|admin  |String 	|否	|操作员  	|		||


 **请求示例**
```
{
	&quot;service&quot;: &quot;RemoteOpenGate&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;618a41c8-36a5-4eba-b56f-5ed817685689&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;laneId&quot;: 4760,
		&quot;admin&quot;: &quot;对外管理员&quot;
	},
	&quot;sign&quot;: &quot;a67d09e11e3a009026bacdaee6ca3de8&quot;
}
 ```

**返回示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;发送开闸成功&quot;
}
 ``` 






---

#### 7.2 远程关闸

**简要描述：** 

- 远程关闸（注意该接口是强制关闸，不会进入进出流程）

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 RemoteCloseGate          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|laneId  |String 	|否	|通道ID  	|		|
|admin  |String 	|否	|操作员  	|		||


 **请求示例**
```
{
	&quot;service&quot;: &quot;RemoteCloseGate&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;618a41c8-36a5-4eba-b56f-5ed817685689&quot;,
	&quot;orgId&quot;: &quot;BTTEST01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;laneId&quot;: 4760,
		&quot;admin&quot;: &quot;对外管理员&quot;
	},
	&quot;sign&quot;: &quot;a67d09e11e3a009026bacdaee6ca3de8&quot;
}
 ```

**返回示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;发送关闸成功&quot;
}
 ``` 






---

### 8. 累计费用

#### 1.获取车辆信息


**简要描述：** 

- 根据车牌号获取车辆信息（临时车，月租车，储值车）

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 CarInfo          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|plate  |String 	|否	|车牌号  	|		||

 **请求示例**
```
{
	&quot;service&quot;: &quot;CarInfo&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;cdd03282-39d4-46e3-b0f9-1590cce99252&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;plate&quot;: &quot;粤GHT4TR&quot;
	},
	&quot;sign&quot;: &quot;f813329464e31f1c9ab54b83b556862f&quot;
}
 ```
**返回参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |       |
|parkName  |String 	|否	|车场名称  	|		|
|plate  |String 	|否	|车牌号  	|		|
|carType  |int 	|否	|车型  	|1-临时车 2-月租车 3-储值车|
|carInfo  |Object 	|否	|车辆信息  	|根据车型显示不同内容 ||

 **车辆信息（carInfo）详情**
- carType=1时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|inTime	  |int	|否	|入场时间	 |       |
|stopTime  |String 	|否	|停车时长  	|		|
|stopTimeSecond  |String 	|否	|停车时间戳  |	|
|amount  |Double 	|否	|需交费  	|单位：分钟/元|
|totalFee	  |Double	|否	|总应收	 |   单位：分钟/元    |
|beforePay  |Double 	|否	|已缴费用  	|	单位：分钟/元	|
|discountVal  |String 	|否	|已优惠  	|	单位：分钟/元	|
|discountType  |int 	|否	|优惠券类型  	|1.金额（分钟） 2.时间（元）|
|recordId  |int 	|是	|平台进出场ID  	|	recordId和localId必须一个不为空	|
|localId  |int 	|是	|本地进出场ID  	|	recordId和localId必须一个不为空	|
|inPic  |String 	|是	|入场图片  	|	图片HTTP链接地址	||

- carType=2，carType=3时,响应参数

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|carId  |int	|否	|车场ID	 |       |
|chargeType  |int	|否	|计费类型ID	 |       |
|endDate  |String 	|否	|有效期止  	|	2021-10-03	|
|spaces  |int 	|否	|车位数  	|	|
|phone  |String 	|是	|电话  	|	|
|address  |String 	|是	|地址  	|	|
|lockStatus  |int 	|是	|锁车状态  	|1-锁车 2-未锁车|
|status  |int 	|是	|状态  	|1-启用 2-未生效 3-暂停|
|carSeatInfoList  |List&lt;CarSeatInfo&gt; 	|是	|车位信息List  	|储值车可为空 ||

**车位信息（CarSeatInfo）详情**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|seatNo  |String	|否	|车位编号	 |       |
|chargeType  |Integer 	|否	|计费车型ID  	|	|
|chargeName  |String 	|否	|计费车型name  	|	|
|dayCharge  |double 	|是	|日费用  	|	|
|monthCharge  |double	|是	|月费用  	|	|
|quarterCharge  |double |是	|季度费用  	|	|
|halfYearCharge  |double |是	|半年费用  	|	|
|yearCharge  |double|是	|年费用  	|储值车可为空 ||

**返回示例**
 
 ``` 
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;parkId&quot;: 10102,
        &quot;parkName&quot;: &quot;Boot测试车场&quot;,
        &quot;plate&quot;: &quot;粤GHT4TR&quot;,
        &quot;carType&quot;: 1,
        &quot;carInfo&quot;: {
            &quot;inTime&quot;: &quot;2021-10-28 17:48:13&quot;,
            &quot;stopTime&quot;: &quot;142小时35分钟&quot;,
            &quot;stopTimeSecond&quot;: &quot;513299&quot;,
            &quot;amount&quot;: 72.0,
            &quot;totalFee&quot;: 72.0,
            &quot;beforePay&quot;: 0.0,
            &quot;discountVal&quot;: &quot;无&quot;,
            &quot;discountType&quot;: null,
            &quot;laneId&quot;: null,
            &quot;recordId&quot;: 422571,
            &quot;localId&quot;: null,
            &quot;inPic&quot;: &quot;http://boot-park.oss-cn-beijing.aliyuncs.com/picture/10102/02E1702201409536/20211028/17/20211028_174813_138.jpg&quot;
        }
    }
}
 ``` 





---

### 9. 获取订单记录

#### 获取车辆订单记录

**简要描述：** 

- 根据车牌获取订单记录

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 LicensePlateOrder          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |   |
|plate  |String	|否	|车牌	 |   ||

 **请求示例**
```
{
	&quot;service&quot;: &quot;LicensePlateOrder&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;2df848ed-0e81-464c-9ac2-9876164d3001&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 11609,
		&quot;plate&quot;:&quot;粤XDFV454&quot;
	},
	&quot;sign&quot;: &quot;44a65d0c41cdeba424d7bee3a901060d&quot;
}
```
**返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
| code  |String	|否	|状态码	 |	0-正常，其他-异常       |
| msg   |String 	|是	|状态码处理描述  	|错误描述	|
|data     |Object	|否   | 具体业务接口json对象   |	  |
| inTime  |String	|否	|  入场时间  ||
| amount  |int	|否	| 金额  | |
| parkName  |String	|否	|  车场名称  ||
| orderId  |String	|否	|  订单编号  ||
| plate  |String	|否	|  车牌  ||
| outTime  |String	|否	|  出场时间  |||
**返回示例**
````
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: [
        {
            &quot;inTime&quot;: &quot;2024-07-16 11:11:11&quot;,
            &quot;amount&quot;: 4,
            &quot;parkName&quot;: &quot;MM纯云车场&quot;,
            &quot;orderId&quot;: &quot;20240706170830075521318483&quot;,
            &quot;plate&quot;: &quot;粤XDFV454&quot;,
            &quot;outTime&quot;: &quot;2024-07-16 12:11:11&quot;
        }
    ]
}
````

```






---

### 语音屏显

#### 发送语音播报



**简要描述：** 

- 根据车场ID,通道ID. 发送语音播报

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 SendLaneVoice          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|laneId  |Integer	|否	|通道ID	 |       |
|voiceVal  |String	|否	|语音播报内容	 |       ||

 **请求示例**
```
{
	&quot;service&quot;: &quot;LaneInfoList&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;6d5e55c9-96e0-4c98-93a9-a156b777a5e5&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;laneId&quot;: 1001,
		&quot;voiceVal&quot;: &quot;欢迎光临&quot;
		
	},
	&quot;sign&quot;: &quot;b58023cc394e4801b57c6e62cb79fe08&quot;
}
 ```
**返回参数** 

| 字段          | 类型    | 空  | 描述         | 实例                 |
| :------------ | :------ | :-- | :----------- | :------------------- |
| data        | String     | 否  | 触发指令的结果       |播报成功                      ||

 
**返回示例**
 
 ``` 
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;laneType&quot;: 1,
        &quot;isOpenGate&quot;: false,
        &quot;plate&quot;: &quot;粤UU0011&quot;,
        &quot;recognizeTime&quot;: &quot;2025-04-16 15:08:14&quot;,
        &quot;parkId&quot;: 61555,
        &quot;laneId&quot;: 5077,
        &quot;laneName&quot;: &quot;A-入口1&quot;,
        &quot;imageUrl&quot;: &quot;https://qc-park-bucket.oss-cn-shenzhen.aliyuncs.com//default/no_plate_in.png&quot;
    }
}
 ``` 





---

#### 发送屏显内容



**简要描述：** 

- 根据车场ID,通道ID. 发送屏显内容

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 SendLaneDisplay          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数**

| 字段          | 类型    | 空  | 描述         | 实例                 |
| :------------ | :------ | :-- | :----------- | :------------------- |
| parkId        | Integer | 否  | 车场ID       |                      |
| laneId        | Integer | 否  | 通道ID       |                      |
| oneVal        | String  | 是  | 第一行屏显   | 欢迎进入停车场A      |
| twoVal        | String  | 是  | 第二行屏显   | 粤ABC001              |
| threeVal      | String  | 是  | 第三行屏显   | 临停入场               |
| fourVal       | String  | 是  | 第四行屏显   | 时间: 2025-04-16 12:00 |

 **请求示例**
```
{
	&quot;service&quot;: &quot;LaneInfoList&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;6d5e55c9-96e0-4c98-93a9-a156b777a5e5&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;laneId&quot;: 1001,
		&quot;voiceVal&quot;: &quot;欢迎光临&quot;
		
	},
	&quot;sign&quot;: &quot;b58023cc394e4801b57c6e62cb79fe08&quot;
}
 ```
**返回参数** 

| 字段          | 类型    | 空  | 描述         | 实例                 |
| :------------ | :------ | :-- | :----------- | :------------------- |
| data        | String     | 否  | 触发指令的结果       |播报成功                      ||

 
**返回示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;播报成功&quot;
}
 ``` 





---

#### 获取当前车道是否有车


**简要描述：** 

- 根据车场ID,通道ID. 获取当前车道是否有车。

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 GetLaneIsExistCar          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车场ID	 |       |
|laneId  |Integer	|否	|通道ID	 |       ||

 **请求示例**
```
{
	&quot;service&quot;: &quot;LaneInfoList&quot;,
	&quot;version&quot;: &quot;01&quot;,
	&quot;msgId&quot;: &quot;6d5e55c9-96e0-4c98-93a9-a156b777a5e5&quot;,
	&quot;orgId&quot;: &quot;BOOT01&quot;,
	&quot;data&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;laneId&quot;: 1001
	},
	&quot;sign&quot;: &quot;b58023cc394e4801b57c6e62cb79fe08&quot;
}
 ```
**返回参数** 

| 字段          | 类型    | 空  | 描述         | 实例                 |
| :------------ | :------ | :-- | :----------- | :------------------- |
| parkId        | int     | 否  | 车场ID       |                      |
| parkName      | String  | 否  | 车场名称     |                      |
| laneId        | int     | 否  | 通道ID       |                      |
| laneName      | String  | 否  | 通道名称     | 入口通道A            |
| laneType      | int     | 否  | 通道类型     | 1                    |
| plate         | String  | 是  | 车牌号       | 粤B12345             |
| recognizeTime | String  | 是  | 识别时间     | 2025-04-16 11:42:00  |
| isOpenGate    | Boolean | 是  | 是否开闸     | true                 |
| imageUrl      | String  | 是  | 识别图片URL  | http://example.com/image/12345.jpg |

 
**返回示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: {
		&quot;parkId&quot;: 10102,
		&quot;parkName&quot;: &quot;Boot测试车场&quot;,
		&quot;laneList&quot;: [{
			&quot;laneId&quot;: 944,
			&quot;laneName&quot;: &quot;测试地库入口&quot;,
			&quot;laneType&quot;: 1
		}, {
			&quot;laneId&quot;: 1168,
			&quot;laneName&quot;: &quot;测试地库出口&quot;,
			&quot;laneType&quot;: 2
		}]
	}
}
 ``` 





---

### 充电信息上报

#### 充电减免（用于地锁）

**简要描述：** 

- 接收充电信息上报减免停车费用

**请求URL：** 
- `/data/centre/api/v1 `
  
**请求方式：**
- POST 

**公共请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 ChargingReport          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |Integer	|否	|车库ID	 |   |
|partyName  |String	|否	|充电方名称	 |   |
|plate  |String	|否	|车牌	 |   |
|orderId  |String	|否	|交易流水号	 |   |
|pileNo  |String	|否	|充电桩编号	 |   |
|beginTime  |String	|否	|开始充电时间	 |   ||



**返回成功示例**
 
 ``` 
{
	&quot;retCode&quot;: 0,
	&quot;retMsg&quot;: null,
	&quot;retData&quot;: &quot;减免成功&quot;
}
 ``` 







---

### 停车发票开具

#### 获取开票地址—未正式上线

**简要描述：** 

- 根据停车订单获取开票地址
- 目前只支持票通平台开具

**请求URL：** 
- `/data/centre/api/v1 `

**请求方式：**
- POST 

**公共请求参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 ExternalInvoicing          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |   |
|parkOrderList  |String	|否	|停车订单合集,多个用逗号分隔（202504091534400007,202504091534400008）	 |   ||

 **请求示例**
```
{
    &quot;service&quot;: &quot;ExternalInvoicing&quot;,
    &quot;version&quot;: &quot;01&quot;,
    &quot;msgId&quot;: &quot;a1971da4-2da2-4aa7-aec8-b568e37727b8&quot;,
    &quot;orgId&quot;: &quot;BTTEST01&quot;,
    &quot;data&quot;: {
        &quot;parkId&quot;: 61543,
        &quot;parkOrderList&quot;: &quot;202504091534400007&quot;,
    },
    &quot;sign&quot;: &quot;9a8d484e785a9824942687c0b45a3caf&quot;
}
```
**返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
| code  |String	|否	|状态码	 |	0-正常，其他-异常       |
| msg   |String 	|是	|状态码处理描述  	|错误描述	|
|data     |Object	|否   | 具体业务接口json对象   |	  |
| inVoiceOrderId  |String	|否	|  开票订单ID  ||
| invoiceUrl  |String	|否	| 开票地址  ||
**返回示例**
````
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;inVoiceOrderId&quot;: &quot;20250414151330242613&quot;,
        &quot;invoiceUrl&quot;: &quot;http://pay0.qcyuns.com//general_invoice/fujica_special_invoice.html?data=eyJpZCI6NDAyLCJwYXJrSWQiOjYxNTQzLCJwYXJrTmFtZSI6ImNoZW7ovablnLoiLCJjYXJUeXBlIjpudWxsLCJjcmVhdGVUaW1lIjoiMjAyNS0wNC0xNCAxNToxMzozMCIsImludm9pY2VDaGFubmVsIjpudWxsLCJpbnZvaWNlT3JkZXJJZCI6IjIwMjUwNDE0MTUxMzMwMjQyNjEzIiwicGFya09yZGVyTGlzdCI6IjIwMjUwNDA5MTg0MTA3OTQ4IiwiYW1vdW50IjoxLjAsInBob25lIjpudWxsLCJvcGVuSWQiOm51bGwsInN0YXR1cyI6MSwiYnV5ZXJUYXhUeXBlIjpudWxsLCJidXllclRheE5vIjpudWxsLCJidXllck5hbWUiOm51bGwsImJ1eWVyRW1haWwiOm51bGwsImJ1eWVyTW9iaWxlIjpudWxsLCJidXllclRlbGVwaG9uZSI6bnVsbCwiYnV5ZXJBZGRyZXNzIjpudWxsLCJidXllckJhbmtOYW1lIjpudWxsLCJidXllckJhbmtBY2NvdW50IjpudWxsLCJyZW1hcmsiOm51bGwsImludm9pY2VVcmwiOm51bGx9&quot;
    }
}
````

```






---

#### 获取开票订单信息 - 未正式上线

**简要描述：** 

- 根据开票订单号获取开票信息
- 目前只支持票通平台开具

**请求URL：** 
- `/data/centre/api/v1 `

**请求方式：**
- POST 

**公共请求参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|service  |String	|否	|服务名称	 |	 GetInvoiceOrder          |
|version  |String 	|否	| 版本  	|	01	|
|msgId    |String	|否	|消息ID（唯一）   |	 |
|orgId    |String 	|否   |机构ID（分配）    |	 		 |
|data     |Object	|否   | 具体业务接口json对象   |	  |
|sign     |String 	|否   | 请求签名  |请查看签名生成规则||

**业务请求参数** 

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
|parkId  |int	|否	|车场ID	 |   |
|invoiceOrderId  |String	|否	|开票订单号	 |   ||

 **请求示例**
```
{
    &quot;service&quot;: &quot;ExternalInvoicing&quot;,
    &quot;version&quot;: &quot;01&quot;,
    &quot;msgId&quot;: &quot;a1971da4-2da2-4aa7-aec8-b568e37727b8&quot;,
    &quot;orgId&quot;: &quot;BTTEST01&quot;,
    &quot;data&quot;: {
        &quot;parkId&quot;: 61543,
        &quot;invoiceOrderId&quot;: &quot;202504091534400007&quot;,
    },
    &quot;sign&quot;: &quot;9a8d484e785a9824942687c0b45a3caf&quot;
}
```
**返回参数**

|字段|类型|空|描述|实例|
|:----    |:-------    |:--- |-- -|------      |
| code  |String	|否	|状态码	 |	0-正常，其他-异常       |
| msg   |String 	|是	|状态码处理描述  	|错误描述	|
|data     |Object	|否   | 具体业务接口json对象   |	  |
| status  |int	|否	|  开票状态（1：开票中 2：开票成功 4：开票失败）  ||
| invoiceUrl  |String	|是	| 开票状态为2时，返回发票地址  | |
| remark  |String	|是	|  开票状态为4时，返回异常信息  ||
**返回示例**
````
{
    &quot;retCode&quot;: 0,
    &quot;retMsg&quot;: null,
    &quot;retData&quot;: {
        &quot;invoiceUrl&quot;: &quot;https://fpkjnw.vpiaotong.com/tp/scan-invoice/view/JQv~KhcTyudGZgAr5EIBYZmTvfA~Xgd_.pt&quot;,
        &quot;status&quot;: 2
    }
}
````

```






---

