冻品SAAS PAD版 REST-API

标签（空格分隔）： 接口文档

---

# 1. 目录

| 编号    | 名称        | url                                     |
| ----- | --------- | --------------------------------------- |
| 3.1   | 通用        |                                         |
| 3.1.1 | 供应商渐近查询   | base/supply/query                       |
| 3.1.2 | 商品渐近查询    | base/product/query                      |
| 3.1.3 | 仓库选单      | base/depot/list                         |
| 3.1.4 | 付款方式选单    | base/account/list                       |
| 3.1.5 | 获取单号      | sys/sequence/get                        |
| 3.1.6 | 客户渐进查询    | base/custom/query                       |
| 3.2   | 进货业务      |                                         |
| 3.2.1 | 保存进货单     | sales/order/add                         |
| 3.2.2 | 查询进货单     | purchase/order/query                    |
| 3.2.3 | 进货单详情     | purchase/order/detail                   |
| 3.2.4 | 查询付款信息    | purchase/payinfo/query                  |
| 3.2.5 | 保存付款单     | purchase/pay/add                        |
| 3.2.6 | 查询付款单     | purchase/pay/query                      |
| 3.3   | 用户        |                                         |
| 3.3.1 | 用户登录      | sys/login                               |
| 3.3.2 | 用户信息      | sys/user/get                            |
| 3.3.3 | 密码修改      | sys/password/change                     |
| 3.4   | 消息        |                                         |
| 3.4.1 | 最新消息数     | message/unreadnumber/get                |
| 3.4.2 | 消息列表      | message/history/list                    |
| 3.4.3 | 读消息       | message/single/read                     |
| 3.5   | 销货业务      |                                         |
| 3.5.1 | 保存销货单     | sales/order/add                         |
| 3.5.2 | 打印销货单     | sales/order/print                       |
| 3.5.3 | 分享销货单     | sales/order/share                       |
| 3.5.4 | 查询销货单     | sales/order/query                       |
| 3.5.5 | 销货单详情     | sales/order/detail                      |
| 3.5.6 | 查询收款信息    | sales/collect/info/query                |
| 3.5.7 | 保存收款单     | sales/collect/add                       |
| 3.5.8 | 查询收款单     | sales/collect/query                     |
| 3.6   | 仓库管理      |                                         |
| 3.6.1 | 查询销售单     | inventory/salesorder/query              |
| 3.6.2 | 查询出库记录    | inventory/historydelivery/query         |
| 3.6.3 | 查询销售单出库信息 | inventory/salesorder/deliveryinfo/query |
| 3.6.4 | 商品出库      | inventory/delivery/add                  |
| 3.6.5 | 查询出库单     | inventory/delivery/query                |
| 3.6.6 | 查询出库单详情   | inventory/delivery/detail               |
| 3.6.7 | 查询商品库存    | inventory/stock/query                   |


# 2. 协议规范

## 2.1 请求URL
https://[接口域名]/[resources]/[action]
接口协议：https
HTTP方法：统一为POST

| 字段名         | 用途     | 备注             |
| ----------- | ------ | -------------- |
| 接口域名        |        | c.api.[domain] |
| [resources] | 表示资源名称 |                |
| [action]    | 表示动作   |                |

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

| 参数名           | 类型         | 参数描述                                     |
| ------------- | ---------- | ---------------------------------------- |
| appcode       | String     | 应用版本代码                                   |
| systemtype    | String     | 系统类型，如ios、android                        |
| systemversion | String     | 手机平台的系统版本                                |
| deviceid      | String     | 设备唯一标识                                   |
| devicemodel   | String     | 设备型号                                     |
| channel       | String     | 渠道代码                                     |
| auid          | Number(18) | 用户唯一标识码                                  |
| timestamp     | Number     | 本请求的unix时间戳，用于确认请求的有效期。默认情况下，请求时间戳与服务器时间（北京时间）偏差大于600秒则会被拒绝 |
| validtime     | Number     | 配合timestamp确定请求的有效期，单位为秒，最大值为600。若不设置此参数或参数值非法，则按默认值600秒计算有效期 |
| sign          | String     | 参数内容签名                                   |

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

| 参数名      | 类型           | 限制   | 参数描述                                     |
| -------- | ------------ | ---- | ---------------------------------------- |
| ret_code | Number(11)   | 必填   | 返回应答码 0-处理成功                             |
| err_msg  | String       | 必填   | 错误信息： ok-成功，其他为错误信息                      |
| result   | String<JSON> |      | 请求正确时，若有额外数据要返回，则结果以json格式封装在该字段。若无额外数据，则可能无此字段。 |

## 2.4 错误代码
常见错误代码

| 错误代码 | 错误信息         | 说明                      |
| ---- | ------------ | ----------------------- |
| 1    | 系统正在维护，请稍后重试 | 系统维护                    |
| 2    | 系统异常，请联系管理员  | 系统异常                    |
| 3    | 请重新登录系统      | 用户数据出现异常，需要重新登录系统获取正确数据 |
| 4    | 系统出现异常，请升级版本 | 用户app出现异常，需要升级版本        |

