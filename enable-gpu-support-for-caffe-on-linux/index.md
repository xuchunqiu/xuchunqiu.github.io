# 为 Linux 上的 Caffe 启用 GPU 支持


## 0x00 下载 CUDA 和 cuDNN

下载 `CUDA Toolkit 8.0` 和 `cuDNN v7.1.4, for CUDA 8.0`

- CUDA 下载：https://developer.nvidia.com/cuda-80-ga2-download-archive

- cuDNN 下载：https://developer.nvidia.com/rdp/cudnn-archive

## 0x01 安装 `CUDA`

```bash
user@machine:~# sudo sh cuda_8.0.61_375.26_linux.run
……
Do you accept the previously read EULA?
accept/decline/quit: accept
Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 375.26?
(y)es/(n)o/(q)uit: n
Install the CUDA 8.0 Toolkit?
(y)es/(n)o/(q)uit: y
Enter Toolkit Location
 [ default is /usr/local/cuda-8.0 ]: 
# CUDA_HOME=/usr/local/cuda
Do you want to install a symbolic link at /usr/local/cuda?
(y)es/(n)o/(q)uit: y
Install the CUDA 8.0 Samples?
(y)es/(n)o/(q)uit: y
Enter CUDA Samples Location
 [ default is /home/user ]: 
Installing the CUDA Toolkit in /usr/local/cuda-8.0 ...
Installing the CUDA Samples in /home/user ...
Copying samples to /home/user/NVIDIA_CUDA-8.0_Samples now...
Finished copying samples.
Installing the CUDA Toolkit in /usr/local/cuda-8.0 ...
Missing recommended library: libGLU.so
Missing recommended library: libXmu.so

Installing the CUDA Samples in /home/user ...
Copying samples to /home/user/NVIDIA_CUDA-8.0_Samples now...
Finished copying samples.

===========
= Summary =
===========

Driver:   Not Selected
Toolkit:  Installed in /usr/local/cuda-8.0
Samples:  Installed in /home/user, but missing recommended libraries

Please make sure that
 -   PATH includes /usr/local/cuda-8.0/bin
 -   LD_LIBRARY_PATH includes /usr/local/cuda-8.0/lib64, or, add /usr/local/cuda-8.0/lib64 to /etc/ld.so.conf and run ldconfig as root

To uninstall the CUDA Toolkit, run the uninstall script in /usr/local/cuda-8.0/bin

Please see CUDA_Installation_Guide_Linux.pdf in /usr/local/cuda-8.0/doc/pdf for detailed information on setting up CUDA.

***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 361.00 is required for CUDA 8.0 functionality to work.
To install the driver using this installer, run the following command, replacing <CudaInstaller> with the name of this run file:
    sudo <CudaInstaller>.run -silent -driver

Logfile is /tmp/cuda_install_6304.log
Signal caught, cleaning up
```

### 故障排除：Missing libGLU.so、libXmu.so

```bash
# 对于 Missing recommended library: libGLU.so
# 和 Missing recommended library: libXmu.so
# 安装依赖库：
user@machine:~# sudo apt-get install freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libgl1-mesa-glx libglu1-mesa libglu1-mesa-dev

# 对于 ***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 361.00 is required for CUDA 8.0 functionality to work. 不用管
```

### 将 `CUDA` 安装目录写入环境变量

