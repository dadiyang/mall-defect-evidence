# A38 修前接口直证（基线包 dcaa93b，探针＋引擎留痕）

缺陷：任意登录会员调用 POST /portal/order/cancelTimeOutOrder，一次即批量关闭
所有会员的超时未付款订单。环境：master @ dcaa93b，mall-portal:8085。日期 2026-10-10。
引擎判定：mall-admin 项目轮 1，job 2594，实例 #48006 FAIL（钉桩 ORD-15）。

## 探针直证（基线包在位，会员 A/B 均新注册）

前置（接口造数）：管理端把订单超时设为 1 分钟；会员 A 下单（订单 351，status=0 待付款）；等待 65 秒。

1) A 单在 B 调用前（数据库核对）：

   {"id": 351, "member_id": 460, "status": 0}

2) 会员 B（mbr_p38b…）用自己的令牌调用超时关闭端点：

   POST /order/cancelTimeOutOrder
   （Authorization: B 令牌）
   → http=200 {"code": 200, "message": "操作成功"}

3) A 单在 B 调用后（数据库核对）：

   {"id": 351, "member_id": 460, "status": 4}

会员 A 与调用者 B 无任何关系，A 的待付款订单被 B 的一次调用关闭（status 0→4），
A 失去付款机会；响应只报"操作成功"，看不出关闭了别人的订单。

## 服务端日志

复现时段 mall-portal 日志无异常堆栈——服务端全局扫描关闭属"正常执行"，无任何越权拦截。