# 3. 接口定义

## 3.1. 通用

### 3.1.1 供应商渐近查询

url:base/supply/query

方法：POST

功能描述：输入关键字，按供应商名称模糊查询，仅显示前10个

版本支持：1.0.0

请求参数：

| 参数名           | 类型         | 限制   | 默认值  | 参数描述     |
| ------------- | ---------- | ---- | ---- | -------- |
| companycode   | Number(18) | 必填   |      | 公司代码     |
| supplykeyword | String     | 必填   |      | 供应商名称关键字 |

请求样例：
```
content = {"supplykeyword":"超市","companycode":"1"}
```

响应参数：

| 参数名         | 类型         | 参数描述    |
| ----------- | ---------- | ------- |
| supply      | List<Node> | 供应商节点列表 |
| supply/code | Number(18) | 供应商代码   |
| supply/name | String     | 供应商名称   |
响应样例：
```
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
```

### 3.1.2 商品渐近查询
url: base/product/query

方法：POST

功能描述：输入关键字，按商品名称/编号模糊查询，仅显示前10个

版本支持：1.0.0

请求参数：

| 参数名            | 类型         | 限制   | 默认值  | 参数描述    |
| -------------- | ---------- | ---- | ---- | ------- |
| companycode    | Number(18) | 必填   |      | 公司代码    |
| productkeyword | String     | 必填   |      | 商品名称关键字 |


请求样例：

响应参数：

| 参数名                   | 类型         | 参数描述   |
| --------------------- | ---------- | ------ |
| product               | List<Node> | 商品节点列表 |
| product/code          | Number(18) | 商品代码   |
| product/name          | String     | 商品名称   |
| product/category      | String     | 商品类型   |
| product/inventionunit | String     | 库存单位   |

响应样例：

### 3.1.3 仓库选单
url:base/depot/list

方法：POST

功能描述：查询指定公司的仓库

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述 |
| ----------- | ---------- | ---- | ---- | ---- |
| companycode | Number(18) | 必填   |      | 公司代码 |

请求样例：

响应参数：

| 参数名        | 类型         | 参数描述   |
| ---------- | ---------- | ------ |
| depot      | List<Node> | 仓库节点列表 |
| depot/code | Number(18) | 仓库代码   |
| depot/name | String     | 仓库名称   |

响应样例：

### 3.1.4 付款方式选单
url:base/account/list

方法：POST

功能描述：查询指定公司的账号

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述 |
| ----------- | ---------- | ---- | ---- | ---- |
| companycode | Number(18) | 必填   |      | 公司代码 |

请求样例：

响应参数：

| 参数名          | 类型         | 参数描述   |
| ------------ | ---------- | ------ |
| account      | List<Node> | 账号节点列表 |
| account/code | Number(18) | 账号代码   |
| account/Name | String     | 账号名称   |

响应样例：

### 3.1.5 获取单号
url:sys/sequence/get

方法：POST

功能描述：根据单号类型，获取单号，如进货单号、付款单号等

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述                                     |
| ----------- | ---------- | ---- | ---- | ---------------------------------------- |
| companycode | Number(18) | 必填   |      | 公司代码                                     |
| type        | String     | 必填   |      | 单号类型:  CKD-出库单;FKD-付款单;HCD-红冲单;JHD-进货单;JHTHD-进货退货单;SKD-收款单;XSD-销售单;XSTHD-销售退货单 |

请求样例：

响应参数：

| 参数名  | 类型     | 参数描述 |
| ---- | ------ | ---- |
| no   | String | 单号   |

响应样例：

### 3.1.6 客户渐近查询

url:base/custom/query

方法：POST

功能描述：输入关键字，按客户名称模糊查询，仅显示前10个

版本支持：1.0.0

请求参数：

| 参数名           | 类型         | 限制   | 默认值  | 参数描述    |
| ------------- | ---------- | ---- | ---- | ------- |
| companycode   | Number(18) | 必填   |      | 公司代码    |
| customkeyword | String     | 必填   |      | 客户名称关键字 |

请求样例：

响应参数：

| 参数名         | 类型         | 参数描述   |
| ----------- | ---------- | ------ |
| custom      | List<Node> | 客户节点列表 |
| custom/code | Number(18) | 客户代码   |
| custom/name | String     | 客户名称   |
响应样例：


## 3.2 进货业务

### 3.2.1 保存进货单
url:purchase/order/add

方法：POST

功能描述：新增进货单的保存

版本支持：1.0.0

请求参数：

