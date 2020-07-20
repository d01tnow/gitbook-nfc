# RTD Smart Poster

智能海报是 NFC 技术的一种关键使用场景之一. 其"智能"表现在, 它可以包含可以在 NDEF 应用程序中触发动作的动作记录(Action record). 比如: 通过浏览器打开网页, 或者通过短信服务发送短信等.

智能海报的概念是围绕 URI 构建的.

## NDEF 结构

### Record Mapping

智能海报的载荷是一条 NDEF 消息. 它包含几条 NDEF 记录.

* 标题记录: 服务的标题. 一个智能海报可以有任意数量的标题记录. 但是, 不允许同一个语言标识有 2 条及以上的标题记录.
* URI 记录: 一个智能海报**必须只能有且只有一条** URI 记录. 
* 动作记录: 它建议 NDEF 应用程序如何处理 URI 记录内容. 当动作记录不存在时, NDEF 应用程序来决定如何处理智能海报内容. 如果动作记录存在, 应用程序应该把它作为强烈推荐对待, 虽然可以忽略动作记录的建议, 但是, 很可能造成不同设备间不同的用户体验.
* 图标记录: 智能海报的图标. 一条智能海报中可以包含任意个图标记录. 当智能海报中包含多个 MIME-typed 图标记录时, 应用程序可以显示并让用户选择, 这依赖 NFC 设备的能力.
* 大小记录(Size record): 如果 URI 指向一个外部实体(比如: 通过 URL), 大小记录可以指示该实体的大小. 
* 类型记录: 如果 URI 指向一个外部实体(比如: 通过 URL), 类型记录可以指明实体的 MIME 类型.

可能还有可以用于特定应用程序的处理的其他记录. 例如, 一个NDEF应用程序可能包括一个使用适当MIME类型的vCard联系卡. NDEF应用程序可以忽略在智能海报内的任何额外的记录.

#### URI 记录

不要求 NDEF 应用程序支持特定的 URI 协议. 但是, 如果应用程序不支持智能海报引用的协议, 应用程序应该忽略整个智能海报. 

#### 标题记录

标题记录是一个 RTD Text 的实例.

NDEF 应用程序应该显示标题记录的文本给用户. 

#### 动作记录

动作记录的 NFC 本地类型名称为: "act". 动作记录应该定义在一条智能海报的本地域中. 独立的动作记录, 被当做错误.

动作记录的内容仅有 1 Byte. 用于记录动作记录预定义的代码. 具体内容见 表-Action-Record-Values.

表-Action-Record-Values:

| 代码       | 动作                                                         |
| ---------- | ------------------------------------------------------------ |
| 0x00       | 默认动作(比如: 发送短信, 以浏览器打开, 拨打电话等)           |
| 0x01       | 保存.(比如: 保存短信到收件箱, 保存 URI 到浏览器的书签, 保存电话号码到联系人列表等) |
| 0x02       | 打开编辑. (比如: 打开短信编辑器, 打开 URI 编辑器, 打开联系人编辑器等) |
| 0x03..0xFF | 保留. 当做 0x00 处理.                                        |

NDEF 应用程序可以忽略动作记录的建议. 如果默认动作未定义, 那么, 应用程序可以显示一个可选列表让用户选择.


