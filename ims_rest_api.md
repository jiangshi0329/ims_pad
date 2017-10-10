# 冻品SAAS PAD版 REST-API

标签（空格分隔）： 接口文档

---

# 1. 目录

| 编号   | 名称      | url  |
| ---- | ------- | ---- |
| 3.1    | 通用      |      |
| 3.1.1  | 供应商渐近查询 |base/supply/query|
| 3.1.2  | 商品渐近查询  |base/product/query|
| 3.1.3  | 仓库选单    |base/depot/list|
| 3.1.4  | 付款方式选单  |base/account/list|
| 3.2    | 进货业务    |      |
| 3.2.1  | 保存进货单   |purchase/order/add|
| 3.2.2  | 查询进货单   |purchase/order/query|
| 3.2.3  | 查询付款信息  |purchase/payinfo/query|
| 3.2.4  | 保存付款单   |purchase/pay/add|
| 3.2.5  | 查询付款单   |purchase/pay/query|

# 2. 协议规范
## 2.1 请求URL
https://[接口域名]/[resources]/[action]
接口协议：https
HTTP方法：统一为POST

| 字段名 | 用途 | 备注 |
| ------ | ---- | ---- |
| 接口域名 |  | c.api.[domain] |
| [resources] | 表示资源名称 | |
| [action] | 表示动作 | |

注：

- 以POST方式调用接口时，参数应以POST参数形式传递。HTTP HEADER中“Content-type”字段要设置为“application/x-www-form-urlencoded”。 
- 为了确保数据隐私和安全，API须通过HTTPS方式请求。
- 服务器接收数据和返回数据编码格式统一为UTF-8。
- 使用JSON格式返回数据，在http响应头申明Content-Type及Accept：Content-Type:application/json;charset=utf-8;Accept:application/json;
- 格式化JSON数据并且使用gzip压缩。
- 接口字段名称仅支持小写字母。
- Node代表单个节点。
- List代表数组节点。
- 类型Number括号中的数字代表数字转换为字符时的字符允许最大个数，Number(18)可定义为Long，Number(11)可定义为Integer。

## 2.2 通用参数
各接口请求的通用参数都放置在header中，参数列表如下：

| 参数名 | 类型 | 参数描述 |
| --- | --- | --- |
| appcode | String | 应用版本代码 |
| systemtype | String | 系统类型，如ios、android |
| systemversion | String | 手机平台的系统版本 |
| deviceid | String | 设备唯一标识 |
| devicemodel | String | 设备型号 |
| channel | String | 渠道代码 |
| auid | Number(18) | 用户唯一标识码 |
| timestamp | Number | 本请求的unix时间戳，用于确认请求的有效期。默认情况下，请求时间戳与服务器时间（北京时间）偏差大于600秒则会被拒绝 |
| validtime | Number | 配合timestamp确定请求的有效期，单位为秒，最大值为600。若不设置此参数或参数值非法，则按默认值600秒计算有效期 |
| sign | String | 参数内容签名 |

## 2.3 返回结果
返回结果的例子如下：
```
{
   "ret_code":0,
   "err_msg":"ok",
   "result":{
     ......
   }
}
```

返回结果字段定义如下：

| 参数名 | 类型 | 限制 | 参数描述 |
| --- | --- | --- | --- |
| ret_code | Number(11) | 必填 | 返回应答码 0-处理成功 |
| err_msg | String | 必填 | 错误信息： ok-成功，其他为错误信息 |
| result | String<JSON> | | 请求正确时，若有额外数据要返回，则结果以json格式封装在该字段。若无额外数据，则可能无此字段。|

## 2.4 错误代码
常见错误代码

| 错误代码 | 错误信息 | 说明 |
| --- | --- | --- |
| 1 | 系统正在维护，请稍后重试 | 系统维护 |
| 2 | 系统异常，请联系管理员 | 系统异常 |
| 3 | 请重新登录系统 | 用户数据出现异常，需要重新登录系统获取正确数据 |
| 4 | 	系统出现异常，请升级版本 | 用户app出现异常，需要升级版本 |
 
# 3. 接口定义

## 3.1. 通用

### 3.1.1 供应商渐近查询

url:base/supply/query

方法：POST

功能描述：输入关键字，按供应商名称模糊查询，仅显示前10个

版本支持：1.0.0

请求参数：

| 参数名  | 类型   | 限制   | 默认值  | 参数描述 |
| ---- | ---- | ---- | ---- | ---- |
| companycode | Number(18) | 必填 | | 公司代码 |
| supplykeyword| String| 必填 | | 供应商名称关键字|

