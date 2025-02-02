## PVE下安装Ubuntu虚拟机直通Nvidia3060 12G Laptop显卡跑Ollama

### 0. 禁用 Secure Boot
重启计算机：进入 BIOS 设置界面。通常在开机时按下特定的热键（如 F2、Del、Esc 等），具体按键取决于您的主板型号。
进入 BIOS 设置：在 BIOS 设置中，找到“Security”或“Boot”选项卡。
禁用 Secure Boot：将 Secure Boot 设置为“Disabled”。
保存并退出：保存更改并退出 BIOS 设置，计算机将自动重启。

### 1. 确认显卡信息
```
lspci | grep -i nvidia
```
### 3. 安装编译环境（如果需要）
```
sudo apt update
sudo apt install gcc make g++
```
### 4. 添加 PPA 存储库
```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
```

### 5. 安装 NVIDIA 驱动程序

```
ubuntu-drivers devices

sudo ubuntu-drivers autoinstall
sudo reboot
```

### 6. 验证驱动程序安装

```
nvidia-smi
```
如果安装成功，您将看到显卡信息以及驱动程序版本。

监控GPU：
```
watch -n 1 nvidia-smi
```

### 7. 安装Cuda
```
sudo apt install nvidia-cuda-dev nvidia-cuda-toolkit
nvcc --version
```
应该是cuda v12

下载cuDNN（8.9适配cuda12）：https://developer.nvidia.com/rdp/cudnn-archive
https://developer.download.nvidia.com/compute/cudnn/secure/8.9.7/local_installers/12.x/cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz?__token__=exp=1738506580~hmac=2aef42d174b76a7187e1bb1b5d121cee7f852baacb5bbd893afb8326458c4a4d&t=eyJscyI6IndlYnNpdGUiLCJsc2QiOiJkZXZlbG9wZXIubnZpZGlhLmNvbS9yZHAvY3Vkbm4tYXJjaGl2ZSJ9

上传到Ubuntu：
```
tar -xvf cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz
sudo cp include/cudnn*.h /usr/lib/cuda/include/
sudo cp lib/libcudnn* /usr/lib/cuda/lib64/
sudo chmod a+r /usr/lib/cuda/include/cudnn*.h
sudo chmod a+r /usr/lib/cuda/lib64//libcudnn*
```

验证 cuDNN 是否安装成功：

```
cat /usr/lib/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

### 8. 安装Ollama

```
export http_proxy="http://192.168.31.101:8080"
export https_proxy="http://192.168.31.101:8080"

curl -fsSL https://ollama.com/install.sh | sh
```
