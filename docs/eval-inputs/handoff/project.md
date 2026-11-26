# 随题资料

这是合成的后端项目切片。以下为现有调用点的源代码摘录，未提供的部署或前端行为均未知。

```java
// ProfileController.me
return profiles.load(passport.currentUser(request));

// ImportService.start
boolean acquired = redis.setIfAbsent("import:" + job.id(), job.id(), Duration.ofMinutes(10));
if (acquired) queue.publish(job);

// ReservationService.confirm (reservation and receipt share a MySQL transaction)
@Transactional
void confirm(Id id) {
    if (reservations.markConfirmed(id) != 1) throw new Conflict();
    receipts.insert(id);
}
```

工作台与预览页请求相同的 API 主机、路径和方法。普通登录凭据由浏览器携带。此资料包未包含客户端源码。运维提供共享 Redis 和 MySQL，未提供故障切换配置。

本次可用的公开资料摘要（用于离线比较，两版取得相同证据）：

- [Spring Security SwitchUserFilter](https://github.com/spring-projects/spring-security/blob/6.5.x/web/src/main/java/org/springframework/security/web/authentication/switchuser/SwitchUserFilter.java) 在目标身份的权限中保留原身份，退出切换时可恢复它。
- [Spring Security JdbcOneTimeTokenService](https://github.com/spring-projects/spring-security/blob/6.5.x/core/src/main/java/org/springframework/security/authentication/ott/JdbcOneTimeTokenService.java) 以数据库删除的影响行数判断是否取得一次性令牌，再检查过期。
- [AWS 幂等 API 实践](https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-APIs/) 描述服务端已执行但客户端未收到响应的情形，以及使用相同请求标识重试的处理。
