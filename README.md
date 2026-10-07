# -小爱触屏音箱x08e刷机-
如题
观前提示：本教程没有图片内容，且改编自：和elmagnifico.tech/2024/04/06/Redmi-xiaoai-sound8-X08C/

几天前买了个小爱触屏音箱，想着在没有手机的时候消遣.于是我去网上找刷机教程，给这个音箱刷了机。

哦对了，如果你还没买，那我强烈建议你去买别的，这玩意废的要命。

本教程用到的文件全部在：（这里放123云盘链接）

首先，打开UsbDk_1.0.22_x64.msi和MediaTek_SP_Driver_v5.2307.zip，安装驱

动。

                 弄固件

看教程用了固件获取工具，我跟着下载下来才发现不对：这个固件是x08c的，同版本号不同型号的固件却是个增量包

后来又不知道去哪里看到个开发版的版本号，跟着下载下来却是最新版。

我当时不知道开发版和稳定版没区别，看名字以为可以安装第三方软件，刷完才发现不对，没办法，只能去小破站上面找改过的刷机包。

找到老丁的视频，但是固件在百度网盘，于是又花了一个小时去下载固件（这个固件改了buildprop，使其可以直接使用USB调试）

我两个固件都另外弄了个用30700版本的alpha修补过的boot.img（文件夹里面是rboot.img）

               以下内容为dump固件（老丁发的固件不需要dump，用工具获取的就要）

下载payload_dumper（）

解压zip，打开文件夹，在地址栏处输入CMD并回车（或者cd）

安装依赖：pip3 install -r requirements.txt

dump:python payload_dumper.py （固件文件名）

output里面就是dump后的固件

               刷机

打开MTKclient，等待一小会，直到gui出现。

将音箱关机，把音箱底下的长的那条垫子扒开，USB口就在那里（是micro-USB！）

两头连接音箱和电脑，再长按电源和音量加（如果你的手不协调先按住音量加），再插电源线

此时MTKclient会显示连接到设备，gui窗口右上角手机那会有加载动画，然后就会出现分区了

（新版得关USB提速，关掉速度又太慢，所以请用链接里面的mtk.exe）

点Flash工具，点解锁bootloader

读分区建议把所有分区读了，要不要勾dump gpt我不知道，因为我没有备份

擦除分区选择 system_a/b , userdata ,  , vendor_a/b , boot_a/b 分区后点击擦除分区

写入把固件里面的所有东西都刷进去（system_other刷system b里面去）

拔电源线长按开机

然后你就可以随便玩了（开了USB调试，现在你可以用调试工具爱干嘛干嘛了），不过得先用adb装宝宝巴士的apk和第三方桌面的apk，然后在音箱自带桌面打开宝宝巴士再选你的桌面才能去打开第三方软件

关闭系统更新只需要卸载com.xiaomi.mico.romupdate就行

              静音（失败）

这音箱真的神的要命，甚至不能静音，在设置里面或者爱玩机工具箱也不行，SELinux设置项里面显示的音量是最低的，但是还是有声音，而且还挺大的

问DeepSeek，让我试试Volume Control Pro，没有用；Audio Switch

转输出路径，转到哪里去？？“通过修改系统文件 vendor/etc/audio_profile_configuration.xml，可以注释掉或删除所有与“Speaker”相关的设备端口定义，这样系统在启动时就不会加载扬声器设备，从根本上禁用外放https://www.hovatek.com/forum/thread-49291-post-250820.html#pid250820。操作前务必备份原文件，错误的修改可能导致设备完全无声或无法开机。”会砖？算了吧、、、

FineVolume？没找到；adb shell settings put system volume_steps_music 50？没效果；Audio Misc Settings还得加刷另一个模块，并且没有用

系统更新后没有设置和更新WebView

需要root和lsposed（新版lsposed不能授权，用vector）

新版似乎没有原生设置，不过WebView的包名变了，但是签名不一样。DeepSeek说APKMirror上面的设置不能用，叫我从固件里面提取apk，于是我用mik解包了system a，我会和旧固件一起放，还有下文的未压缩成zip的模块

好吧我忘记我前面试了什么了，反正DeepSeek最后给的方案是：“因为你的设备开启了 dm-verity，绝对不能直接替换 /system 里的文件。你必须按原厂位置来构建模块”

好吧反正最后模块长这样：

MTKSystemFix/
├── module.prop
├── customize.sh
└── system/
    ├── app/
    │   └── WebViewGoogle/
    │       └── WebViewGoogle.apk      ← 新 WebView，文件名可保持原样
    ├── priv-app/
    │   └── MtkSettings/
    │       └── MtkSettings.apk
    └── etc/
        └── permissions/
            ├── privapp-permissions-platform.xml
            └── privapp-permissions-mediatek.xml

要装核心破解，启用“禁用apk签名验证”，不是软件包管理器，别弄错了！

我用核心破解2.2选项全开会在重启后设置不停崩溃，算是开不了机，核心破解n开除危险外所有选项会掉Zygisk导致lsposed用不了

模块和核心破解启用（作用域选framework）后重启，然后直接手动安装模块里面的WebView（别装其它的！）

不出意外的话这个时候你打开设置-开发者模式-WebView实现就可以看到WebView版本号已经变成138了

                  结语

好吧这玩意很垃圾，建议买别的
不是这GitHub怎么用啊
不是怎么只能传25兆以下的文件啊
好吧我以后再来弄吧