| 参数名                 | 类型         | 限制   | 默认值  | 参数描述           |
| ------------------- | ---------- | ---- | ---- | -------------- |
| companycode         | Number(18) | 必填   |      | 公司代码           |
| supplycode          | Number(18) | 必填   |      | 供应商代码          |
| orderno             | Number(18) | 必填   |      | 进货单单号          |
| product             | List<Node> | 必填   |      | 进货商品清单         |
| product/productcode | Number(18) | 必填   |      | 商品代码           |
| product/number      | Number(11) | 必填   |      | 数量             |
| product/price       | Number(11) | 必填   |      | 价格 单位(分)       |
| product/depotcode   | Number(11) | 必填   |      | 仓库代码           |
| accountcode         | Number(18) | 必填   |      | 付款方式           |
| payamount           | Number(11) | 必填   |      | 付款金额           |
| brokerage           | String     | 必填   |      | 经手人            |
| purchasedate        | String     | 必填   |      | 进货日期(YYYYMMDD) |
| remark              | String     | 必填   |      | 备注             |


请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| /    |      |      |

### 3.2.2 查询进货单
url:purchase/order/query

方法：POST

功能描述：根据供应商、进货单号、商品和起止时间查询进货单

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述           |
| ----------- | ---------- | ---- | ---- | -------------- |
| companycode | Number(18) | 必填   |      | 公司代码           |
| supplycode  | Number(18) | 可选   |      | 供应商代码          |
| orderno     | String     | 可选   |      | 进货单编号          |
| productcode | Number(18) | 可选   |      | 商品代码           |
| startdate   | String     | 必填   |      | 开始日期(YYYYMMDD) |
| enddate     | String     | 必填   |      | 截止日期(YYYYMMDD) |
| pageno      | Number(11) | 可选   | 1    | 页码             |


请求样例：

响应参数：

| 参数名                        | 类型         | 参数描述            |
| -------------------------- | ---------- | --------------- |
| purchaseorder              | List<Node> | 进货单列表           |
| purchaseorder/ordercode    | Number(18) | 代码              |
| purchaseorder/orderno      | String     | 单号              |
| purchaseorder/supplyname   | String     | 供应商             |
| purchaseorder/purchasedate | String     | 进货日期:格式yyyymmdd |
| purchaseorder/state        | Number(2)  | 状态：0-未结清;1-已结清  |

### 3.2.3 进货单详情
url:purchase/order/detail

方法：POST

功能描述：根据进货单代码查详情

版本支持：1.0.0

请求参数：

| 参数名       | 类型         | 限制   | 默认值  | 参数描述  |
| --------- | ---------- | ---- | ---- | ----- |
| ordercode | Number(18) | 必填   |      | 进货单代码 |


请求样例：

响应参数：

| 参数名                               | 类型         | 参数描述            |
| --------------------------------- | ---------- | --------------- |
| purchaseorder                     | Node       | 进货单             |
| purchaseorder/ordercode           | Number(18) | 代码              |
| purchaseorder/supplyname          | String     | 供应商             |
| purchaseorder/purchasedate        | String     | 进货日期:格式yyyymmdd |
| purchaseorder/state               | Number(2)  | 状态：0-未结清;1-已结清  |
| purchaseorder/product             | List<Node> | 商品清单            |
| purchaseorder/product/productno   | String     | 商品编号            |
| purchaseorder/product/productname | String     | 商品名称            |
| purchaseorder/product/category    | String     | 商品分类            |
| purchaseorder/product/unit        | String     | 库存单位            |
| purchaseorder/product/stock       | Number(11) | 库存数量            |
| purchaseorder/product/depot       | String     | 仓库名称            |
| purchaseorder/product/price       | Number(11) | 价格(分)           |
| purchaseorder/product/amount      | Number(11) | 金额(分)           |

### 3.2.4 查询付款信息
url:purchase/payinfo/query

方法：POST

功能描述：根据公司和供应商查付款信息包括已付款和未结清的进货单/进货退货单/进货红冲单

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述  |
| ----------- | ---------- | ---- | ---- | ----- |
| companycode | Number(18) | 必填   |      | 公司代码  |
| supplycode  | Number(18) | 必填   |      | 供应商代码 |


请求样例：

响应参数：

| 参数名               | 类型         | 参数描述                      |
| ----------------- | ---------- | ------------------------- |
| pay               | List<Node> | 付款单列表                     |
| pay/accountname   | String     | 账号名称                      |
| pay/accountno     | String     | 账号：卡号/支付宝账号等              |
| pay/paydate       | String     | 付款日期:格式yyyymmdd           |
| pay/amount        | Number(11) | 金额(分)                     |
| pay/remark        | String     | 备注                        |
| unpay             | List<Node> | 未结清的相关单据列表(进货单/进货退货单/红冲单) |
| unpay/type        | Number(2)  | 单据类型:1-进货单;2-进货退货单;3-红冲单  |
| unpay/no          | String     | 单据编号                      |
| unpay/code        | Number(18) | 单据代码                      |
| unpay/operatedate | String     | 操作日期:格式yyyymmdd           |
| unpay/amount      | Number(11) | 金额(分)                     |
| unpay/remark      | String     | 备注                        |

