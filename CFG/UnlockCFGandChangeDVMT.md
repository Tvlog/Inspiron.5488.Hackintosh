感谢🙏[daggeryu](https://github.com/daggeryu)

**DELL5488**解锁**CFG** & 修改DVMT（本仓库标题其他机型一样适用）

1：将shell文件夹丢到efi里面，开机进bios，添加启动项efi/shell/bootx64.efi

![add](https://github.com/daggeryu/DELL-inspiron-5488/blob/master/images/addshell.png)

2：先输入setup_var 0x5C3 //查询CFG状态

3：如果看到offset 0x5C3 is 0x01 //0x01表示开启,0x00表示关闭

4：输入setup_var 0x5C3 0x00 //将0x5C3地址值改为0x00

5：即可解锁CFG 

6：最后再输入一遍setup_var 0x5C3 ，检查是否改成了0x00 

成功如图： 
![png](https://github.com/daggeryu/DELL-inspiron-5488/blob/master/images/sussess.png)

7：输入 setup_var 0x8E5 0x2 会车；
8：输入 setup_var 0x8E6 0x3 回车。
此方法通过shell修改DVMT大小可有效解决因为dvmt不足而引起安装卡显卡的问题。

输入exit退出


**第二种方法**

1.格式化为 FAT32，然后在 U 盘分区里新建一个 EFI 文件夹，EFI 文件夹里新建一个 BOOT 文件夹，把下载的 efi 文件（名为BOOTX64.efi），放入 BOOT 文件夹里面。

2.关机，使用 U 盘启动

DELL 5488需要在开机出现logo的时候按下F12，然后将引导模式改为传统模式+安全启动关闭

确认后，电脑会自动重启，仍然在dell logo 时按下F12，选择U盘，以UEFI方式启动。这样就到了修改bios的环境了
