# Install Cuda*8.0 + cuDNN5.1 + OpenCV.3.2.0 tutorial

****

### Author: Samuel Wang
### e-mail: samuel325365@gmail.com

****

### Ubuntu16.04 

1.  先將映像檔燒錄至光碟或 USB，並使用光碟或 USB 開機後，即可安裝。

2.  設定網路連線

### Nvidia 顯示卡驅動 & CUDA 8.0 ＆ cuDNN 5.1
1.  下載自己顯示卡的驅動程式 ＆ CUDA 8.0 ＆ cuDNN 5.1

    [Ｎvidia dreivers: NVIDIA-Linux-x86_64-381.22.run](http://www.nvidia.com.tw/Download/index.aspx?lang=tw)

    [CUDA 8.0: cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb](https://developer.nvidia.com/cuda-downloads)

    [cuDNN 5.1: cudnn-8.0-linux-x64-v5.1.tgz](https://developer.nvidia.com/cudnn)

2.  檢查環境
    ```Shell 
        uname -m && cat /etc/*release
    ```
    重點是“x86_64”這一項，保證是x86架構，64bit系統
  
2.  按下 `Alt` `Ctrl` `F1` 進入文字介面並關閉 `X Server`
    ```Shell
        sudo /etc/init.d/lightdm stop
    ```
    Nouveau是一個 Open Source的顯示卡驅動程式，但是它可能會影響NVIDIA驅動的安裝，所以必須要關閉。
    ```Shell
        sudo nano /etc/modprobe.d/blacklist-nouveau.conf
    ```
    寫入：
    ```Shell
        blacklist nouveau
        options nouveau modeset=0
    ```
    ```Shell
        sudo apt-get update
        cd ~/Download
    ```
    如果你先前有安裝任何顯示卡驅動請移除
    ```Shell
        sudo apt-get remove --purge nvidia-*
    ```
    保存並退出 指令`Ctrl` `X`
    ```Shell
        sudo update-initramfs -u
    ```
    安裝顯卡驅動 (然後一直選yes, 注意在最後有個X configuration的東西要選no)

    ```Shell
        sudo chmod a+x NVIDIA-Linux-x86_64-381.22.run
        sudo bash ./NVIDIA-Linux-x86_64-381.22.run
        sudo reboot
    ```
    開啟 `X Server`
    ```Shell
        sudo /etc/init.d/lightdm start
    ```
    檢查是否裝好
    ```Shell
        nvidia-smi
        lspci -vnn | grep -i VGA -A 12
    ```
    裏面最後一行的 Kernel driver in use: nouveau 及為現在使用的顯卡驅動(裝完後會變成顯示：nvidia)

3.  安裝cuda8.0

    ```Shell
        cd ~/Download
        sudo dpkg -i cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb
        sudo apt-get update
        sudo apt-get install cuda
    ```
    安裝好了編譯一下環境路徑
    ```Shell
        sudo nano ~/.bashrc
    ```
    在最後面加上
    ```Shell
        export CUDA_HOME=/usr/local/cuda-8.0
        export LD_LIBRARY_PATH=${CUDA_HOME}/lib64
        
        PATH=${CUDA_HOME}/bin:${PATH}
        export PATH
    ```
    離開檔案`Ctrl`+`X`
    ```Shell
        source ~/.bashrc
    ```
    編譯 CUDA Samples
    ```Shell
        cd /usr/local/cuda/bin
        sudo bash cuda-install-samples-8.0.sh ~
        cd ~/NVIDIA_CUDA-8.0_Samples
        make -k
    ```
    執行看看
    ```Shell
        ./1_Utilities/deviceQuery/deviceQuery
    ```
    ![image](https://github.com/samuel325365/aLittleLinuxThing/blob/master/install_linux_nvidiaDrivers_cuda_cudnn_opencv/cuda_samples.png)


### 其他相關命令

確定nvidia driver會把nouveau blacklist掉

```grep 'nouveau' /etc/modprobe.d/* | grep nvidia```

```Shell
/etc/modprobe.d/nvidia-331_hybrid.conf:blacklist nouveau

/etc/modprobe.d/nvidia-331_hybrid.conf:blacklist lbm-nouveau

/etc/modprobe.d/nvidia-331_hybrid.conf:alias nouveau off

/etc/modprobe.d/nvidia-331_hybrid.conf:alias lbm-nouveau off

/etc/modprobe.d/nvidia-graphics-drivers.conf:blacklist nouveau

/etc/modprobe.d/nvidia-graphics-drivers.conf:blacklist lbm-nouveau

/etc/modprobe.d/nvidia-graphics-drivers.conf:alias nouveau off

/etc/modprobe.d/nvidia-graphics-drivers.conf:alias lbm-nouveau off

```

****

find the real name of the nvidia module

```modprobe -R nvidia```

```Shell
nvidia_331
```

****

check that nvidia kernel module is loaded or not

```lsmod | grep nvidia```

```Shell
nvidia              10699336  49 

drm                   302817  2 nvidia

```
****

details about the nvidia_331 module

```modinfo nvidia_331```

```Shell
filename:       /lib/modules/3.13.0-24-generic/updates/dkms/nvidia_331.ko

alias:          char-major-195-*

version:        331.67

supported:      external

license:        NVIDIA
```

### 安裝 Opencv + Opencv_contrib

```Shell
	bash opencv_contrib_install.sh
```
	如果以上沒有錯誤那就表示完成了~

### 參考

[ubuntu14.04 安裝nvidia顯卡driver](https://standbymesss.blogspot.tw/2016/09/ubuntu1404-nvidiadriver.html)