### 3.2.5 保存付款单
url:purchase/pay/add

方法：POST

功能描述：保存付款单

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述            |
| ----------- | ---------- | ---- | ---- | --------------- |
| companycode | Number(18) | 必填   |      | 公司代码            |
| supplycode  | Number(18) | 必填   |      | 供应商代码           |
| accountcode | Number(18) | 必填   |      | 付款方式            |
| payno       | String     | 必填   |      | 付款单号            |
| paytype     | Number(2)  | 必填   |      | 付款类型:1-付款 2-预付款 |
| payamount   | Number(11) | 必填   |      | 付款金额            |
| brokerage   | String     | 必填   |      | 经手人             |
| paydate     | String     | 必填   |      | 付款日期(YYYYMMDD)  |
| remark      | String     | 必填   |      | 备注              |


请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| /    |      |      |

### 3.2.6 查询付款单
url:purchase/pay/query

方法：POST

功能描述：根据供应商、付款类型、付款方式和起止时间查询付款单

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述            |
| ----------- | ---------- | ---- | ---- | --------------- |
| companycode | Number(18) | 必填   |      | 公司代码            |
| supplycode  | Number(18) | 可选   |      | 供应商代码           |
| paytype     | Number(2)  | 可选   |      | 付款类型:1-付款 2-预付款 |
| accountcode | Number(18) | 可选   |      | 付款方式            |
| startdate   | String     | 必填   |      | 开始日期(YYYYMMDD)  |
| enddate     | String     | 必填   |      | 截止日期(YYYYMMDD)  |
| pageno      | Number(11) | 可选   | 1    | 页码              |


请求样例：

响应参数：

| 参数名             | 类型         | 参数描述            |
| --------------- | ---------- | --------------- |
| pay             | List<Node> | 付款单列表           |
| pay/no          | String     | 单号              |
| pay/supplyname  | String     | 供应商             |
| pay/amount      | Number(18) | 金额(分)           |
| paytype         | Number(2)  | 付款类型:1-付款 2-预付款 |
| pay/accountname | String     | 付款方式            |
| pay/accountno   | String     | 付款账号            |
| pay/paydate     | String     | 付款日期:格式yyyymmdd |

## 3.3 用户

### 3.3.1 用户登录
url:sys/user/login

方法：POST

功能描述：用户输入账号密码登录

版本支持：1.0.0

请求参数：

| 参数名      | 类型     | 限制   | 默认值  | 参数描述 |
| -------- | ------ | ---- | ---- | ---- |
| name     | String | 必填   |      | 账号   |
| password | String | 必填   |      | 密码   |


请求样例：

响应参数：

| 参数名    | 类型         | 参数描述                                   |
| ------ | ---------- | -------------------------------------- |
| result | Number(11) | 结果信息：0- 登录成功;1- 账号不存在;2- 密码错误;3- 账号已禁用 |
| auid   | Number(18) | 用户唯一识别码，仅当结果为0时返回                      |

### 3.3.2 用户信息
url:sys/user/get

方法：POST

功能描述：根据账号获取用户信息

版本支持：1.0.0

请求参数：

| 参数名  | 类型         | 限制   | 默认值  | 参数描述    |
| ---- | ---------- | ---- | ---- | ------- |
| auid | Number(18) | 必填   |      | 用户唯一识别码 |


请求样例：

响应参数：

| 参数名              | 类型        | 参数描述        |
| ---------------- | --------- | ----------- |
| user             | Node      | 用户信息        |
| user/name        | String    | 账号          |
| user/realname    | String    | 姓名          |
| user/rolename    | String    | 角色名称        |
| user/phone       | String    | 手机号         |
| user/sex         | Number(2) | 性别:0-女; 1-男 |
| user/companyname | String    | 公司名称        |

### 3.3.3 密码修改
url:sys/password/change

方法：POST

功能描述：用户修改密码

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述    |
| ----------- | ---------- | ---- | ---- | ------- |
| auid        | Number(18) | 必填   |      | 用户唯一识别码 |
| oldpassword | String     | 必填   |      | 原密码     |
| newpassword | String     | 必填   |      | 新密码     |

请求样例：

响应参数：

| 参数名    | 类型        | 参数描述                           |
| ------ | --------- | ------------------------------ |
| result | Number(2) | 处理结果:0-成功;1-原密码错误;2-新密码不合法或太简单 |

## 3.4 消息

### 3.4.1 最新消息数
url:message/unreadnumber/get

方法：POST

功能描述：获取最近未读的消息数目

版本支持：1.0.0

请求参数：

| 参数名          | 类型         | 限制   | 默认值  | 参数描述                       |
| ------------ | ---------- | ---- | ---- | -------------------------- |
| auid         | Number(18) | 必填   |      | 用户唯一识别码                    |
| lastreadtime | String     | 必填   |      | 最近读取消息时间：格式为yyyymmddhhmiss |

