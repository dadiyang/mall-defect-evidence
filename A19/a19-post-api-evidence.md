# A19 修后接口直证（修复构建 a4fb3c4 上的实测）

修复：cancelOrder 取消前比对当前登录会员与订单会员（对齐 deleteOrder 既有归属校验模式）。
环境：master @ dcaa93b + fix/A19-cancel-order-ownership（a4fb3c4），mall-portal:8085。日期 2026-10-10。

## 引擎复验（判定权威）

mall-admin 项目轮 1，job 2574，实例 #47978 PASS：
会员 B 取消会员 A 的待付款订单被拒，数据库核对订单保持 status=0（待付款）。

## 请求/响应原文（同一修复构建上的补充实拍探针，与引擎判定一致）

会员 B 令牌取消会员 A 的订单：

POST /order/cancelUserOrder?orderId=（A 的待付款订单id）
（Authorization: B 令牌）
→ {"code": 500, "message": "不能取消他人订单！"}

数据库核对：该订单 member_id 仍为 A，status=0（待付款，未被关闭）。

## 服务端日志

修复构建上该请求走业务拒绝分支（ApiException→GlobalExceptionHandler），
无系统异常堆栈——拒绝语义明确。
