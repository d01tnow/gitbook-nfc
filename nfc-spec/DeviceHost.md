# DeviceHost

DeviceHost.java 文件中定义了 NCI 相关接口. 它在 NFC Service 层. 它的实现类是 [NativeNfcManager](http://116.236.177.98:38080/source/xref/android-nfc/packages/apps/Nfc/nci/src/com/android/nfc/dhimpl/NativeNfcManager.java#37). JNI 实现 [NativeNfcManager](http://116.236.177.98:38080/source/xref/android-nfc/packages/apps/Nfc/nci/jni/NativeNfcManager.cpp). 

## DeviceHost Interface

```java
public interface DeviceHost {
  /**
     * Called at boot if NFC is disabled to give the device host an opportunity
     * to check the firmware version to see if it needs updating. Normally the firmware version
     * is checked during {@link #initialize(boolean enableScreenOffSuspend)},
     * but the firmware may need to be updated after an OTA update.
     *
     * <p>This is called from a thread
     * that may block for long periods of time during the update process.
     */
    public boolean checkFirmware();
	/**
		* 打开 NFC
		* @return True: 成功
		*/
    public boolean initialize();
	/**
		* 关闭 NFC
		* @return True: 成功
		*/
    public boolean deinitialize();
	/**
		* 获取驱动名称
		* @return 驱动名称. 实现中返回字符串常量: DRIVER_NAME
		*/
    public String getName();
	/**
		* 开始轮询和监听 
		* @param params 
				NfcDiscoveryParameters
					techMask: 技术标识的掩码. 默认值 0.
					enableLowPowerPolling: 是否启用低电量时轮询. 默认值 true.
					enableReaderMode: 是否启用读卡器模式. 默认值 false.
					enableHostRouting: 是否启用卡模拟. 默认值 false.
					enableP2p: 是否启用P2P.默认值 false.
		* @param restart : 是否重启
		* @return void
		*/
    public void enableDiscovery(NfcDiscoveryParameters params, boolean restart);
	/**
		* 停止轮询和监听
		* @return void
		*/
    public void disableDiscovery();
	/**
		* 发送原始数据帧
		* @param data: 数据
		* @return 是否成功. true: 成功
		*/
    public boolean sendRawFrame(byte[] data);
	/**
		* 添加一条到 EE 的 aid 路由. jni 的实现在 jni/RoutingManager.cpp. 
		* @param aid: aid的值
		* @param route: EE 的 ID. 比如: DH-EE为 0x00.
				EE 的 ID, 可以通过 native 接口 获取.
        @see AidRoutingManager.java
        int doGetDefaultRouteDestination(); 获取默认的 HCE 
    		int doGetDefaultOffHostRouteDestination(); 获取默认 OffHost 目的地
    		byte[] doGetOffHostUiccDestination(); 获取 OffHost-UICC 目的地
    		byte[] doGetOffHostEseDestination(); 获取 OffHost-eSE 目的地
    		int doGetAidMatchingMode(); 获取 AID 匹配模式
    		int doGetDefaultIsoDepRouteDestination(); 获取默认的 ISO-DEP 目的地
    		这些的配置通过 NfcConfig 获得.
    * @param aidInfo: prefix or suffix aid. 
    		@RegisteredAidCache.java 常量: AID_ROUTE_QUAL_PREFIX, AID_ROUTE_QUAL_SUBSET
		* @return 是否成功. true: 成功
		*/
    public boolean routeAid(byte[] aid, int route, int aidInfo);
	/**
		* 根据 AID 移除一条路由
		* @param aid: 将被移除的路由的 AID
		* @return 是否成功. true: 成功
		*/
    public boolean unrouteAid(byte[] aid);
	/**
		* 确认更新路由
		* @return 是否成功. true: 成功
		*/
    public boolean commitRouting();
	/**
		* 注册 Type 3 tag 的识别码
		* @param t3tIdentifier: 识别码(10 或 18 字节)
		* @return void
		*/
    public void registerT3tIdentifier(byte[] t3tIdentifier);
	/**
		* 取消注册指定的 Type 3 tag 的识别码
		* @param t3tIdentifier: 识别码(10 或 18 字节)
		* @return void
		*/
    public void deregisterT3tIdentifier(byte[] t3tIdentifier);
	/**
		* 清除 T3t的识别码缓存. 直接清除 Service 层的缓存, 无需调用 NCI 接口.
		* @return 
		*/
    public void clearT3tIdentifiersCache();
	/**
		* 获取 LF_T3T_MAX 的值
		* @param
		* @return 
		*/
    public int getLfT3tMax();

    public LlcpConnectionlessSocket createLlcpConnectionlessSocket(int nSap, String sn)
            throws LlcpException;
    public LlcpServerSocket createLlcpServerSocket(int nSap, String sn, int miu,
            int rw, int linearBufferLength) throws LlcpException;
    public LlcpSocket createLlcpSocket(int sap, int miu, int rw,
            int linearBufferLength) throws LlcpException;
    public boolean doCheckLlcp();
    public boolean doActivateLlcp();
  
	/**
		* 重置 Tag 所有的传输超时时间
		* 内部调用 NfcTag::getInstance().resetAllTransceiveTimeouts();
		* @return void
		*/
    public void resetTimeouts();
	/**
		* 设置某个 tech 的传输超时. 
		* @param technology: Tag 的 tech
		* @param timeout: 超时时间. 单位: TODO: 待查. 
		* @return 
		*/
    public boolean setTimeout(int technology, int timeout);
	/**
		* 获取 Tag 某个 tech 的传输超时时间.
		* @param technology: Tag 的 tech
		* @return 传输超时时间
		*/
    public int getTimeout(int technology);
	/**
		* 终止操作. 
		* @param msg: 消息.
		* @return void
		*/
    public void doAbort(String msg);
	/**
		* 判断 Tag tech 是否支持 ReadOnly. NativeNfcManager.java 直接实现了. 不调用 NCI.
		* @param technology: Tag 的 tech
		* @return 是否支持 ReadOnly. true: 支持
		*/
    boolean canMakeReadOnly(int technology);
	/**
		* 获取指定的 Tag tech 的最大收发长度. NativeNfcManager.java 直接实现了. 不调用 NCI.
		* @param technology: Tag 的 tech
		* @return 最大收发长度, 单位: 字节
		*/
    int getMaxTransceiveLength(int technology);
	/**
		* 获取支持的 AID 路由表最大长度.
		* @return 路由表最大长度. 单位: 字节
		*/
    public int getAidTableSize();

    void setP2pInitiatorModes(int modes);
    void setP2pTargetModes(int modes);
	/**
		* 判断是否支持收发 APDU 扩展长度(>261字节)的数据. NativeNfcManager.java 直接实现了. 不调用 NCI.
		* @return true: 支持
		*/
    boolean getExtendedLengthApdusSupported();

    int getDefaultLlcpMiu();
    int getDefaultLlcpRwSize();
	/**
		* 转储到文件中
		* @param fd: 文件描述符
		* @return void
		*/
    void dump(FileDescriptor fd);
	/**
		* 设置在屏幕关闭状态下, 控制器处于全功率状态. 在一段时间无活动后, 控制器会进入小睡模式.
			@see POWER_STATE_FULL(0x01) controller is in full-power state when screen is off. after a period of inactivity, controller goes into snooze mode.
		* @return 是否成功. ture: 成功
		*/
    boolean enableScreenOffSuspend();
	/**
		* 设置在屏幕关闭状态下, 控制器电源关闭.
			@see POWER_STATE_OFF: power level is OFF when screen is off.
		* @return 是否成功. ture: 成功
		*/
    boolean disableScreenOffSuspend();
	/**
		* 设置屏幕状态.
		* @param screen_state_mask: 屏幕状态掩码. NfcService.java 中根据
		* @return void
		*/
    public void doSetScreenState(int screen_state_mask);
	/**
		* 获取 NCI 版本
		* @return  NCI 版本
		*/
    public int getNciVersion();
	/**
		* 启用 Dta 模式
		* @return void
		*/
    public void enableDtaMode();
	/**
		* 停用 Dta 模式
		* @return void
		*/
    public void disableDtaMode();
	/**
		* 恢复出厂设置
		* @return void
		*/
    public void factoryReset();
	/**
		* 关闭 NFC
		* @return void
		*/
    public void shutdown();
	/**
		* 设置是否启用安全NFC. 
			安全 NFC 是 Android 10 中引入的一项功能，允许仅在设备屏幕处于解锁状态时启用脱离主机的 NFC 卡模拟。通过实现此功能，用户可以选择启用安全 NFC，以提高安全性.
			<a href="https://source.android.google.cn/devices/tech/connect/secure-nfc?hl=zh-cn">安全NFC</a>
			内部实现: 如果初始化过, 则会刷新路由. 如果 RF 启用则重启 RF discovery
		* @param enable: true - 启用
		* @return 
		*/
    public boolean setNfcSecure(boolean enable);
}
```

## DeviceHostListener

处理 NCI 消息的回调接口

```java
public interface DeviceHostListener {
  /**
	 * 在 remote endpoint 被发现时 NCI 回调该接口
	 * @param tag: 
	 * @return 
   */
  public void onRemoteEndpointDiscovered(TagEndpoint tag);

  /**
	 * HCE 激活通知回调.
	 * @param technology: tag的技术.
	 * @return void
   */
  public void onHostCardEmulationActivated(int technology);
  /**
	 * HCE 数据传输回调.
	 * @param technology: tag 的技术.
	 * @param data: HCE 数据.
	 * @return void
   */
  public void onHostCardEmulationData(int technology, byte[] data);
  /**
	 * HCE 失活通知回调
	 * @param 
	 * @return 
   */
  public void onHostCardEmulationDeactivated(int technology);

  /**
	 * Notifies P2P Device detected, to activate LLCP link
	 */
  public void onLlcpLinkActivated(NfcDepEndpoint device);
  /**
	 * Notifies P2P Device detected, to activate LLCP link
	 */
  public void onLlcpLinkDeactivated(NfcDepEndpoint device);
  public void onLlcpFirstPacketReceived(NfcDepEndpoint device);

  /**
	 * 远端场激活通知. 
	   NfcService 的处理是向有SE访问权限的Package或已安装的有NFC_TRANSACTION_EVENTS权限的Package广播消息
	 * @param 
	 * @return 
   */
  public void onRemoteFieldActivated();
	/**
	 * 远端场失活通知
	 	 NfcService 的处理是向有SE访问权限的Package或已安装的有NFC_TRANSACTION_EVENTS权限的Package广播消息
	 * @param 
	 * @return 
   */
  public void onRemoteFieldDeactivated();
	/**
	 * HCI 通知与AID相关的交易数据.
	 	 NfcService 的处理是找到所有允许访问的已安装的有NFC_TRANSACTION_EVENTS权限的Package, 向这些包发送数据
	 * @param aid: AID
	 * @param data: 数据
	 * @param sename: 安全元件(SE)的名称. 
	   android.se.omapi 包中的 ISecureElementService 通过 aid 和 sename, 可以判断指定包名的包是否有权限接收交易事件.
	 * @return 
   */
  public void onNfcTransactionEvent(byte[] aid, byte[] data, String seName);

  /**
	 * NCI 处理 NFCEE 发现请求通知时触发, 通知 NFC Service 层 NFCEE 更新.
	 	 NFC Service 的处理是调用 applyRouting(force=true). 
	 	 applyRouting 内部处理是如果处于 "屏幕解锁&&已经在和Tag交互" 状态, 则发送延迟消息MSG_RESUME_POLLING, 该消息处理还是会调用applyRouting. 个人理解是: 在解锁读Tag状态下, 不会开启/关闭(polling和listen). 否则, 根据条件调用 NCI 开启/关闭(polling和listen).
	 * @return void
   */
  public void onEeUpdated();
}
```

