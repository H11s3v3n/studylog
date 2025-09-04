# ubuntu24.04使用open-vm-tools无法在主机和虚拟机之间拖拽文件夹

用vmware装ubuntu24.04虚拟机，装好vmtools然后发现能在主机和虚拟机之间复制粘贴，但是不能在主机和虚拟机直接拖拽文件，这个问题之前也遇到过，一直没找到解决办法，今天终于找到解决办法了！

---

搜索发现是Ubuntu（22.04，20.04等）默认启用了新版的窗口系统Wayland而非原来的X11，VMware Tools似乎未支持这个特性。

接下来禁用Wayland

`sudo gedit /etc/gdm3/custom.conf`

删掉WaylandEnable=false这一行最开始的#号
重启虚拟机系统即可

---

贴一下vmtools卸载安装命令

```
sudo apt-get update #更新系统软件包
sudo apt-get autoremove open-vm-tools #移除旧版本的VMware Tools
sudo apt-get install open-vm-tools-desktop #安装新的VMware Tools
sudo reboot #关机
```

