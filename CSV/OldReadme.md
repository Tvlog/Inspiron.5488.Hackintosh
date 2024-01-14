# DELL-5488 Opencore EFI Hackintosh

## 说明  
DELL-inspiron-5488 的黑苹果 EFI

当前支持 Monterey 12(A), Ventura 13(A), Sonoma 14(A)

🎬:Sonoma 14部分功能异常，14.2仍存在，待观察解决中...


## 电脑配置
| 规格     | 详细信息                                     |
| -------- | ---------------------------------------- |
| 电脑型号 | DELL-inspiron-5488             |
| 处理器   | 英特尔 酷睿 i5-8265u处理器             |
| 内存     | 金士顿 8GBx2  DDR4 2666MHz（U板只支持到2400） |
| 硬盘     | 西部数据 NVMe固态硬盘 sn520 1TB         |
| 硬盘     | 西部数据 SATA固态硬盘 蓝盘 2TB                  |
| 集成显卡 | Intel GMA UHD 620                          |
| 独立显卡 | NVIDIA GeForce MX250   无法驱动，通过SSDT屏蔽   |
| 声卡     | Realtek ALC236/ALC3204                   |
| 无线网卡 | AX200 160MHZ                   |
| 有线网卡 | Realtek PCIe Ethernet Controller Driver                  |
| 指纹    | Goodix Fingerprint Sensor Driver 无法驱动，通过定制USB屏蔽  |
| 摄像头  | UVC Camera 功能正常                        |
| 读卡器  |Realtek Memory Card Reader Driver      通过USB定制功能正常   |

🎬:指纹没必要屏蔽，耗电极低


## BIOS设置
||||
--|-------------------------------------------|-----------
1 |General \ Boot Sequence-Boot List Option |  [UEFI]
2 |System Configuration \ SATA Operation       |  [AHCI]
3 |Secure Boot \ Secure Boot Enable            | [Disabled]
4 |Virtualization Support \ Virtualization    |  [Enable]
5 |Virtualization Support \ VT for Direct I/O |  [Enable] [Disabled]
6 |Security Device Support                    | [Enabled] （Win11 - TPM 2.0）
7 |SupportAssist System Resolution \ Auto OSRecovery Threshold | [Disabled]
8 |Intel SGE \ Intel SGX Enable            | [Enable] [Disabled]

🎬:选项5⃣️8⃣️可根据个人情况开关，5⃣️建议保持打开，config.plist已做设置，8⃣️若不使用Windows指纹功能建议关闭


## 已知问题
1⃣️ ~~耳麦无法使用。需定制AppleALC解决，过程较为复杂，暂时搁浅~~ 已解决✅

2⃣️ 英特尔网卡蓝牙模块无法按预期睡眠。睡眠后蓝牙崩溃或连接丢失，Bluetoothd占用大量CPU资源，风扇狂转。浅度睡眠低概率复发，深度睡眠百分百复发，建议关闭蓝牙以解决

3⃣️ ~~极低概率仍会出现Sleep Wake Failure in EFI，后重启，二分法尝试解决中...~~ 已阶段性解决，代固化⌛️

4⃣️ 开机时间一长，iCloud上传下载不可用，任何操作均会进入等待队列🚫，注销后进入恢复正常 暂无解决头绪，用注销缓释，可能更换系统版本可以解决

5⃣️ Sonoma14.0,14.1,14.2查找Mac，iMessage，FaceTime认证过程出现错误，可能网卡内建，Inter Wi-Fi正式版更新能解决 弃坑Sonoma感觉不太适合Hackintosh


