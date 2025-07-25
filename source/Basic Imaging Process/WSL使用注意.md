# WSL使用注意事项/踩坑

## WSL移动VHDX位置

```powershell
wsl --shutdown

# 替换为你想要的打包路径
wsl --export Ubuntu-22.04 D:\Ubuntu_WSL\Ubuntu.tar

# 注销原有的Ubuntu
wsl --unregister Ubuntu-22.04

# 导入为新的Ubuntu，注意替换想要存放的vhdx的新的路径
wsl --import Ubuntu-22.04 D:\Ubuntu_WSL D:\Ubuntu_WSL\Ubuntu.tar
```

这样移动过后，点开wsl会发现默认用户切换为了root。可以在wsl中设置后重启：

```bash
vim /etc/wsl.conf

# 添加以下内容，注意替换用户名
# [user]
# default=user_name 
# 然后windows中wsl --shutdown后重启wsl
```

另外注意，虽然移动了光盘映像的位置，但是产生的Temp文件还是在系统盘`C:\Users\<你的用户名>\AppData\Local\Temp`（可能和一些环境变量设置有关）。

## WSL修改内存大小

需要编辑`C:\Users\<你的用户名>\.wslconfig`（如果不存在则自己创建）。根据自己电脑实际设置，一些高级设置请参考[官网](https://learn.microsoft.com/zh-cn/windows/wsl/wsl-config)。

```
[wsl2]
memory=32GB
processors=16
swap=8GB
localhostForwarding=true
```

玄学：FSL有时候闪退，将使用的processors调小后变得稳定，不知道是什么原因。

## WSL VHDX文件过大

WSL光盘映像文件只会扩容，不会缩小（删除里面的文件不会减少占用的空间大小）。

参考https://blog.csdn.net/C_lonq/article/details/133930176

```powershell
wsl --shutdown
Diskpart
select vdisk file="path/to/your/wsl.vhdx"
compact vdisk
```

## 启动WSL提示分发错误

[Unable to launch wsl2](https://github.com/microsoft/WSL/issues/11474)里面有详细的解决方法（简单来说需要创建一个新的wsl系统，然后修复原来的VHDX文件）

这里也简单记录一下过程：
1. 备份之前的vhdx文件
   原始文件在默认`%USERPROFILE%\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu_79rhkp1fndgsc\LocalState\ext4.vhdx`。如果之前移动了位置，请找打对应的路径。将其复制一份到其他位置，以免恢复过程中失败...
   假设复制到了`/mnt/path/to/your/ext4.vhdx`。

2. 创建一个新的wsl系统
   这里对版本没有要求，注意不要和之前的重名了就行了。
   ```powershell
   wsl --install -d Ubuntu-22.04
   ```

3. 打开新创建的wsl系统，等待初始化完成。
   安装需要的工具
   ```bash
   sudo apt update
   sudo apt install qemu-utils
   ```

4. 修复原来的vhdx文件
   ```bash
   sudo modprobe nbd
   sudo qemu-nbd -c /dev/nbd0 -f vhdx /mnt/path/to/your/ext4.vhdx
   # 这一步中间会询问修复操作，输入a代表所有yes
   # 结束之后
   sudo qemu-nbd -d /dev/nbd0
   ```

5. 替换文件
   ```powershell
   wsl --shutdown
   ```
   将/mnt/path/to/your/ext4.vhdx复制到原来的位置，覆盖掉原来的文件。之后应该可以正常打开。

## WSL2适配CUDA

在[WMH-SynthSeg的使用](../CSVD/WMH-SynthSeg使用过程记录.md)中已经记录了这一问题。这里记录几个要点。

### 不要在WSL2中单独安装NVIDIA驱动！

只需要在Windows系统中安装nvidia驱动，而不用在wsl中安装。如果`nvidia-smi`命令有问题，那很可能就是在wsl2中安装了nvidia驱动覆盖了默认配置好的路径，需要删掉：

```bash
sudo apt-get purge -y '*nvidia-driver*' '*nvidia-kernel*' '*libnvidia*'
sudo apt autoremove -y
sudo apt clean
```

### 安装CUDA

下载前请注意所使用软件的CUDA与cudnn的适配，以及pytorch或tensorflow的版本要求！！！

打开[CUDA Toolkit](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64)的下载网站，一般下载最新的可能与其他软件不兼容，请下滑到Resources栏选择Archive of Previous CUDA Releases。按照系统选择（如下图），这里似乎选择Ubuntu和WSL-Ubuntu没有什么区别。

![cuda toolkit](./cuda_toolkit.png)

选择之后下面会出现命令，按照这个命令就可以安装了。

![cuda toolkit](./cuda_toolkit2.png)

然后在`.bashrc`中更新环境变量
```bash
# 注意替换自己的cuda版本，看下在/usr/local中的文件夹名称是什么
export CUDA_HOME=/usr/local/cuda-12.3
export PATH=$PATH:$CUDA_HOME/bin
export LD_LIBRARY_PATH=/usr/local/cuda-12.3/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

安装后，运行`nvcc -V`应该能正常输出。

### 安装cudnn

进入[cudnn](https://developer.nvidia.com/rdp/cudnn-archive)的网站，选择适合的cudnn版本（下载需要登录nvidia，好像可以找其它不需要登录的方法）。我选择的是Local Installer for Linux x86_64 (Tar)，下面的Local Installer for Ubuntu22.04 x86_64 (Deb)应该也可以。假设下载到了当前目录：

```bash
# 请替换cudnn-linux-x86_64-8.9.6.50_cuda12-archive.tar.xz为实际路径
tar -xvf cudnn-linux-x86_64-8.9.6.50_cuda12-archive.tar.xz

# 请确定解压后的目录是否为cuda，以及/usr/local下的cuda文件夹名称
sudo cp -P cuda/include/cudnn*.h /usr/local/cuda-12.3/include
sudo cp -P cuda/lib/libcudnn* /usr/local/cuda-12.3/lib64
sudo chmod a+r /usr/local/cuda-12.3/include/cudnn*.h
sudo chmod a+r /usr/local/cuda-12.3/lib64/libcudnn*
```

之后应该可以正常使用了。