请求样例：

响应参数：

| 参数名    | 类型         | 参数描述 |
| ------ | ---------- | ---- |
| number | Number(11) | 未读条数 |

### 3.4.2 消息列表
url:message/history/list

方法：POST

功能描述：分页获取历史消息，时间倒序排序

版本支持：1.0.0

请求参数：

| 参数名        | 类型         | 限制   | 默认值  | 参数描述                        |
| ---------- | ---------- | ---- | ---- | --------------------------- |
| auid       | Number(18) | 必填   |      | 用户唯一识别码                     |
| recenttime | String     | 可选   |      | 第一页读取消息时间：格式为yyyymmddhhmiss |
| pageno     | Number(11) | 可选   | 1    | 页码                          |

请求样例：

响应参数：

| 参数名                       | 类型         | 参数描述                        |
| ------------------------- | ---------- | --------------------------- |
| recenttime                | String     | 第一页读取消息时间：格式为yyyymmddhhmiss |
| message                   | List<Node> | 消息列表                        |
| message/messagecode       | Number(18) | 消息代码                        |
| message/readflag          | Number(2)  | 已读标记：0-未读;1-已读              |
| message/content           | String     | 消息内容                        |
| message/notifytime        | String     | 通知时间：格式为yyyymmddhhmiss      |
| message/extra             | Node       | 扩展信息                        |
| message/extra/productname | String     | 商品名字                        |
| message/extra/productno   | String     | 商品编号                        |
| message/extra/stock       | Number     | 库存数量                        |
| message/extra/unit        | String     | 库存单位                        |

### 3.4.3 读消息
url:message/single/read

方法：POST

功能描述：标记单条消息为已读状态

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述 |
| ----------- | ---------- | ---- | ---- | ---- |
| messagecode | Number(18) | 必填   |      | 消息代码 |

请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| /    |      |      |

## 3.5 进货业务

### 3.5.1 保存销售单
url:sales/order/add

方法：POST

功能描述：新增销售单的保存

版本支持：1.0.0

请求参数：

| 参数名                 | 类型         | 限制   | 默认值  | 参数描述           |
| ------------------- | ---------- | ---- | ---- | -------------- |
| companycode         | Number(18) | 必填   |      | 公司代码           |
| customcode          | Number(18) | 必填   |      | 客户代码           |
| orderno             | String(18) | 必填   |      | 销货单单号          |
| product             | List<Node> | 必填   |      | 销货商品清单         |
| product/productcode | Number(18) | 必填   |      | 商品代码           |
| product/number      | Number(11) | 必填   |      | 数量             |
| product/price       | Number(11) | 必填   |      | 价格 单位(分)       |
| product/depotcode   | Number(11) | 必填   |      | 仓库代码           |
| collecttype         | Number(2)  | 必填   |      | 收款类型：1-收款 2-订金 |
| accountcode         | Number(18) | 必填   |      | 收款方式           |
| amount              | Number(11) | 可选   |      | 收款金额           |
| vehiclelicence      | String     | 可选   |      | 车牌号            |
| brokerage           | String     | 必填   |      | 经手人            |
| saledate            | String     | 必填   |      | 销货日期(YYYYMMDD) |
| remark              | String     | 可选   |      | 备注             |
| multidelivery       | Number(2)  | 必填   |      | 多次出库:0-否 1-是   |
| stalldelivery       | Number(2)  | 必填   |      | 档口出货:0-否 1-是   |

注意：选择多次出库，销售单必须结清，即收款金额=应收款；档口出货应生成出库记录；

请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| /    |      |      |

### 3.5.2 打印销售单
url:sales/order/print

方法：POST

功能描述：根据销售单代码，本地或远程打印销售单

版本支持：1.0.0

请求参数：

| 参数名       | 类型         | 限制   | 默认值  | 参数描述           |
| --------- | ---------- | ---- | ---- | -------------- |
| ordercode | Number(18) | 必填   |      | 销售单代码          |
| type      | Number(2)  | 必填   |      | 打印类型：0-本地 1-远程 |


请求样例：

响应参数：

| 参数名   | 类型        | 参数描述          |
| ----- | --------- | ------------- |
| error | Number(2) | 错误信息，详见错误码对应表 |

错误码对应表
| 错误码  | 错误描述         |
| ---- | ------------ |
| 0    | success      |
| 8    | 打印机信息错误,参数有误 |
| 9    | 连接打印机失败,参数有误 |
| 10   | 权限不足         |
| 12   | 缺少必要参数       |
| 13   | 打印失败,参数有误    |
| 33   | Uuid不合法      |

### 3.5.3 分享销售单
url:sales/order/share

方法：POST

功能描述：根据销售单代码，向其他应用（如qq、微信等）分享销售单

版本支持：1.0.0

请求参数：