```bash
user@machine:~# sudo vim ~/.bashrc

# 将以下内容写入到~/.bashrc尾部：
export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

### 运行 `CUDA` 测试用例

```bash
user@machine:~# cd /usr/local/cuda-8.0/samples/1_Utilities/deviceQuery
user@machine:~# sudo make
"/usr/local/cuda-8.0"/bin/nvcc -ccbin g++ -I../../common/inc  -m64    -gencode arch=compute_20,code=sm_20 -gencode arch=compute_30,code=sm_30 -gencode arch=compute_35,code=sm_35 -gencode arch=compute_37,code=sm_37 -gencode arch=compute_50,code=sm_50 -gencode arch=compute_52,code=sm_52 -gencode arch=compute_60,code=sm_60 -gencode arch=compute_60,code=compute_60 -o deviceQuery.o -c deviceQuery.cpp
nvcc warning : The 'compute_20', 'sm_20', and 'sm_21' architectures are deprecated, and may be removed in a future release (Use -Wno-deprecated-gpu-targets to suppress warning).
"/usr/local/cuda-8.0"/bin/nvcc -ccbin g++   -m64      -gencode arch=compute_20,code=sm_20 -gencode arch=compute_30,code=sm_30 -gencode arch=compute_35,code=sm_35 -gencode arch=compute_37,code=sm_37 -gencode arch=compute_50,code=sm_50 -gencode arch=compute_52,code=sm_52 -gencode arch=compute_60,code=sm_60 -gencode arch=compute_60,code=compute_60 -o deviceQuery deviceQuery.o 
nvcc warning : The 'compute_20', 'sm_20', and 'sm_21' architectures are deprecated, and may be removed in a future release (Use -Wno-deprecated-gpu-targets to suppress warning).
mkdir -p ../../bin/x86_64/linux/release
cp deviceQuery ../../bin/x86_64/linux/release

# 如果显示一些关于GPU的信息，则说明安装成功
user@machine:~# sudo ./deviceQuery
./deviceQuery Starting...

CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "GeForce GTX 950M"
CUDA Driver Version / Runtime Version          9.0 / 8.0
CUDA Capability Major/Minor version number:    5.0
Total amount of global memory:                 2003 MBytes (2100232192 bytes)
( 5) Multiprocessors, (128) CUDA Cores/MP:     640 CUDA Cores
GPU Max Clock rate:                            1124 MHz (1.12 GHz)
Memory Clock rate:                             900 Mhz
Memory Bus Width:                              128-bit
L2 Cache Size:                                 2097152 bytes
Maximum Texture Dimension Size (x,y,z)         1D=(65536), 2D=(65536, 65536), 3D=(4096, 4096, 4096)
Maximum Layered 1D Texture Size, (num) layers  1D=(16384), 2048 layers
Maximum Layered 2D Texture Size, (num) layers  2D=(16384, 16384), 2048 layers
Total amount of constant memory:               65536 bytes
Total amount of shared memory per block:       49152 bytes
Total number of registers available per block: 65536
Warp size:                                     32
Maximum number of threads per multiprocessor:  2048
Maximum number of threads per block:           1024
Max dimension size of a thread block (x,y,z): (1024, 1024, 64)
Max dimension size of a grid size    (x,y,z): (2147483647, 65535, 65535)
Maximum memory pitch:                          2147483647 bytes
Texture alignment:                             512 bytes
Concurrent copy and kernel execution:          Yes with 1 copy engine(s)
Run time limit on kernels:                     Yes
Integrated GPU sharing Host Memory:            No
Support host page-locked memory mapping:       Yes
Alignment requirement for Surfaces:            Yes
Device has ECC support:                        Disabled
Device supports Unified Addressing (UVA):      Yes
Device PCI Domain ID / Bus ID / location ID:   0 / 1 / 0
Compute Mode:
< Default (multiple host threads can use ::cudaSetDevice() with device simultaneously) >

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 9.0, CUDA Runtime Version = 8.0, NumDevs = 1, Device0 = GeForce GTX 950M
Result = PASS
```

## 0x02 安装 `cuDNN`

安装教程： https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html

```bash
user@machine:~# sudo dpkg -i ~/Downloads/libcudnn7_7.1.4.18-1+cuda8.0_amd64.deb
user@machine:~# sudo dpkg -i ~/Downloads/libcudnn7-dev_7.1.4.18-1+cuda8.0_amd64.deb
user@machine:~# sudo dpkg -i ~/Downloads/libcudnn7-doc_7.1.4.18-1+cuda8.0_amd64.deb
```

### 运行 `cuDNN` 测试用例

```bash
# Copy the cuDNN sample to a writable path.
user@machine:~# cp -r /usr/src/cudnn_samples_v7/ $HOME

# Go to the writable path.
user@machine:~# cd $HOME/cudnn_samples_v7/mnistCUDNN

