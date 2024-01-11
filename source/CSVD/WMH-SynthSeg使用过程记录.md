# Freesurfer WMH-SynthSeg使用过程记录

## Step 0：准备
本地环境：**Windows10** + **WSL2** + **Ubuntu 22.04**
时间：2024-01-07

## Step 1：安装Freesurfer7的开发版本
根据此时Freesurfer的官方说明（[WMH-SynthSeg](https://surfer.nmr.mgh.harvard.edu/fswiki/WMH-SynthSeg)），WMH-SynthSeg只能通过开发版本使用。下面是开发版本的安装过程：

**选择合适的Freesurfer版本**：在该页面中找到"Development Version"（[FreeSurfer Download and Install](https://surfer.nmr.mgh.harvard.edu/fswiki/DownloadAndInstall)）或直接进入[Index of /pub/dist/freesurfer/dev](https://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/dev/)选择合适的开发版本。
![开发版本界面](WMH-SynthSeq_1.png)
根据我的环境，我选择的是最后一个（freesurfer_ubuntu22-7-dev_amd64.deb）。相应地，修改Step 2中的下载路径。