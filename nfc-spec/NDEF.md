以下 NFC Data Exchange Format(NDEF) Technical Specification.pdf 简称为 NDEF-SPEC.
NDEF规范定义了用来在 NFC 设备间或 NFC 设备与 NFC 标签间交换信息的消息封装格式.  NDEF 是轻量级的二进制的消息格式.
NDEF 消息的结构图(截取自 NDEF-SPEC 2.3)
![[ndef-message.png]]
一条记录的结构布局(截取自 NDEF-SPEC 3.2)
![[record-layout.png]]

<img src="/Users/wangzhijun/me/gitbook/nfc/nfc-spec/record-layout.png" alt="image-20200712132255088" style="zoom:50%;" />

* 记录布局说明

| 名称 | 长度(bit) | 取值及含义 |
| -- | -- | -- |
| MB(Message Begin) | 1 | 1: 消息开始, 0: 非消息开始 |
| ME(Message End) | 1 | 1: 消息结束, 0: 非消息结束 |
| CF(Chunk Flag) | 1 | 1: 记录的分块, 0: 非记录的分块. 具体结构见后续说明. |
| SR(Short Length) | 1 | 1: PLAYLOAD LENGTH=1. 0: PLAYLOAD LENGTH=4 |
| IL(ID Length is present) | 1 | 1: 记录中有 ID_LEGTH 字段(1Byte). 0: 记录中没有 ID_LENGTH 和 ID域. |
| TNF(Type Name Format) | 3 | 取值见 TNF 表. 描述 playload 类型名称格式. |

* Chunk 说明
  * 初始分块: CF=1. 不管 PLAYLOAD_LENGTH 是否为0, 整个分块载荷的类型必须在 TYPE 域中指定. ID 域可以用于携带整个分块载荷的标识符. PLAYLOAD_LENGTH 字段**仅指示本分块**的载荷的长度.
  * 中间分块: CF=1. TYPE_LENGHT=0, IL = 0. TNF = 0x06. PLAYLOAD_LENGTH 字段**仅指示本分块**的载荷的长度.
  * 结束分块: CF=0. TYPE_LENGHT=0, IL = 0. TNF = 0x06. PLAYLOAD_LENGTH 字段**仅指示本分块**的载荷的长度.

* TNF 表

| Type Name Format                   | Value | 描述                                                         |
| ---------------------------------- | ----- | ------------------------------------------------------------ |
| Empty                              | 0x00  | 表示没有类型和playload. <br>必须: TYPE_LENGTH=ID_LENGTH=PLAYLOAD_LENGTH = 0 |
| NFC Forum well-known type[[record-type-definition]] | 0x01  |                                                              |
| Media-type as defined in RFC2046   | 0x02  |                                                              |
| Absolute URI as defined in RFC3986 | 0x03  |                                                              |
| NFC Forum external type [[record-type-definition]]  | 0x04  |                                                              |
| Unknown                            | 0x05  | 必须: TYPE_LENGTH=0                                          |
| Unchanged                          | 0x06  | 必须用在所有中间记录分块和结尾记录分块中.<br>必须: TYPE_LENGTH=0 |
| Reserved                           | 0x07  | 保留, 不能设置.                                              |

