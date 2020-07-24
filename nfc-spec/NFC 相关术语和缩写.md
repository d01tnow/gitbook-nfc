# NFC 相关术语和缩写

* NFC:
* NFC-A:
* IRI: Internationalized Resource Identifier . 国际化资源标识符.
* SE: Secure Element. 安全元件.

## NCI 

* DH: Device Host. 设备主机.
* NFCC: NFC Controller. NFC 控制器.
* NFCEE: NFC Execution Environment . NFC 执行环境. 比如: SE, DH-NFCEE(DeviceHost上的NFCEE)
* T1T: NFC Forum Type 1 Tag Technical Specification. 第1类标签. ISO14443-3A. 96B~2KB, 106kbps.
* T2T: NFC Forum Type 2 Tag Technical Specification. 第2类标签. ISO14443-3A. 48B~2KB, 106kbps.
* T3T: NFC Forum Type 3 Tag Technical Specification. 第3类标签. JIS 5319-4Sony FeliCa体系. 1~9KB, 212kpbs.
* T4T: NFC Forum Type 4 Tag Technical Specification. 第4类标签.  ISO14443-4 A/B . 2KB~144KB, 106kpbs~424kbps.
* T5T: NFC Forum Type 5 Tag Technical Specification. 第5类标签. ISO15693. 32~256 B. 212/424kbps
* NFA: Nfc For Android. NfA是一组用于android平台的nfc助手。主要目的是便于在android平台上和nfc设备通信。

## 卡模拟

* HCE: 基于主机的卡模拟
* HCE Service: 在 Android 中定义了 HostApduService 接口. 用户可以实现自己的服务. 当读卡器读取卡模拟的设备时, 系统会启动正确的HCE 服务, 同读卡器交互, 完成业务交易.
* offHost: 非 Host 的卡模拟. 比如: 使用 SE.
* AID:  ISO 7816-4 定义了一种以AID(应用ID)为核心的应用服务选择方式. Android 通过 AID 调用合适的 HCE 服务. 一个 AID 最多包含16字节. 建议使用 ISO 7816-5 规范注册 AID, 以避免和其他应用发生冲突.
* AID Group: AID 群组. 某些情况下, HCE 服务需要注册多个 AID 以实现特定的应用, 并且需要确保它是所有这些 AID 的默认处理程序. AID 群组是指操作系统应将其视为一类的 AID 列表。对于 AID 群组中的所有 AID，作为一个整体看待, Android 会保证满足以下条件之一：
  - 群组中的所有 AID 都会发送到此 HCE 服务
  - 群组中的 AID 都不会发送到此 HCE 服务（例如，因为用户更倾向于使用另一项也请求处理您群组中的一个或多个 AID 的服务）
* AID Category: AID 类别. 每个 AID 群组都可以与一个类别关联. 便于管理和使用. Android可以按类别对 HCE 服务进行分组, 从而允许用户在类别级设置默认应用. 用户看的是类别, 而非具体的 AID. Android 4.4 定义了两类: CATEGROY_PAYMENT, CATEGROY_OTHER. 在任何给定时间，系统中只能启用 `CATEGORY_PAYMENT` 类别中的一个 AID 群组。