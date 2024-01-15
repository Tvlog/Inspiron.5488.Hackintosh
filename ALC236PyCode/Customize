有别于网络上流传广泛的EFI设置，以下设置均为我个人认为没必要的设置

framebuffer-unifiedmem：设置核显能调用的显存大小（实际使用的是内存），DATA 格式，按需使用
00000060（1536M，默认）
00000080（2048M，如果花屏可尝试）
000000C0（3072M）
0000F0FF（4095M）

我驱动的，UHD630核显的显存为默认的1536MB，看到网上不少文章把显存改为2048MB，暂时不清楚这样做的意义和作用是啥？
回答：没任何意义，4k也没意义，部分机器花屏可以通过改成2048显存解决···只有部分部分！！不是全部，没必要跟风，电脑没花屏之类的用默认就好

第一：除非有必要（例如1536导致的屏幕花屏），否则作为原生态主义推荐者，表示不推荐修改；
第二：白苹果核显的显存默认也是1536MB，显存没单独去打补丁了


Intel BIOS 设置
注意:大多数选项可能不会出现在你的固件中，我们建议尽可能匹配，但如果这些选项在你的BIOS中不可用，不要太担心

#禁用
快速启动（Fast Boot）
安全引导（Secure Boot）
串口/COM端口（Serial/COM Port）
并口（Parallel Port）
VT-d (如果将DisableIoMapper设置为YES，则可以启用)
兼容性支持模块(CSM)(在大多数情况下必须关闭，当该选项启用时，像gIO这样的GPU错误/停顿很常见)
雷电 Thunderbolt(用于初始安装，因为如果安装不正确，Thunderbolt可能会导致问题)
Intel SGX
Intel Platform Trust
CFG Lock (MSR 0xE2写保护)(必须关闭，如果你找不到选项，那么在Kernel -> Quirks下启用AppleXcpmCfgLock。你的hack将无法在启用CFG-Lock的情况下启动)

#启用
VT-x
4G以上解码
超线程
执行禁止位
EHCI/XHCI Hand-off
操作系统类型:Windows 8.1/10 UEFI模式(一些主板可能需要”其他操作系统”代替)
DVMT预分配(iGPU内存):64MB或以上
SATA 模式: AHCI
