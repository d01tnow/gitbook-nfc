# Record Type Definition

这个文档描述了记录类型的分类, 类型名称的命名规范, 编码方式, 比较方法和错误处理内容.

## 缩略语

| 缩略语 | 全称                                   | 描述                                                         |
| ------ | -------------------------------------- | ------------------------------------------------------------ |
| NID    | Namespace Identifier.                  | URN 命名空间的唯一标识                                       |
| NSS    | Namespace Specific String.             | URN中的NID之后的部分.                                        |
| RTD    | Record Type Definition.                | NFC 特定的记录类型和名称. 记录的TNF=0x01                     |
| URI    | Uniform Resource Identifier.           | 统一资源标识符.                                              |
| URN    | Uniform Resource Name.                 | 统一资源名称. 一个特殊的 URI. [RFC2141]. 它使用 "urn" 作为 URI scheme |
| MIME   | Multipurpose Internet Mail Extensions. | 多用途互联网邮件扩充类型. 描述通过互联网传输的强类型数据的格式. |

## 术语

* NFC Forum Device: 一种针对NFC论坛定义的在至少一种通讯模式下支持至少一种通讯协议的设备. 当前定义的NFC论坛设备: NFC通用设备, NFC标签设备和NFC读卡器.
* NDEF application: NDEF 应用程序. 安装在 NFC 论坛设备上的逻辑的, 高层的应用程序. 它使用 NDEF 格式信息与其他 NFC 论坛设备交换数据.
* NDEF generator: 一个以 NDEF 消息格式封装应用程序定义的载荷的实体或模块.
* NDEF parser: 一个解析 NDEF 消息并将有效载荷移交给应用程序的实体或模块.
* NDEF playload: 一条 NDEF 记录内携带的应用程序数据.
* NDEF record: NDEF 记录包含由类型, 长度和可选的 ID 描述的有效载荷.
* NFC Forum Tag: 支持 NDEF 的非接触式标签或(智能) 卡.
* NFC Reader Device: NFC 读卡器设备. 支持 读取器/写入器 操作方式的 NFC 论坛设备. 它还可以支持启动器.
* NFC Tag Device: 至少支持一种用于卡模拟和NDEF的通讯协议的 NFC 论坛设备.
* NFC Universal Device: 支持以下操作方法的NFC论坛设备: 发起方, 目标和读取器/写入器. 它还可以支持卡模拟器.

## Record Type 

NDEF 记录的 TYPE 字段包含 NDEF 记录类型的名称. NDEF 应用程序使用 NDEF 记录类型名称标识 NDEF 记录内容的语义和结构.

NDEF 记录类型名称可以用几种格式指定, 这些格式称为类型名称格式. 它们由 NDEF 记录头中 TNF 域指示.

* TNF 表

| Type Name Format                   | Value | 描述                                                         |
| ---------------------------------- | ----- | ------------------------------------------------------------ |
| Empty                              | 0x00  | 表示没有类型和playload. <br>必须: TYPE_LENGTH=ID_LENGTH=PLAYLOAD_LENGTH = 0 |
| NFC Forum well-known type[NFC RTD] | 0x01  |                                                              |
| Media-type as defined in RFC2046   | 0x02  |                                                              |
| Absolute URI as defined in RFC3986 | 0x03  |                                                              |
| NFC Forum external type [NFC RTD]  | 0x04  |                                                              |
| Unknown                            | 0x05  | 必须: TYPE_LENGTH=0                                          |
| Unchanged                          | 0x06  | 必须用在所有中间记录分块和结尾记录分块中.<br>必须: TYPE_LENGTH=0 |
| Reserved                           | 0x07  | 保留, 不能设置.                                              |

NDEF 记录类型名称可以由 NFC 论坛和第三方定义. 以下各节中定义了控制RTD类型名称空间的规则.

### NFC Forum Well Know Type

NFC Forum Well Know Type 的相关属性:

* 该类型由 NDEF 记录头的 TNF=0x01 指示  .
* 它是一个 URN. 定义于 [RFC 2141]. NID 为 "nfc".
* 它的 URN 前缀是 "wkt:". 但是, 编码在消息中时, 写入相对的 URI, 而不是完整的 URI. 例如: 完整的 URI 为 "urn:nfc:wkt:abc" , 被编码为 "abc".

### NFC Forum Global Type

NFC 论坛全局类型由 NFC 论坛定义并管理. 其他组织不允许定义和重定义.

全局类型由**大写字母**开头.

### NFC Forum Local Type

NFC 论坛本地类型由**小写字母或者数字**开头.

NFC 论坛本地类型可以在另一个 NDEF 记录的上下文中使用. 当应用程序上下文可用时, 应用程序才可以处理这些类型. 

RTD 或 应用程序定义用于解释本地类型的上下文. 本地类型可以由另一个应用程序在不同的上下文和不同的内容中重用.

## NFC Forum External Type

外部类型名称适用于希望自行分配名称空间以用于其自身目的的组织.

相关属性:

* 该类型由 NDEF 记录头的 TNF=0x04 指示.
* 它的前缀: "urn:nfc:ext:"
* 它的格式: "urn:nfc:ext:组织名:类型名 . 例如: urn:nfc:ext:example.com:abc

## Record Type Names

完整的类型名使用 ABNF (扩展巴科斯范式) 定义在 [RFC2234]. 全局类型名称和外部类型名称的二进制编码是 ASCII 编码的一部分(范围: [0x20, 0x7E]).

### Binary Encoding

URN NID 和 NFC NSS前缀不包含在 NDEF 二进制格式中.但是, 如果RTD以其他格式 ( 例如XML ) 使用, 则URN以绝对URN格式给出.

本规范**未定义** NDEF 载荷的编码. NDEF记录内容的编码由定义 NDEF记录类型的文档中指定.

### NFC 论坛类型中 '%' 编码

* Well Know Types ( 包括全局和本地 ) 不应使用百分号编码.
* 扩展类型不应使用百分号编码. 如果使用, 应用程序应该先将字符串转换为 utf8 编码, 然后再进行百分号编码.

### 记录类型名的比较

记录类型名的比较是逐字符, 区分大小写的.

## 错误处理

如果一条记录的类型名称不符合类型名称规范, 则该条记录被忽略.

