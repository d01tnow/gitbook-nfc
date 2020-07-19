# RTD URI 

URI 记录类型

## Records Mapping

* URI 记录类型: "U", NDEF 的二进制编码 0x55.
* URI 记录内容: 包含 "标识代码" ("Identifier code") 和 "URI 字段" ("URI Field")

表-URI-Record-Contents

| 名称     | Offset | 长度(Bytes) | 值           | 描述                                     |
| -------- | ------ | ----------- | ------------ | ---------------------------------------- |
| 标识代码 | 0      | 1           | URI 标识代码 | 详见表-URI-Identifier-Code               |
| URI 字段 | 1      | N           | UTF-8 字符串 | 余下的URI 或 当标识代码=0时, 表示整个URI |

表-URI-Identifier-Code

预定义的一些常见协议的 URI 前缀.

| 代码    | Hex        | 协议                                     |
| ------- | ---------- | ---------------------------------------- |
| 0       | 0x00       | N/A. 未做预处理. URI字段包含未加密的URI. |
| 1       | 0x01       | http://www.                              |
| 2       | 0x02       | https://www.                             |
| 3       | 0x03       | http://                                  |
| 4       | 0x04       | https://                                 |
| 5       | 0x05       | tel:                                     |
| 6       | 0x06       | mailto:                                  |
| 7       | 0x07       | ftp://anonymous:anonymous@               |
| 8       | 0x08       | ftp://ftp.                               |
| 9       | 0x09       | ftps://                                  |
| 10      | 0x0A       | sftp://                                  |
| 11      | 0x0B       | smb://                                   |
| 12      | 0x0C       | nfs://                                   |
| 13      | 0x0D       | ftp://                                   |
| 14      | 0x0E       | dav://                                   |
| 15      | 0x0F       | news:                                    |
| 16      | 0x10       | telnet://                                |
| 17      | 0x11       | imap:                                    |
| 18      | 0x12       | rtsp://                                  |
| 19      | 0x13       | urn:                                     |
| 20      | 0x14       | pop:                                     |
| 21      | 0x15       | sip:                                     |
| 22      | 0x16       | sips:                                    |
| 23      | 0x17       | tftp:                                    |
| 24      | 0x18       | btspp://                                 |
| 25      | 0x19       | bt12cap://                               |
| 26      | 0x1A       | btgoep://                                |
| 27      | 0x1B       | tcpobex://                               |
| 28      | 0x1C       | irdaobex://                              |
| 29      | 0x1D       | file://                                  |
| 30      | 0x1E       | urn:epc​\:id\:                            |
| 31      | 0x1F       | urn:epc:tag:                             |
| 32      | 0x20       | urn:epc:pat:                             |
| 33      | 0x21       | urn:epc:raw:                             |
| 34      | 0x22       | urn:epc:                                 |
| 35      | 0x23       | urn:nfc:                                 |
| 36..255 | 0x24..0xFF | 留作后用                                 |

### URI 字段

该字段根据[RFC 3987]提供URI(因此该URI实际上是国际化的资源标识符(IRI), 但是由于遗留原因, 本规范使用了遗留术语“URI”. 

URI 字段的编码应该是 UTF-8. 除非, URI scheme 规定了特定编码.

URI 字符仅在 7-bit US-ASCII 空间内定义. 所以, NDEF 应用程序应该将 UTF-8 的 IRI 字符串转换为 7-bit US-ASCII 字符串. 这个编码已经在[RFC 3987]和IDN [RFC 3492]文档中定义. 对于不同的方案, 编码可能是不同的.

URI可能包含UTF-8字符. 但是, Internet不能处理它们, 因此在使用URI之前需要进行转换. 对于大多数设备, 这种转换由应用程序处理. 

#### 错误处理

包含从0x0到0x1F 的任何字符值的URI都被当做无效URI, URI记录将被丢弃. 任何无效的UTF-8序列都应该被认为是错误的, 并且应该丢弃整个URI记录.

## 附录

### 例子

| Offset | 内容                                   | 描述                                                         |
| ------ | -------------------------------------- | ------------------------------------------------------------ |
| 0      | 0xD1                                   | NDEF 头, 第1个字节, 布局信息<br>MB=1,ME=1,SR=1,TNF=0x01      |
| 1      | 0x01                                   | NDEF 头, 第2个字节, TYPE LENGTH<br>类型长度: 0x01, 1Byte     |
| 2      | 0x08                                   | NDEF 头, 第3个字节, PAYLOAD LENGTH<br/>载荷长度: 0x08, 8Bytes |
| 3      | 0x55                                   | NDEF 头, 第4个字节, TYPE<br/>类型: "U", 表示 "URI 记录"      |
| 4      | 0x01                                   | NDEF 载荷, URI 记录内容<br>标识代码: 0x01, 表示: "http://www." |
| 5      | 0x6e 0x66 0x63 0x2e <br>0x63 0x6f 0x6d | NDEF 载荷, URI 记录内容<br/>URI字符串: "nfc.com"             |

