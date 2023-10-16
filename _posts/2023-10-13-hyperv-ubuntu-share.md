---
title: Hyper-V虚拟机Ubuntu中访问windows中的文件
img_path: /assets/img/posts/20231016/
categories: [奇技淫巧]
tags: [Hyper-V, Ubuntu]
---

最方便便捷的方法还是使用 windows 文件共享方式

### `Hyper-V`中的`Ubuntu`之间如何访问共享文件以及注意事项

1. Windows 下设置共享文件夹或者某个盘都可以
   需要共享的`文件夹=>右键=>属性=>共享` 点击共享按钮选择用户(建议不要使用 Everyone，直接使用当前 windows 登录的用户)
   ![addremote](windows-share.webp)
2. 获取 windows 下的虚拟网卡的 ip 地址(我也尝试直接使用计算机名称但是不成功的)
   ![addremote](windows-ip.webp)
3. 到 Ubuntu 下执行命令

   ```bash
    sudo mount -t cifs 共享文件夹的路径 需要挂载的文件夹 -o username=用户名,password=密码
   ```

   共享文件夹的路径：在 windows 下设置成功后共享窗口中有个网络路径复制过来将电脑的名称修改为虚拟网卡 ip 地址，我这里是`//172.26.144.1/app`

   需要挂载的文件夹：也就是你在 Ubuntu 下的访问路径(随意设置)，我这里是`/mnt/app`

   成功后就在 Ubuntu 里访问挂载的路径就能看到 windows 下共享的文件夹了

4. 若需要重启后还有效(长期使用),那么需要在`/etc/fstab`文件后添加一行代码
   ```bash
   //172.26.144.1/app    /mnt/app    cifs    auto,username=用户名,password=密码,dir_mode=0777,file_mode=0777    0 0
   ```
   将你自己的信息替换进去，至于如何修改 ubuntu 文件你可以大显身手什么 vim 命令啦，不过我喜欢使用`nano`
   ```bash
   sudo nano /etc/fstab
   ```

_ubuntu 下执行命令失败提示什么没安装就执行安装命令就行了_

_如果某天你突然发现失效了那查下是不是你虚拟网卡 ip 地址变了_