请求样例：
content = {"supplykeyword":"超市","companycode":"1"}
响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| supply | List<Node>     | 供应商节点列表 |
|supply/code | Number(18)| 供应商代码|
|supply/name | String | 供应商名称|
响应样例：
{
    "err_msg": "ok",
    "ret_code": 0,
    "result": {
        "supply": [
            {
                "code": 1,
                "name": "久久超市"
            },
            {
                "code": 2,
                "name": "联华超市"
            },
            {
                "code": 910340743049121792,
                "name": "物美超市"
            },
            {
                "code": 910747998189916160,
                "name": "好又多超市"
            },
            {
                "code": 910793815894396928,
                "name": "天猫超市"
            },
            {
                "code": 910794875660800000,
                "name": "百度超市"
            }
        ]
    }
}
### 3.1.2 商品渐近查询
url: base/product/query

方法：POST

功能描述：输入关键字，按商品名称/编号模糊查询，仅显示前10个

版本支持：1.0.0

请求参数：

| 参数名  | 类型   | 限制   | 默认值  | 参数描述 |
| ---- | ---- | ---- | ---- | ---- |
| companycode | Number(18) | 必填 | | 公司代码 |
| productkeyword | String | 必填 | | 商品名称关键字 |


请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| product | List<Node> | 商品节点列表 |
| product/code | Number(18) | 商品代码 |
| product/name | String | 商品名称 |
| product/category | String | 商品类型 |
| product/inventionunit | String | 库存单位 |

响应样例：

### 3.1.3 仓库选单
url:base/depot/list

方法：POST

功能描述：查询指定公司的仓库

版本支持：1.0.0

请求参数：

| 参数名  | 类型   | 限制   | 默认值  | 参数描述 |
| ---- | ---- | ---- | ---- | ---- |
| companycode | Number(18) | 必填 | | 公司代码 |

请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| depot | List<Node> | 仓库节点列表 |
| depot/code | Number(18) | 仓库代码 |
| depot/name | String | 仓库名称 |

响应样例：

### 3.1.4 付款方式选单
url:base/account/list

方法：POST

功能描述：查询指定公司的账号

版本支持：1.0.0

请求参数：

| 参数名  | 类型   | 限制   | 默认值  | 参数描述 |
| ---- | ---- | ---- | ---- | ---- |
| companycode | Number(18) | 必填 | | 公司代码 |

请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| account | List<Node> | 账号节点列表|
| account/code | Number(18) | 账号代码 |
| account/Name | String | 账号名称 |

响应样例：

## 3.2 进货业务

### 3.2.1 保存进货单
url:purchase/order/add

方法：POST

功能描述：新增进货单的保存

版本支持：1.0.0

请求参数：

| 参数名  | 类型   | 限制   | 默认值  | 参数描述 |
| ---- | ---- | ---- | ---- | ---- |
| companycode | Number(18) | 必填 | | 公司代码 |
| supplycode | Number(18) | 必填 | | 供应商代码 |
| product | List<Node> | 必填 | | 进货商品清单 |
| product/productcode | Number(18) | 必填 | | 商品代码 |
| product/number | Number(11) | 必填 | | 数量 |
| product/price | Number(11) | 必填 | | 价格 |
| product/depotcode | Number(11) | 必填 | | 仓库代码 |
| accountcode | Number(18) | 必填 | | 付款方式 |
| payamount | Number(11) | 必填 | | 付款金额 |
| brokerage | String | 必填 | | 经手人 |
| purchasedate | String | 必填 | | 进货日期(YYYYMMDD) |
| remark | String | 必填 | | 备注 |


请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| / |  | |

### 3.2.1 查询进货单
url:purchase/order/query

方法：POST

功能描述：根据供应商、进货单号、商品和起止时间查询进货单

版本支持：1.0.0

请求参数：

| 参数名  | 类型   | 限制   | 默认值  | 参数描述 |
| ---- | ---- | ---- | ---- | ---- |
| companycode | Number(18) | 必填 | | 公司代码 |
| supplycode | Number(18) | 可选 | | 供应商代码 |
| orderno | String | 可选 | | 进货单编号 |
| productcode | Number(18) | 可选 | | 商品代码 |
| startdate | String | 必填 | | 开始日期(YYYYMMDD) |
| enddate | String | 必填 | | 截止日期(YYYYMMDD) |


请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| / |  | |