| 参数名       | 类型         | 限制   | 默认值  | 参数描述  |
| --------- | ---------- | ---- | ---- | ----- |
| ordercode | Number(18) | 必填   |      | 销售单代码 |


请求样例：

响应参数：

| 参数名      | 类型     | 参数描述   |
| -------- | ------ | ------ |
| shareurl | String | 分享链接地址 |

### 3.5.4 查询进货单
url:sales/order/query

方法：POST

功能描述：根据客户、销售单号、商品和起止时间查询销售单

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述           |
| ----------- | ---------- | ---- | ---- | -------------- |
| companycode | Number(18) | 必填   |      | 公司代码           |
| customcode  | Number(18) | 可选   |      | 客户代码           |
| orderno     | String     | 可选   |      | 进货单编号          |
| productcode | Number(18) | 可选   |      | 商品代码           |
| startdate   | String     | 必填   |      | 开始日期(YYYYMMDD) |
| enddate     | String     | 必填   |      | 截止日期(YYYYMMDD) |
| pageno      | Number(11) | 可选   | 1    | 页码             |


请求样例：

响应参数：

| 参数名                   | 类型         | 参数描述            |
| --------------------- | ---------- | --------------- |
| salesorder            | List<Node> | 销售单列表           |
| salesorder/ordercode  | Number(18) | 代码              |
| salesorder/orderno    | String     | 单号              |
| salesorder/customname | String     | 客户名称            |
| salesorder/saledate   | String     | 销售日期:格式yyyymmdd |
| salesorder/state      | Number(2)  | 状态：0-未结清;1-已结清  |

### 3.5.5 销售单详情
url:sales/order/detail

方法：POST

功能描述：根据销售单代码查详情

版本支持：1.0.0

请求参数：

| 参数名       | 类型         | 限制   | 默认值  | 参数描述  |
| --------- | ---------- | ---- | ---- | ----- |
| ordercode | Number(18) | 必填   |      | 销售单代码 |


请求样例：

响应参数：

| 参数名                            | 类型         | 参数描述            |
| ------------------------------ | ---------- | --------------- |
| salesorder                     | Node       | 销售单             |
| salesorder/ordercode           | Number(18) | 代码              |
| salesorder/customname          | String     | 客户              |
| salesorder/salesdate           | String     | 进货日期:格式yyyymmdd |
| salesorder/state               | Number(2)  | 状态：0-未结清;1-已结清  |
| salesorder/product             | List<Node> | 商品清单            |
| salesorder/product/productno   | String     | 商品编号            |
| salesorder/product/productname | String     | 商品名称            |
| salesorder/product/category    | String     | 商品分类            |
| salesorder/product/unit        | String     | 库存单位            |
| salesorder/product/number      | Number(11) | 数量              |
| salesorder/product/depot       | String     | 仓库名称            |
| salesorder/product/price       | Number(11) | 价格(分)           |
| salesorder/product/amount      | Number(11) | 金额(分)           |

### 3.5.6 查询收款信息
url:sales/collect/info/query

方法：POST

功能描述：根据公司代码和客户代码收款信息，包括已收款和未结清的销售单/销售退货单/销售红冲单等

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述 |
| ----------- | ---------- | ---- | ---- | ---- |
| companycode | Number(18) | 必填   |      | 公司代码 |
| customcode  | Number(18) | 必填   |      | 客户代码 |


请求样例：

响应参数：

| 参数名                 | 类型         | 参数描述                      |
| ------------------- | ---------- | ------------------------- |
| collect             | List<Node> | 收款单列表                     |
| collect/accountname | String     | 账号名称                      |
| collect/accountno   | String     | 账号：卡号/支付宝账号等              |
| collect/collectdate | String     | 收款日期:格式yyyymmdd           |
| collect/amount      | Number(11) | 金额(分)                     |
| collect/remark      | String     | 备注                        |
| receipt             | List<Node> | 未结清的相关单据列表(销售单/销售退货单/红冲单) |
| receipt/type        | Number(2)  | 单据类型:1-销售单;2-销售退货单;3-红冲单  |
| receipt/no          | String     | 单据编号                      |
| receipt/code        | Number(18) | 单据代码                      |
| receipt/operatedate | String     | 操作日期:格式yyyymmdd           |
| receipt/amount      | Number(11) | 金额(分)                     |
| receipt/remark      | String     | 备注                        |

### 3.5.7 保存收款单
url:sales/collect/add

方法：POST

功能描述：保存收款单

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述           |
| ----------- | ---------- | ---- | ---- | -------------- |
| companycode | Number(18) | 必填   |      | 公司代码           |
| customcode  | Number(18) | 必填   |      | 客户代码           |
| accountcode | Number(18) | 必填   |      | 收款方式           |
| collectno   | String     | 必填   |      | 收款单号           |
| collecttype | Number(2)  | 必填   |      | 收款类型:1-收款 2-定金 |
| amount      | Number(11) | 必填   |      | 收款金额           |
| brokerage   | String     | 必填   |      | 经手人            |
| collectdate | String     | 必填   |      | 收款日期(YYYYMMDD) |
| remark      | String     | 必填   |      | 备注             |


