# Freesurfer WMH-SynthSeg使用过程记录

## Step 0：准备
本地环境：**Windows10** + **WSL2** + **Ubuntu 22.04**  
时间：2024-01-07

## Step 1：选择合适的Freesurfer7的开发版本
根据此时Freesurfer的官方说明（[WMH-SynthSeg](https://surfer.nmr.mgh.harvard.edu/fswiki/WMH-SynthSeg)），WMH-SynthSeg只能通过开发版本使用。下面是开发版本的安装过程：

**选择合适的Freesurfer版本**：在该页面中找到"Development Version"（[FreeSurfer Download and Install](https://surfer.nmr.mgh.harvard.edu/fswiki/DownloadAndInstall)）或直接进入[Index of /pub/dist/freesurfer/dev](https://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/dev/)选择合适的开发版本。  
![进入开发版本界面](WMH-SynthSeg_1.png)  
根据我的环境，我选择的是最后一个（freesurfer_ubuntu22-7-dev_amd64.deb）。相应地，修改**Step 2**中的下载路径。  
![开发版本界面](WMH-SynthSeg_2.png)

## Step 2：安装Freesurfer的开发版本
过程参考Freesurfer官方安装指南（[Freesurfer on Ubuntu Linux in WSL](https://surfer.nmr.mgh.harvard.edu/fswiki/FS7_wsl_ubuntu)），因为版本不同，修改相应的地方即可。具体安装过程如下：  
```bash
# 进入HOME路径
root@LXG:~# cd

# 下载开发版本的.deb文件
root@LXG:~# wget https://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/dev/freesurfer_ubuntu22-7-dev_amd64.deb

# 检查下载的文件是否存在
root@LXG:~# ls freesurfer_ubuntu22-7-dev_amd64.deb

# 更新Ubuntu相关的包
root@LXG:~# sudo apt-get update -y

# 安装开发版本
# 注意：这一步提示已经安装，因此我卸载了之前安装的Freesurfer版本（7.4.1）
root@LXG:~# sudo apt-get -y install ./freesurfer_ubuntu22-7-dev_amd64.deb

# 添加环境变量
root@LXG:~# export FREESURFER_HOME=/usr/local/freesurfer/7-dev
root@LXG:~# echo "export FREESURFER_HOME=/usr/local/freesurfer/7-dev" >> $HOME/.bashrc

# 添加license和相关变量。事先将license.txt放在HOME路径中
root@LXG:~# cd
root@LXG:~# ls license.txt
root@LXG:~# echo "export FS_LICENSE=$HOME/license.txt" >> $HOME/.bashrc

# 其它环境变量
root@LXG:~# echo "export XDG_RUNTIME_DIR=$HOME/.xdg" >> $HOME/.bashrc
root@LXG:~# echo "export DISPLAY=:0" >> $HOME/.bashrc
root@LXG:~# echo "source $FREESURFER_HOME/SetUpFreeSurfer.sh" >> $HOME/.bashrc
```
编辑$HOME/.bashrc文件，将其中之前版本的变量信息删除（如图中的前一部分，为7.4.1版本的环境变量）
![删除其他版本的环境变量](WMH-SynthSeg_3.png)   
重新启动Ubuntu，看到如下提示说明安装成功：   
-------- freesurfer-linux-ubuntu22_x86_64-dev-20240105-87ffad0 --------  
Setting up environment for FreeSurfer/FS-FAST (and FSL)  
FREESURFER_HOME   /usr/local/freesurfer/7-dev  
FSFAST_HOME       /usr/local/freesurfer/7-dev/fsfast  
FSF_OUTPUT_FORMAT nii.gz  
SUBJECTS_DIR      /usr/local/freesurfer/7-dev/subjects  
MNI_DIR           /usr/local/freesurfer/7-dev/mni  
FSL_DIR           /usr/local/fsl

## Step 3：WMH-SynthSeg命令使用
TODO