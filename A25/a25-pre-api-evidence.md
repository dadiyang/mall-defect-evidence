# A25 修前接口直证（素材取自引擎留痕）

缺陷：会员 B 凭订单号对会员 A 的订单发起退货申请，申请行直接落库。
环境：master @ dcaa93b，mall-portal:8085。复现日期 2026-10-10。
引擎判定：mall-admin 项目轮 1，job 2550，实例 #47908 FAIL（钉桩 RTN-07）。

## 请求/响应原文（引擎留痕，会员 B 令牌）

1) 造数：注册会员 B

POST /sso/register
{"username": "mbr_r07b1009235620", "password": "RegMbr@2026", "telephone": "13915613807", "authCode": "180863"}
→ {"code": 200, "message": "注册成功"}

2) 会员 B 登录

POST /sso/login
{"username": "mbr_r07b1009235620", "password": "RegMbr@2026"}
→ {"code": 200, "message": "操作成功", "data": {"tokenHead": "Bearer ", "token": "..."}}

3) 会员 B 对会员 A 的订单发起退货申请（orderId=174 为会员 A 的订单）

POST /returnApply/create
{"orderId": 174, "productId": 256, "orderSn": "202610090101000098",
 "memberUsername": "mbr_r071009235620", "returnName": "回归退货人", "returnPhone": "...", ...}
（Authorization: B 令牌）
→ {"code": 200, "message": "操作成功", "data": 1}

## 数据库核对

oms_order_return_apply 新增申请行（id=40）指向 A 的订单 174——
申请内容全部由请求方上送，服务端原样入库。

## 服务端日志

复现时段 mall-portal 日志无异常堆栈（0 行）——服务端静默放行，无归属校验。
