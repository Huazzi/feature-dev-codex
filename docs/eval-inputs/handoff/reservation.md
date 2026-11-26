# 会议室预约回执

员工在已有工作台选择会议室和时段，确认预约后可以下载回执。手机网络不稳定，员工可能再次点击确认，也可能关掉页面再打开。前台凭回执核对预约。请为这个功能写一份中文设计讨论稿。沿用现有预约系统，同一预约意图最多占用一个名额。

## 随题项目资料（离线合成切片）

预约服务由另一团队维护。其说明写明：确认成功即占用名额，提交后返回预约号；生成回执失败不会自动取消预约。工作台已有以下调用。预约系统的重试、结果查询和取消接口未包含在资料中。

```java
// DeskController.confirm：当前实现
Booking booking = reservations.confirm(employee, room, slot);
return booking.number();

// VisitorPassService.download：另一个已上线的业务调用
Pass pass = passes.get(passId);
return documents.renderPdf(pass.snapshot());
```

文档服务按传入的快照生成 PDF，可以在返回文件前报告明确失败。工作台有自己的数据库。客户端现有实现只在收到成功响应后显示预约号。前台是否允许核对其他凭证，资料中没有说明。

随题公开资料摘要：[AWS 对可安全重试 API 的工程说明](https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-APIs/)描述了调用方没有收到响应、服务方可能已执行的情形，以及服务端支持调用方请求标识时如何识别同一次操作。该摘要是两组共有的资料。
