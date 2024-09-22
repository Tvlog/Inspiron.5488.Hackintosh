有别于网络上流传广泛的EFI设置，以下设置均为我个人认为没必要的设置

framebuffer-unifiedmem：设置核显能调用的显存大小（实际使用的是内存），DATA 格式，按需使用
|||
|--|--
|00000060|（1536M，默认）
|00000080|（2048M，如果花屏可尝试）
|000000C0|（3072M）
|0000F0FF|（4095M）

我驱动的，UHD630核显的显存为默认的1536MB，看到网上不少文章把显存改为2048MB，暂时不清楚这样做的意义和作用是啥？
回答：没任何意义，4k也没意义，部分机器花屏可以通过改成2048显存解决···只有部分部分！！不是全部，没必要跟风，电脑没花屏之类的用默认就好

第一：除非有必要（例如1536导致的屏幕花屏），否则作为原生态主义推荐者，表示不推荐修改；
第二：白苹果核显的显存默认也是1536MB，显存没单独去打补丁了


## [Inter BIOS 设置](https://sumingyd.github.io/OpenCore-Install-Guide/config-laptop.plist/coffee-lake.html#intel-bios-%E8%AE%BE%E7%BD%AE)
![启用](https://github.com/Tvlog/Inspiron.5488.Hackintosh/assets/141799395/52a07f8f-29c9-48bd-b59c-31e27d60c2c9)
![禁用](https://github.com/Tvlog/Inspiron.5488.Hackintosh/assets/141799395/3c9db266-a0e8-464d-825e-6a43e5ce5bbf)


IntelBluetoothFirmware.kext：固件驱动，上传固件驱动并让Intel蓝牙硬件进入可工作状态

BluetoolFixup.kext：Lilu插件，acdt团队编写的在用户层修补蓝牙守护程序bluetoothd使得其可以以USB传输协议驱动大部分的蓝牙硬件

IntelBTPatcher.kext：Lilu插件，内核层修补蓝牙堆栈、USB堆栈，使其兼容Intel蓝牙 一般不需要
