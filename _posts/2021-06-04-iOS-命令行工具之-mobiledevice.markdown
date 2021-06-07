
### [mobiledevice](https://github.com/imkira/mobiledevice)
以下为翻译内容

#### 要求
1. MAC OS X 10.6 以上版本
2. 通过USB连接你的iPhone设备
3. 需要安装mobiledevice
4. 安装app需要提前安装开发者证书

#### 安装
```shell
brew update
brew install mobiledevice
```

#### 支持的能力

* 安装卸载app 
* 连接电脑的iphone设备列表 `mobiledevice list_devices`
* 获取设备属性 `mobiledevice list_device_props -u <udid>`

```shell
    ActivationPublicKey
    ActivationState
    ActivationStateAcknowledged
    BasebandSerialNumber
    BasebandStatus
    BasebandVersion
    BluetoothAddress
    BuildVersion
    CPUArchitecture
    DeviceCertificate
    DeviceClass
    DeviceColor
    DeviceName
    DevicePublicKey
    DieID
    ...
```
 
* 获取属性值 `mobiledevice get_device_prop property_name`

#### 罗列app
为了获取设备上安装的所有app列表：

```shell
    mobiledevice list_apps
```
输出类似内容如下：

```shell
    com.apple.VoiceMemos
    com.apple.mobiletimer
    com.apple.AdSheetPhone
    com.apple.weather
    com.apple.iphoneos.iPodOut
    com.apple.mobilesafari
    com.apple.Preferences
    ...
    com.mycompany.myapp1
    com.mycompany.myapp2
    ...
```
为了精准的获取某个设备，你也可以追加 -u <udid> 标记，如下

```shell
    mobiledevice list_apps -u 7c211433f02071597741e6ff5a8ea34789abbf43
```

#### 罗列app属性
显示某个app的属性，你可以使用下面的命令

```
    mobiledevice list_app_props com.mycompany.myapp
```
输出类似内容如下：

```shell
    SBIconClass
    CFBundleInfoDictionaryVersion
    Entitlements
    DTPlatformVersion
    CFBundleName
    DTSDKName
    ApplicationType
    UIViewControllerBasedStatusBarAppearance
    CFBundleIcons
    UIStatusBarStyle
    Container
    LSRequiresIPhoneOS
    CFBundleDisplayName
    PrivateURLSchemes
    UIBackgroundModes
    DTSDKBuild
    ...
```

为了精准的获取某个设备，你也可以追加 -u <udid> 标记，如下

```shell
    mobiledevice list_app_props -u 7c211433f02071597741e6ff5a8ea34789abbf43 com.mycompany.myapp
```
备注：

* 假如没有使用`-u <udid>`标记，则系统默认使用第一个被发现的设备

#### 获取应用属性值
显示某个app的属性值，你可以使用下面的命令

```
    mobiledevice get_app_prop com.mycompany.myapp property_name
```

举例，如果你想获取苹果天气app的安装路径，你可以使用`path`属性：

```
    mobiledevice get_app_prop com.apple.weather Path
```
为了精准的获取某个设备，你也可以追加 -u <udid> 标记，如下

```
    mobiledevice get_app_prop -u 7c211433f02071597741e6ff5a8ea34789abbf43 com.mycompany.myapp Path
```

备注：

* 假如没有使用`-u <udid>`标记，则系统默认使用第一个被发现的设备
* 执行成功后，值将放在回车符后边


#### 安装app
安装一个app到设备，你可以这样做：

```
    mobiledevice install_app path/to/my_application.app
```
为了精准的获取某个设备，你也可以追加 -u <udid> 标记，如下

```
    mobiledevice install_app -u 7c211433f02071597741e6ff5a8ea34789abbf43 path/to/my_application.app
```
备注：

* 假如没有使用`-u <udid>`标记，则系统默认使用第一个被发现的设备

#### 卸载app
卸载一个app，你需要提供` bundle identifier`

```
    mobiledevice uninstall_app com.mycompany.myapp
```
为了精准的获取某个设备，你也可以追加 -u <udid> 标记，如下

```
    mobiledevice uninstall_app -u 7c211433f02071597741e6ff5a8ea34789abbf43 com.mycompany.myapp
```
备注：

* 假如没有使用`-u <udid>`标记，则系统默认使用第一个被发现的设备

#### 把设备和电脑之间的TCP通道转向
假如你的app建立了一个监听某个端口的TCP server，通过USB访问这个端口是非常有用的（无需通过WIFI/3G）。设备允许你建立一个通道在电脑和设备之间通过USB接口，你通过访问电脑上的某个端口，手机会将这个连接转到具体的监听端口通过如下命令：

```
    mobiledevice tunnel 8080 80
```

如果先前的例子视图解释和说明Mac‘s TCP 端口 8080 和设备的TCP端口80，内容输出如下

```
    Tunneling from local port 8080 to device port 80...
```
如此你可以通过`telnet localhost 8080`和手机端开启的TCP 80端口服务进行通信

为了精准的获取某个设备，你也可以追加 -u <udid> 标记，如下

```sh
    mobiledevice tunnel -u 7c211433f02071597741e6ff5a8ea34789abbf43 8080 80
```
备注：

* 假如没有使用`-u <udid>`标记，则系统默认使用第一个被发现的设备
* 如果你的进程一直开着，则端口转发服务也会一直开启，直到你决定终止它（你可以使用 CTRL-C）这将终结所有被转发的连接，也不再接受新的连接。
* mobiledevice允许你保持多个TCP连接，通过一个实例。也可以保持不同的端口转发（通过建立多个mobiledevice实例）

#### 获取一个应用的标示符（identifier）
这是一个公共命令，跟 MobileDevice Framework没有关系。为了获取标示符（e.g. com.mycompany.myapp）你可以使用如下命令，必须是一个可用的.app文件不是.ipa!)

```
    mobiledevice get_bundle_id path/to/my_application.app
```
备注：

* path是指你的本地电脑，不是手机设备

#### 贡献
找到一个bug？ 或者想增加一个功能？
你可以fork这个工程，并且给我提一个合入申请

#### 授权
mobiledevice使用的是MIT 授权
www.opensource.org/licenses/MIT




#### 寻找元素
* Class Name
* Accessibility Id
* Link Text
* Predicate
* Class Chain
* XPath
* 

#### 检索速度



#### 参考
[WebDriver 协议](https://w3c.github.io/webdriver/)
[w3c](https://blog.csdn.net/niguang09/article/details/7428676)
[XPath](https://www.runoob.com/xpath/xpath-tutorial.html)
[classchain](https://github.com/facebookarchive/WebDriverAgent/wiki/Class-Chain-Queries-Construction-Rules)
[predicate query](https://github.com/facebookarchive/WebDriverAgent/wiki/Predicate-Queries-Construction-Rules)