请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| /    |      |      |

### 3.5.8 查询收款单
url:sales/collect/query

方法：POST

功能描述：根据客户、收款类型、收款方式和起止时间查询收款单

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述           |
| ----------- | ---------- | ---- | ---- | -------------- |
| companycode | Number(18) | 必填   |      | 公司代码           |
| customcode  | Number(18) | 可选   |      | 客户代码           |
| collecttype | Number(2)  | 可选   |      | 收款类型:1-收款 2-定金 |
| accountcode | Number(18) | 可选   |      | 收款方式           |
| startdate   | String     | 必填   |      | 开始日期(YYYYMMDD) |
| enddate     | String     | 必填   |      | 截止日期(YYYYMMDD) |
| pageno      | Number(11) | 可选   | 1    | 页码             |


请求样例：

响应参数：

| 参数名                 | 类型         | 参数描述            |
| ------------------- | ---------- | --------------- |
| collect             | List<Node> | 收款单列表           |
| collect/collectno   | String     | 单号              |
| collect/custom      | String     | 客户              |
| collect/amount      | Number(18) | 金额(分)           |
| collect/collecttype | Number(2)  | 收款类型:1-收款 2-定金  |
| collect/accountname | String     | 收款方式            |
| collect/accountno   | String     | 收款账号            |
| collect/collectdate | String     | 收款日期:格式yyyymmdd |

## 3.6 仓库管理

### 3.6.1 查询销售单
url:inventory/salesorder/query

方法：POST

功能描述：根据客户、销售单号、出库单号、出库状态查询销售单

版本支持：1.0.0

请求参数：

| 参数名           | 类型         | 限制   | 默认值  | 参数描述             |
| ------------- | ---------- | ---- | ---- | ---------------- |
| companycode   | Number(18) | 必填   |      | 公司代码             |
| customcode    | Number(18) | 可选   |      | 客户代码             |
| orderno       | String     | 可选   |      | 销售单号             |
| deliveryno    | String     | 可选   |      | 出库单号             |
| deliverystate | Number(2)  | 必填   |      | 出库状态:1-未完成 2-已完成 |
| pageno        | Number(11) | 可选   | 1    | 页码               |


请求样例：

响应参数：

| 参数名                      | 类型         | 参数描述                     |
| ------------------------ | ---------- | ------------------------ |
| salesorder               | List<Node> | 销售单列表                    |
| salesorder/ordercode     | Number(18) | 代码                       |
| salesorder/orderno       | String     | 单号                       |
| salesorder/custom        | String     | 客户                       |
| salesorder/saledate      | String     | 收款日期:格式yyyymmdd          |
| salesorder/deliverystate | Number(2)  | 出库状态：0-未出库，1- 出库中，2- 已出库 |

### 3.6.2 查询出库记录
url:inventory/historydelivery/query

方法：POST

功能描述：根据销售单和起止时间查询收款单

版本支持：1.0.0

请求参数：

| 参数名       | 类型         | 限制   | 默认值  | 参数描述  |
| --------- | ---------- | ---- | ---- | ----- |
| ordercode | Number(18) | 必填   |      | 销售单代码 |


请求样例：

响应参数：

| 参数名                            | 类型         | 参数描述            |
| ------------------------------ | ---------- | --------------- |
| delivery                       | List<Node> | 出库单记录           |
| delivery/deliverycode          | Number(18) | 代码              |
| delivery/deliveryno            | String     | 单号              |
| delivery/custom                | String     | 客户              |
| delivery/vehiclelicence        | String     | 车牌号             |
| delivery/brokerage             | String     | 经手人             |
| delivery/deliverydate          | String     | 出库日期:格式yyyymmdd |
| delivery/remark                | String     | 备注              |
| delivery/product               | List<Node> | 商品列表            |
| delivery/product/code          | Number(18) | 代码              |
| delivery/product/name          | String     | 名称              |
| delivery/product/depot         | String     | 仓库              |
| delivery/product/unit          | String     | 库存单位            |
| delivery/product/salenumber    | Number(11) | 销售数量            |
| delivery/product/currentnumber | Number(18) | 本次出库数量          |

### 3.6.3 查询销售单出库信息
url:inventory/deliveryinfo/query

方法：POST

功能描述：查询指定销售单的商品信息和客户等

版本支持：1.0.0

请求参数：

| 参数名       | 类型         | 限制   | 默认值  | 参数描述  |
| --------- | ---------- | ---- | ---- | ----- |
| ordercode | Number(18) | 必填   |      | 销售单代码 |


请求样例：

响应参数：

