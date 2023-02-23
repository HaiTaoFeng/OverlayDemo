# OverlayDemo
RRO实例应用

修改方法：
 1.修改AndroidManifest.xml中的android:targetPackage属性，修改为需要被rro的应用的包名；
 
 2.添加需要被rro的资源名称到values的资源文件中，如strings.xml的<string name="common_setting">helloWorld</string>；
 
 3.当前RRO应用和被RRO的应用签名需要一致；
 
 4.将apk push到vendor/overlay(没有则创建)里面安装。
 

命令：
启用资源包
adb shell cmd overlay enable com.xxx.xxx(当前应用包名)
 禁用资源包
adb shell cmd overlay disable com.xxx.xxx(当前应用包名)

说明：OverlayManagerService 使用 idmap2 将目标软件包中的资源 ID 映射到叠加层软件包中的资源 ID。
生成的 ID 映射存储在 /data/resource-cache/ 中。如果叠加层无法正常运行，请在 /data/resource-cache/
中查找叠加层的相应 idmap 文件，然后运行以下命令。
查看资源是否替换：
adb shell idmap2 dump --idmap-path 文件路径+名称

结果：0x01040151 -> 0x01050001 string/common_setting

原理：
* <pre>
 *         Android framework
 *            |         ^
 *      . . . | . . . . | . . . .
 *     .      |         |       .
 *     .    AIDL,   broadcasts  .
 *     .   intents      |       .
 *     .      |         |       . . . . . . . . . . . .
 *     .      v         |       .                     .
 *     .  OverlayManagerService . OverlayManagerTests .
 *     .                  \     .     /               .
 *     . (1)               \    .    /            (3) .
 *      . . . . . . . . . . \ . . . / . . . . . . . . .
 *     .                     \     /              .
 *     . (2)                  \   /               .
 *     .           OverlayManagerServiceImpl      .
 *     .                  |            |          .
 *     .                  |            |          .
 *     . OverlayManagerSettings     IdmapManager  .
 *     .                                          .
 *     . . . .  . . . . . . . . . . . . . . . . . .
 * </pre>
