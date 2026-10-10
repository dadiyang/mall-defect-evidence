# A20 修前接口直证（素材取自引擎留痕）

缺陷：会员 B 凭订单号查看会员 A 的订单详情，返回完整订单含收货人姓名、电话、地址。
环境：master @ dcaa93b，mall-portal:8085。复现日期 2026-10-10。
引擎判定：mall-admin 项目轮 1，job 2549，实例 #47892 FAIL（钉桩 ORD-05）。

## 请求/响应原文（引擎留痕，会员 B 令牌）

1) 造数：注册会员 B

POST /sso/register
{"username": "mbr_o05b1009235408", "password": "RegMbr@2026", "telephone": "13915612487", "authCode": "061716"}
→ {"code": 200, "message": "注册成功"}

2) 会员 B 登录

POST /sso/login
{"username": "mbr_o05b1009235408", "password": "RegMbr@2026"}
→ {"code": 200, "message": "操作成功", "data": {"tokenHead": "Bearer ", "token": "..."}}

3) 会员 B 查会员 A 的订单详情（orderId=152 为会员 A 的订单）

GET /order/detail?orderId=152
（Authorization: B 令牌）
→ {"code": 200, "message": "操作成功", "data": {"id": 152, "memberId": 144,
"orderSn": "202610090101000076", "memberUsername": "mbr_o051009235408",
"totalAmount": 999.0, "payAmount": 999.0, ... 含收货人姓名/电话/省市区详细地址 ...}}

引擎断言记录：返回单 id=152 收货人=回归收货人 电话=13800009235408（A 的收货信息）。

## 服务端日志

复现时段 mall-portal 日志无异常堆栈（0 行）——服务端静默放行，无归属校验。
