# RegisteredAidCache

## 说明

* AID 最长 16 字节.
* 代码中将前缀形式的, 以'\*' 结尾的 AID(长度 < 16), 称为 root. 比如: "A000000003\*" 是 前缀root. "A000000003", "A00000000301\*", "A0000000030102" 都称做冲突的 子AID(conflicting children AIDs)
* 代码中将前缀形式的, 以'\#' 结尾的 AID(长度 < 16)

## 成员函数

* ```java
  // 解决 AID 冲突, 返回解决的AID信息(有可能还有冲突)
  // 解决方法如下:
  // 如果冲突列表中有优先的前景服务(preferred foreground service), 则该服务胜出.
  // 否则, 如果有优先的支付服务(preferred payment service), 则该服务胜出.
  // 否则, 返回冲突列表.
  // @Param conflictingServices: 冲突服务列表
  // @Param makeSingleServiceDefault: 在解决列表的Size=1 时, 是否设置AidResolveInfo.defaultService=AidResolveInfo.services.get(0)
  AidResolveInfo resolveAidConflictLocked(Collection<ServiceAidInfo> conflictingServices,                                             boolean makeSingleServiceDefault)
  ```

* ```java
  
  AidResolveInfo resolveAidConflictLocked(ArrayList<ServiceAidInfo> aidServices, ArrayList<ServiceAidInfo> conflictingServices)
  ```