# Compile the mnistCUDNN sample.
user@machine:~# make clean && make
# Run the mnistCUDNN sample. 'Test passed!' means cuDNN works.
user@machine:~# ./mnistCUDNN
……
Test passed!
```

#### 故障排除：没有 `libcudart.so.8.0`

```bash
./mnistCUDNN: error while loading shared libraries: libcudart.so.8.0: cannot open shared object file: No such file or directory

# 解决办法：https://blog.csdn.net/u011636567/article/details/77162217
# 首先检查 /usr/local/cuda-8.0/lib64 目录，看看 libcudart.so.8.0 是否存在，如果不存在，则先正确安装 cuda; 如果确定 cuda 已经存在，这个问题的出现与 Linux 的 ldconfig 命令有关
以下两种方法同理： 
# 1.
user@machine:~# sudo ldconfig /usr/local/cuda-8.0/lib64 
# 2.
user@machine:~# export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-8.0/lib64
# 如果仍然不行，再尝试执行: 
user@machine:~# export PATH=$PATH:/usr/local/cuda-8.0/bin 
user@machine:~# export LIBRARY_PATH=$LIBRARY_PATH:/usr/local/cuda-8.0/lib64 
user@machine:~# source /etc/profile 
```

## 0x03 在 `Caffe` 中启用 GPU

### 查看 `CUDA` 和 `cuDNN` 版本：

```bash
# CUDA
user@machine:~# cat /usr/local/cuda/version.txt

# if cuDNN was installed from a tar file
user@machine:~# cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
# if cuDNN was installed from a debian file
user@machine:~# cat /usr/include/cudnn.h | grep CUDNN_MAJOR -A 2
```

### 切换 `Caffe` 到 `cuDNN` 加速模式并测试运行手写体识别

首先修改 `Makefile.config`：

```bash
user@machine:~# sudo vim ~/caffe/Makefile.config

# 注释掉仅 CPU 模式开关
# CPU_ONLY := 1
# 打开 cuDNN 加速开关
USE_CUDNN := 1
```

然后重新编译整个工程（根据输出可以观察到编译的对象是 `~/caffe/src/caffe` 下的 `*.cu` 和 `*.cpp`）：

```bash
user@machine:~# make clean && make -j8
```

在运行时启用 GPU 有两种方式:

1. 运行 MNIST 例程前，修改训练超参数文件：

```bash
user@machine:~# sudo vim examples/mnist/lenet_solver.prototxt
# 改 CPU 为 GPU
solver_mode: GPU

#   运行 MNIST 识别程序
user@machine:~# ./examples/mnist/train_lenet.sh
```

2. 运行时使用参数指定 GPU 执行

```bash
user@machine:~# ./examples/mnist/train_lenet.sh -gpu 0
```


## 0x04 补充：确认 `make` 编译时调用的 GPU 核数

> `总核数` = `物理 CPU 个数` X `每颗物理 CPU 的核数` 
> 
> `总逻辑 CPU 数` = `物理 CPU 个数` X `每颗物理 CPU 的核数` X `超线程数`

```bash
# 查看物理 CPU 个数
user@machine:~# cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

# 查看每个物理 CPU 中 core 的个数（即核数）
user@machine:~# cat /proc/cpuinfo| grep "cpu cores"| uniq

# 查看逻辑 CPU 的个数
user@machine:~# cat /proc/cpuinfo| grep "processor"| wc -l
16
# make 的时候就可以 make -j16
```

## 0x05 补充：监测 GPU 运行情况

- 第三方工具：[gmonitor](ttps://github.com/mountassir/gmonitor/releases)
    ```bash
    user@machine:~# tar xvzf gmonitor-v1.3.tar.gz
    user@machine:~# sudo cp ~/Downloads/gmonitor /usr/local/bin/
    user@machine:~# gmonitor
    ```
- 官方工具：nvidia-smi（安装完显卡驱动后自带）
    ```bash
    # 每10s刷新一次
    user@machine:~# watch -n 10 nvidia-smi
    ```