| 参数名                                  | 类型         | 参数描述 |
| ------------------------------------ | ---------- | ---- |
| deliveryinfo                         | Node       | 销售单  |
| deliveryinfo/ordercode               | Number(18) | 代码   |
| deliveryinfo/custom                  | String     | 客户   |
| deliveryinfo/product                 | List<Node> | 商品列表 |
| deliveryinfo/product/no              | String     | 编号   |
| deliveryinfo/product/code            | Number(18) | 代码   |
| deliveryinfo/product/name            | String     | 名称   |
| deliveryinfo/product/depotcode       | Number(18) | 仓库代码 |
| deliveryinfo/product/unit            | String     |      |
| deliveryinfo/product/salenumber      | Number(11) |      |
| deliveryinfo/product/deliverednumber | Number(11) |      |

### 3.6.4 商品出库
url:inventory/delivery/add

方法：POST

功能描述：输入商品的出库数量及出库相关信息（车牌号、经手人、出库日期等）后保存出库单

版本支持：1.0.0

请求参数：

| 参数名               | 类型         | 限制   | 默认值             | 参数描述 |
| ----------------- | ---------- | ---- | --------------- | ---- |
| ordercode         | Number(18) | 必填   | 销售单代码           |      |
| vehiclelicence    | String     | 必填   | 车牌号             |      |
| brokerage         | String     | 必填   | 经手人             |      |
| deliverydate      | String     | 必填   | 出库日期:格式yyyymmdd |      |
| remark            | String     |      | 备注              |      |
| product           | List<Node> |      | 商品出库明细          |      |
| product/code      | Number(18) |      | 商品代码            |      |
| product/depotcode | Number(18) |      | 仓库代码            |      |
| product/number    | Number(11) |      | 销售数量            |      |


请求样例：

响应参数：

| 参数名  | 类型   | 参数描述 |
| ---- | ---- | ---- |
| /    |      |      |

### 3.6.5 查询出库单
url:inventory/delivery/query

方法：POST

功能描述：根据客户、销售单号、出库单号和起止时间查询收款单

版本支持：1.0.0

请求参数：

| 参数名        | 类型         | 限制   | 默认值  | 参数描述           |
| ---------- | ---------- | ---- | ---- | -------------- |
| customcode | Number(18) | 可选   |      | 客户代码           |
| orderno    | String     | 可选   |      | 销售单号           |
| deliveryno | String     | 可选   |      | 出库单号           |
| startdate  | String     | 必填   |      | 开始日期(YYYYMMDD) |
| enddate    | String     | 必填   |      | 截止日期(YYYYMMDD) |
| pageno     | Number(11) | 可选   | 1    | 页码             |

请求样例：

响应参数：

| 参数名                   | 类型         | 参数描述            |
| --------------------- | ---------- | --------------- |
| delivery              | List<Node> | 出库单记录           |
| delivery/ordercode    | Number(18) | 销售单代码           |
| delivery/orderno      | String     | 销售单单号           |
| delivery/custom       | String     | 客户              |
| delivery/deliverycode | Number(18) | 出库单代码           |
| delivery/deliveryno   | String     | 出库单单号           |
| delivery/deliverydate | String     | 出库日期:格式yyyymmdd |
| delivery/state        | Number(2)  | 状态: 0-未完成 1-已完成 |

### 3.6.6 查询出库单详情
url:inventory/delivery/detail

方法：POST

功能描述：根据出库单代码查详情

版本支持：1.0.0

请求参数：

| 参数名          | 类型         | 限制   | 默认值  | 参数描述  |
| ------------ | ---------- | ---- | ---- | ----- |
| ordercode    | Number(18) | 必填   |      | 销售单代码 |
| deliverycode | Number(18) | 必填   |      | 出库单代码 |


请求样例：

响应参数：

| 参数名                     | 类型         | 参数描述 |
| ----------------------- | ---------- | ---- |
| product                 | List<Node> |      |
| product/code            | Number(18) |      |
| product/name            | String     |      |
| product/depot           | String     |      |
| product/unit            | String     |      |
| product/salenumber      | Number(11) |      |
| product/currentnumber   | Number(18) |      |

### 3.6.7 查询商品库存
url:inventory/stock/query

方法：POST

功能描述：根据商品名称，模糊查询上架的商品库存信息

版本支持：1.0.0

请求参数：

| 参数名         | 类型         | 限制   | 默认值  | 参数描述 |
| ----------- | ---------- | ---- | ---- | ---- |
| companycode | Number(18) | 必填   |      | 公司代码 |
| productname | Number(18) | 可选   |      | 商品名称 |
| pageno      | Number(11) | 可选   | 1    | 页码   |


请求样例：

响应参数：

| 参数名               | 类型         | 参数描述 |
| ----------------- | ---------- | ---- |
| stock             | List<Node> | 商品列表 |
| stock/productno   | Number(18) | 编号   |
| stock/productname | String     | 名称   |
| stock/category    | String     | 类型   |
| stock/depot       | String     | 仓库   |
| stock/unit        | String     | 单位   |
| stock/stock       | Number(11) | 数量   |