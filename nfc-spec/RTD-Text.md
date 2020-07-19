# RTD Text



## 缩略语

* BOM: Unicode Byte-Order-Mark. Unicode 字节顺序标记.
* CRLF: Carriage Return – Line Feed. 回车换行. 
* IETF: Internet Engineering Task Force. 互联网工程任务组, 是全球互联网最具权威的技术标准化组织.
* lsb: least significant bit. 最低有效位. 和大写的 LSB (Least Significant Byte)区分开.
* MIME: Multipurpose Internet Mail Extensions. 多用途互联网邮件扩充类型. 描述通过互联网传输的强类型数据的格式.
* NDEF: NFC Data Exchange Format. NFC 数据交换格式.
* RFU: Reserved for Future Use. 留作后用.
* RTD: Record Type Definition [[RTD-text]]. 记录类型定义.
* URI: Uniform Resource Identifier (e.g., http://, ftp://, mailto:, news:) 统一资源标识符.
* URL: Uniform Resource Locator (special case of a URI). 统一资源定位符.
* UTF: Unicode Transformation Format. Unicode 传输格式.

## 术语表



## Text Record

NDEF文本记录包含自由格式的纯文本. 该记录可用于描述服务或NFC论坛标签的内容.

文本记录可以作为唯一记录出现在NDEF消息[NDEF]中, 但是在这种情况下, 行为是不确定的, 需要由NDEF应用程序处理. 通常, 文本记录应与其他NDEF记录一起使用以提供说明性文本.

## 安全

出于安全考虑, 最好仅将“文本”字段用作信息描述字段. 

## NDEF 结构

### 记录映射(Record Mapping)

文本记录类型在 Well Known Type 定义为 "T" (0x54).

表-文本记录内容:

| 偏移 | 长度(bytes) | 描述                                                         |
| ---- | ----------- | ------------------------------------------------------------ |
| 0    | 1           | 状态字节. 具体内容见表-状态字节                              |
| 1    | n           | ISO/IANA 语言代码. 比如: "en_US", "zh_CN". 以 ASCII 编码. 长度由状态字节的 5..0 位指明. |
| n+1  | m           | 实际文本内容. 编码由 状态字节的第 7 bit 指明.                |

表-状态字节:

| 位数(lsb) |                                                              |
| --------- | ------------------------------------------------------------ |
| 7         | 0: 表示文本内容编码方式为 UTF-8<br>1: 表示文本内容编码方式为 UTF-16 |
| 6         | 保留. 应为 0                                                 |
| 5..0      | 文本记录内容中"语言代码"的长度.                              |

文本内容可以显示给用户. 如果存在多个文本记录, 则应显示用户首选项语言的最接近的语言的文本. 如果在单个NDEF应用程序上下文中具有相同语言代码的多个文本元素被认为是错误的.

除换行符, 换行符( 0x0D, 0x0A)和制表符(0x08)之外, 控制字符(UTF-8中的0x00-0x1F)应在显示之前删除. 标记不得嵌入(使用“ text / xhtml”或其他合适的MIME类型). 文本记录应被视为等于MIME类型 "text/plain; format=fixed".

使用 CRLF (即, 回车换行, (0x0D, 0x0A)) 作为换行符. NDEF 应用程序可以处理制表符.

除换行符和制表符外的空白应被折叠 ( 即, 多个空格字符应被视为单个空格字符).

实际文本内容长度(Bytes) = playload length - 语言代码长度(Bytes) - 1;

如果文本记录描述了一个元素, 则它应该在该元素描述之前的NDEF记录列表中出现.

## 语言代码

语言代码符合 [RFC5646] 规范. 

语言代码的长度可以用 0x3F & 文本内容的第一个字节获得.

应当容忍实际字符串中的 BOM (即，无错误条件).  生成文本记录时, 可以省略BOM. 如果省略BOM, 则字节顺序应为Big Endian(UTF-16BE).

## 附录

### 一条 RTD Text 例子: 文本记录内容 "hello, world!" (实际内容长度 13 字节)

| offset | 内容            | 解释                                                         |
| ------ | --------------- | ------------------------------------------------------------ |
| 0      | N/A             | 记录头的第1字节, 布局信息<br>IL=0(无ID_LENGTH, ID字段), SF=1(短格式, payload length=1), TNF=0x01(NFC 论坛已知类型) |
| 1      | 0x01            | 记录头的第2个字节, TYPE_LENGTH<br>表示本条记录的类型长度为 1 字节. |
| 2      | 0x10            | 记录头的第3个字节, PAYLOAD_LENGTH<br>表示本条记录的载荷长度为 16 字节. 载荷的状态 1 字节 + 语言代码名称长 2 字节+ 实际内容长度 13 字节 |
| 3      | "T"             | 记录头的第4个字节, TYPE.<br> 表示本条记录的类型为文本记录.   |
| 4      | 0x02            | 记录的 PAYLOAD 的第一个字节. <br> 文本记录的编码格式为 UTF8, 语言代码名称长度为 2. |
| 5      | "en"            | 记录的 PAYLOAD 的第 2 个字节.<br> "en" 是 "英语" 在 ISO 规范中的语言代码名称. |
| 7      | "hello, world!" | 记录的 PAYLOAD 的剩余内容.                                   |

