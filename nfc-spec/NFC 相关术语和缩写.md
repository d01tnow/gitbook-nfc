# NFC 相关术语和缩写

* NFC:
* NFC-A:
* IRI: Internationalized Resource Identifier . 国际化资源标识符.
* SE: Secure Element. 安全元件.
* eSE: 嵌入式的安全元件.
* UICC: Universal Integrated Circuit Card. 通用集成电路. 指的是在移动电话中的智能卡.
* SIM: Subscribe Identity Module. 用于存储用户身份识别数据, 短信, 电话号码的智能卡. 是 UICC 的一种.

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

## 金融卡IC卡规范

* DDF: 目录定义文件. PBOC3 [IC卡规范第4部分](http://www.docin.com/p-50918524.html)中6.2.1.5规定, 终端支持两种放式建立候选应用列表:  先用目录选择方式, 失败后使用 AID 列表选择方式. 目录选择方式下, 终端从卡片读取一个文件, 该文件中记录了卡片支持的应用的列表, 终端将卡片的应用列表和自己支持的应用列表取交集, 建立共同支持的应用的候选列表. 而 AID 列表对于卡和终端都是必备的. 终端会遍历自己支持的每个应用, 向卡片发送 SELECT 命令, 如果卡片也支持该应用, 则终端将该应用加入到共同支持的应用的候选列表. 终端会按优先级依次选择应用.
* PSE: Payment System Environment . 接触卡, DDF 为: "1PAY.SYS.DDF01".
* PPSE: Proximity Payment System Environment . 近场支付系统环境. 对于非接触卡选择的支付系统环境的 DDF: "2PAY.SYS.DDF01"

## 7816-5

* DF: Dedicated File, 等同于计算机中的目录文件. DF还会有5-16个字节长的名字, 也叫做AID. 如果某个DF下没有子DF, 只有若干EF, 那么这个DF也被称作ADF. 如果某个DF下除了有EF之外, 还有子DF, 那么这个父级的DF也被称作DDF。在文件选择操作过程中，对于DF的选择可以通过FID或者AID来实现，而对于EF的选择只有FID的方式. [参考](https://blog.csdn.net/u012241570/article/details/89377210)
* EF: Elementary File. 基本信息文件. EF还会有一个5位长（范围从1到30）的短文件标识, 就是SFI.
* FID: 无论是DF还是EF都会有对应的两个字节长ID标识，也就是所谓的FID.
* MF: 特殊的 DF. 相当于计算机中的根目录. 默认的 FID 为 0x3F00. 
* AID: 即唯一标识一个应用, 分为两部分, RID(5字节)+PIX（最多11字节）
* RID: 注册标识符, 由ISO组织来分配, 标识一个全球唯一的应用提供商, 一般是分配给卡组织. 比如分配给银联,RID=0xA000000333 
* PIX: 扩展应用标识符, 一般是由应用提供商自己定义. 比如: 银联定义的

> PBOC规范, 常见的AID为(十六进制)：
>
> A0 00 00 03 33  01 01 01    PBOC借记应用
>
> A0 00 00 03 33  01 01 02    PBOC贷记应用
>
> A0 00 00 03 33  01 01 03    PBOC准贷记应用
>
> A0 00 00 03 33  01 01 06    PBOC纯电子现金应用

  

## 卡模拟

* HCE: 基于主机的卡模拟
* HCE Service: 在 Android 中定义了 HostApduService 接口. 用户可以实现自己的服务. 当读卡器读取卡模拟的设备时, 系统会启动正确的HCE 服务, 同读卡器交互, 完成业务交易.
* offHost: 非 Host 的卡模拟. 比如: 使用 SE.
* AID:  ISO 7816-4 定义了一种以AID(应用ID)为核心的应用服务选择方式. Android 通过 AID 调用合适的 HCE 服务. 一个 AID 最多包含16字节. 建议使用 ISO 7816-5 规范注册 AID, 以避免和其他应用发生冲突.
* AID Group: AID 群组. 某些情况下, HCE 服务需要注册多个 AID 以实现特定的应用, 并且需要确保它是所有这些 AID 的默认处理程序. AID 群组是指操作系统应将其视为一类的 AID 列表。对于 AID 群组中的所有 AID, 作为一个整体看待, Android 会保证满足以下条件之一：
  - 群组中的所有 AID 都会发送到此 HCE 服务
  - 群组中的 AID 都不会发送到此 HCE 服务（例如, 因为用户更倾向于使用另一项也请求处理您群组中的一个或多个 AID 的服务）
* AID Category: AID 类别. 每个 AID 群组都可以与一个类别关联. 便于管理和使用. Android可以按类别对 HCE 服务进行分组, 从而允许用户在类别级设置默认应用. 用户看的是类别, 而非具体的 AID. Android 4.4 定义了两类: CATEGROY_PAYMENT, CATEGROY_OTHER. 在任何给定时间, 系统中只能启用 `CATEGORY_PAYMENT` 类别中的一个 AID 群组。