## 常见问题及解决
1⃣️ Sleep Wake Failure in EFI：SMBIOS需自行更新，CPUFriendProvider数据需根据个人情况自行生成[CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend)
[i58265u](https://ark.intel.com/content/www/cn/zh/ark/products/149088/intel-core-i58265u-processor-6m-cache-up-to-3-90-ghz.html)

若仍未解决问题，则：请使用命令或者 Hackintool 修复休眠模式 hibernatemode 和 proximitywake.   
	如果唤醒弹窗 “电脑关机是因为发生了问题” , 请前往 “控制台” 删除 “诊断报告” 中所有日志. （主要是 “Sleep Wake Failure” 相关的）  
	另外BIOS 可开启 “PCIE设备唤醒” 和 “网络唤醒”, 将支持键鼠唤醒. （不要开启 USB Standby Power at S4/S5）  
	
    设置 hibernatemode 和 proximitywake：
- ###### `sudo pmset -a hibernatemode 0`
- ###### `sudo pmset -a proximitywake 0`

2⃣️ 双系统时间不一致：Windows导入注册表采用UTC记时法

    Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1

3⃣️ [背光问题解决方案](https://github.com/acidanthera/WhateverGreen/commit/e2d3d20f7eadd8cf89991514c475daab3c0bdf0f)

+ **调整亮度丝滑器设置以提升用户体验**

为核显添加 `enable-backlight-smoother` 属性或者直接使用 `-igfxbls` 启动参数以使核显亮度调节变得更丝滑。

核显驱动通过修改亮度相关的寄存器的值来调整笔记本内屏的亮度。亮度丝滑器通过拦截这些写入操作并循循渐进地修改寄存器的值来实现亮度调节更丝滑的效果。
打个比方的话，核显驱动的工作方式犹如走楼梯让屏幕一下子变亮或变暗，而亮度丝滑器好比坐扶梯来让屏幕慢慢地变亮或变暗。
亮度丝滑器首先读取当前亮度档位对应的寄存器值 `SRC` 并计算到目标值 `DST` 的距离 `D`。 而后每 `T` 毫秒向目标值走一步，并在 `N` 步之内走完。
默认情况下，`N` 为 35 且 `T` 为 7，但可通过设备属性 `backlight-smoother-steps` 以及 `backlight-smoother-interval` 来修改它们的值。
然而我们建议 `T` 的值不要高于 10 毫秒，并且达到目标值所需要的时间 `N * T` 不要高于 350 毫秒以避免调节亮度时产生阶梯式卡顿现象。
此外，用户可通过 `backlight-smoother-threshold` 属性来指定一个最小的距离 `DM`，以让驱动检测到 `D` 小于 `DM` 时跳过丝滑器直接向寄存器写入目标值。
默认情况下，`DM` 为 0。

如果不希望笔记本内屏在亮度最低时黑屏，用户可通过 `backlight-smoother-lowerbound` 属性来自定义最低亮度档位对应的寄存器值。
同理，`backlight-smoother-upperbound` 属性控制最高亮度档位对应的寄存器值。请参考下面的例子来找到适合你笔记本的值。
若用户未注入这两个属性的话，BLS 使用默认的区间 [0, 2^32-1]。

<details>
<summary>样例: 为一台 Haswell 笔记本定制亮度丝滑器</summary>

如下的内核日志是从一台 Haswell 笔记本上提取的，显卡型号为 Intel HD Graphics 4600。  
日志反映了用户通过亮度快捷键从最低档位调到最高档位时寄存器值的变化。  
因为每个亮度档位之间对应的寄存器值的距离较短, 我们采用 `N = 25` 以及 `T = 8` 来让内屏在 200 毫秒左右达到下一个档位。  

|          设备属性名称          | 类型  |    值    |             备注            |
|:----------------------------:|:----:|:--------:|:--------------------------:|
|   enable-backlight-smoother  | Data | 01000000 |         启用亮度丝滑器       |
|   backlight-smoother-steps   | Data | 19000000 |  25 (0x19 使用小字节序编码)  |
|  backlight-smoother-interval | Data | 08000000 |  08 (0x08 使用小字节序编码)  |
| backlight-smoother-threshold | Data | 00000000 |  00 (0x00 使用小字节序编码)  |


```
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000000; Target = 0x00000036; Distance = 0054; Steps = 25; Stride = 3.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000036; Target = 0x00000036; Distance = 0000; Steps = 25; Stride = 0.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000036; Target = 0x00000054; Distance = 0030; Steps = 25; Stride = 2.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000054; Target = 0x0000007d; Distance = 0041; Steps = 25; Stride = 2.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x0000007d; Target = 0x000000b2; Distance = 0053; Steps = 25; Stride = 3.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000000b2; Target = 0x000000e7; Distance = 0053; Steps = 25; Stride = 3.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000000e7; Target = 0x000000f5; Distance = 0014; Steps = 25; Stride = 1.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000000f5; Target = 0x00000137; Distance = 0066; Steps = 25; Stride = 3.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000137; Target = 0x00000149; Distance = 0018; Steps = 25; Stride = 1.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000149; Target = 0x000001b1; Distance = 0104; Steps = 25; Stride = 5.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000001b1; Target = 0x0000022b; Distance = 0122; Steps = 25; Stride = 5.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x0000022b; Target = 0x00000271; Distance = 0070; Steps = 25; Stride = 3.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000271; Target = 0x000002b8; Distance = 0071; Steps = 25; Stride = 3.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000002b8; Target = 0x00000359; Distance = 0161; Steps = 25; Stride = 7.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000359; Target = 0x000003a4; Distance = 0075; Steps = 25; Stride = 3.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000003a4; Target = 0x00000401; Distance = 0093; Steps = 25; Stride = 4.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000401; Target = 0x00000413; Distance = 0018; Steps = 25; Stride = 1.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000413; Target = 0x0000046b; Distance = 0088; Steps = 25; Stride = 4.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x0000046b; Target = 0x000004ec; Distance = 0129; Steps = 25; Stride = 6.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000004ec; Target = 0x00000588; Distance = 0156; Steps = 25; Stride = 7.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000588; Target = 0x000005f3; Distance = 0107; Steps = 25; Stride = 5.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000005f3; Target = 0x000006b1; Distance = 0190; Steps = 25; Stride = 8.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000006b1; Target = 0x00000734; Distance = 0131; Steps = 25; Stride = 6.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000734; Target = 0x00000815; Distance = 0225; Steps = 25; Stride = 9.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000815; Target = 0x000008af; Distance = 0154; Steps = 25; Stride = 7.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000008af; Target = 0x000009f7; Distance = 0328; Steps = 25; Stride = 14.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000009f7; Target = 0x00000ad9; Distance = 0226; Steps = 25; Stride = 10.
```

从上面的内核日志可得知，内屏亮度最低时，对应的亮度寄存器的值为 `0x00`。
当用户通过快捷键调高亮度时，寄存器值变为 `0x36`，因此你可以指定寄存器最低值为 `0x18` 或 [0x00, 0x36] 区间内的任意一个值来阻止显示器在最低档位时直接黑屏。
你可能需要安装 DEBUG 版本的 WhateverGreen 并提取内核日志来找到一个适合你笔记本的值。

</details>

<details>
<summary>样例: 为一台 Coffee Lake 笔记本定制亮度丝滑器</summary>

如下的内核日志是从一台 Coffee Lake 笔记本上提取的，显卡型号为 Intel UHD Graphics 630。  
日志反映了用户通过亮度快捷键从最低档位调到最高档位时寄存器值的变化。  
因为每个亮度档位之间对应的寄存器值的距离较短, 我们采用 `N = 35` 以及 `T = 7` 来让内屏在 250 毫秒左右达到下一个档位。  

|          设备属性名称          | 类型  |    值    |             备注            |
|:----------------------------:|:----:|:--------:|:--------------------------:|
|   enable-backlight-smoother  | Data | 01000000 |         启用亮度丝滑器        |
|   backlight-smoother-steps   | Data | 23000000 |   35 (0x23 使用小字节序编码)  |
|  backlight-smoother-interval | Data | 07000000 |   07 (0x07 使用小字节序编码)  |
| backlight-smoother-threshold | Data | 2C010000 | 300 (0x012C 使用小字节序编码) |


```
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000000; Target = 0x000004ae; Distance = 1198; Steps = 35; Stride = 35.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000004ae; Target = 0x00000613; Distance = 0357; Steps = 35; Stride = 11.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000613; Target = 0x000007f3; Distance = 0480; Steps = 35; Stride = 14.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000007f3; Target = 0x00000a4b; Distance = 0600; Steps = 35; Stride = 18.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000a4b; Target = 0x00000e0c; Distance = 0961; Steps = 35; Stride = 28.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00000e0c; Target = 0x000012bb; Distance = 1199; Steps = 35; Stride = 35.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000012bb; Target = 0x000019c6; Distance = 1803; Steps = 35; Stride = 52.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000019c6; Target = 0x0000239b; Distance = 2517; Steps = 35; Stride = 72.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x0000239b; Target = 0x00003043; Distance = 3240; Steps = 35; Stride = 93.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00003043; Target = 0x00004216; Distance = 4563; Steps = 35; Stride = 131.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00004216; Target = 0x000050d5; Distance = 3775; Steps = 35; Stride = 108.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x000050d5; Target = 0x00005aea; Distance = 2581; Steps = 35; Stride = 74.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00005aea; Target = 0x00007d21; Distance = 8759; Steps = 35; Stride = 251.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00007d21; Target = 0x0000acf3; Distance = 12242; Steps = 35; Stride = 350.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x0000acf3; Target = 0x0000effc; Distance = 17161; Steps = 35; Stride = 491.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x0000effc; Target = 0x0001328e; Distance = 17042; Steps = 35; Stride = 487.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x0001328e; Target = 0x00014ead; Distance = 7199; Steps = 35; Stride = 206.
igfx: @ (DBG) BLS: [COMM] Processing the request: Current = 0x00014ead; Target = 0x0001d3cc; Distance = 34079; Steps = 35; Stride = 974.
```

</details>

+ **修复在 Kaby Lake/Coffee Lake 平台上运行 macOS 13.4 或以上版本的笔记本开机持续3分钟暗屏问题**

如果你之前使用“亮度寄存器修复”（也就是 `-igfxblr` 这个启动参数）来解决笔记本开机持续3分钟左右暗屏的问题，在升级到 macOS 13.4 或之后的版本后你会发现该补丁失效了。这是因为苹果简化了显卡驱动中读写寄存器相关的函数，导致编译器优化了函数调用的汇编代码，进而导致“亮度寄存器修复”以及“亮度丝滑器”注册的钩子失效。从 v1.6.5 开始，*WEG* 提供了新的补丁来撤销编译器对亮度调节相关函数的优化以及为 Coffee Lake 平台的笔记本重写了调节亮度的函数，从而解决开机持续3分钟暗屏以及“亮度丝滑器”失效的问题。从 v1.6.6 开始，*WEG* 支持 Kaby Lake 平台。

请注意这个新补丁仅适用于使用 macOS 13.4 以及以上的 Kaby Lake 或者 Coffee Lake 核显驱动的笔记本用户。你可以为核显添加 `enable-backlight-registers-alternative-fix` 属性或者直接使用 `-igfxblt` 启动参数来启用这个新的补丁。与此同时，你可以删除原“亮度寄存器修复”的 `enable-backlight-registers-fix` 设备属性或者 `-igfxblr` 启动参数。如果你想在 macOS 13.4 或以上系统中使用“亮度丝滑器”，你需要添加 `-igfxblt` 以及 `-igfxbls` 这两个启动参数。

请注意 Ice Lake 平台的笔记本用户不受此问题影响。

    https://github.com/acidanthera/WhateverGreen/blob/c5a5f507f54a6ff1d17e048fb9456302ef2a3702/Manual/FAQ.IntelHD.cn.md

4⃣️ 适应键盘布局

在 Windows PC 专用键盘上，请用 Alt 键代替 Option 键，用 Ctrl 键或 Windows 标志键代替 Command 键。

Mac 菜单和键盘通常会使用符号来表示某些按键，其中包括以下修饰键：

| Mac     | Windows                                     |
| -------- | ---------------------------------------- |
| Command（或 Cmd）⌘ |Windows 标志键|
| Shift ⇧   |一致|
| Option ⌥   |Alt|
|Control ⌃|Ctrl|
|Caps Lock ⇪|一致|
|Fn&nbsp;<img src="https://github.com/Tvlog/Inspiron.5488.Hackintosh/assets/141799395/237ae87e-bd12-4d3d-abd3-455bb242e497" alt="" width="18">|一致|

![键盘图示](https://github.com/Tvlog/Inspiron.5488.Hackintosh/assets/141799395/fc784bd0-ed48-420c-801e-45dc878be423)


# 效果展示
![变频功能正常](https://github.com/Tvlog/Inspiron.5488.Hackintosh/assets/141799395/a7edf221-861b-4c6c-8d49-506df940bb93)


# AppleALC定制过程
![Ubuntu环境下提取codec](https://github.com/Tvlog/Inspiron.5488.Hackintosh/assets/141799395/3ac97c79-7e66-4879-bea5-9f9eb0f1d518)
![修改对应节点信息](https://github.com/Tvlog/Inspiron.5488.Hackintosh/assets/141799395/82b8f6f1-de3d-4f0c-8994-a315a2e828e2)
![修改完成的四份文件编译AppleALC](https://github.com/Tvlog/Inspiron.5488.Hackintosh/assets/141799395/33e8b2f1-a677-46a2-88e8-5542a7e33709)
![成品AppleALC](https://github.com/Tvlog/Inspiron.5488.Hackintosh/assets/141799395/55b579e2-87fc-4dce-89d2-32682babf194)
