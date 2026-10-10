# A20 修后接口直证（修复构建 7732af3 上的实测）

修复：detail 查询详情前比对当前登录会员与订单会员（对齐 deleteOrder 既有归属校验模式）。
环境：master @ dcaa93b + fix/A20-order-detail-ownership（7732af3），mall-portal:8085。日期 2026-10-10。

## 引擎复验（判定权威）

mall-admin 项目轮 1，job 2577，实例 #47982 PASS：
会员 B 查会员 A 的订单详情被拒（code≠200）。

## 请求/响应原文（同一修复提交构建上的补充实拍探针，与引擎判定一致）

会员 B 令牌查会员 A 的订单：

GET /order/detail?orderId=（A 的订单id）
（Authorization: B 令牌）
→ {"code": 500, "message": "不能查看他人订单详情！"}

本人查询自己的订单详情不受影响（引擎守卫用例照常通过）。

## 服务端日志

修复构建上该请求走业务拒绝分支（ApiException→GlobalExceptionHandler），无系统异常堆栈。
