# PSC HC2S（TS40 X2）软件说明


# BIOS

开机待屏幕点亮后按 `DEL` 进入

# 分区



# 软件安装路径

| name          | version  | path                          |
| ------------- | -------- | ----------------------------- |
| nvidia-driver | 430.4    | /usr/src/nvidia-430.40/nvidia |
| cuda          | 10.1     | /usr/local/cuda               |
| nvcc          | 10.0.130 | /usr/local/cuda/bin/          |
| cuDNN         | 7.6.5    | /usr/local/cuda/include       |
| nccl          | 2.5.6    | /usr/local/lib                |
| docker        | 18.09.3  | /usr/bin/docker               |
| nvidia-docker | 2.0.3    | /usr/bin/nvidia-docker        |
| python2       | 2.7.12   | /usr/bin/python               |
| python3       | 3.5.2    | /usr/bin/python3              |




# AI Max 深度学习平台

> AI Max是一款基于Kubernetes+Docker+GPU架构，定位于机器学习的上层应用，免去客户编写代码的麻烦，快速开始深度学习任务。通过容器化技术，封装了人工智能所需的系统环境和底层操作。
>
> http://www.amaxchina.com/Product/Introduction/AIMAX

## 平台架构

![来自 AMAX 官网的架构图。内容描述：由下至上共有基础设施层、资源调度层、应用服务层、业务领域层。其中基础设施层是CPU、TPU、GPU、FPGA、高速以太网、Infiniband、NFS、FlusterFS；资源调度层以 Kubernetes 为基础管理多个容器；应用服务层列出的深度学习框架包括 PyTorch、Caffe、TensorFlow、MXNet，它们之上是 CNN、VGG、ResNet、YOLO、RNN，再之上是模型开发、训练、验证、测试、部署；业务领域层包括金融、教育、医疗、制造、自然语言处理、图像识别、语音识别](http://www.amaxchina.com/op/cms/uploads/images/%3d%3d(1).png)

对于用户来说，唯一需要了解并使用的就是 Docker。

### Docker

Docker 有三个基本概念：

- 镜像 image

  操作系统分为内核和用户空间。对于 Linux 而言，内核启动后，会挂载 root 文件系统为其提供用户空间支持。而 Docker image 就相当于是一个 root 文件系统。通常会包含特定的程序、库、环境配置等。

- 容器 container

  container 由 image 建立，但不会反向影响 image。container 的实质是拥有独立命名空间的进程，这意味这它与宿主机隔离并拥有自己的 root 文件系统、网络配置、进程空间、用户 ID 空间等。
  
  container 可以被 建立 / 启动 / 重启 / 停止 / 删除；container 可以被保存为新的 image；container 可以直接挂载宿主机目录。

- 仓库 repository

  用于分发 image

#### Docker 解决的痛点

在 AI Max 中进行训练的主要原因是可以实现平台和宿主机、平台内不同用户、同用户不同项目（在该平台中被称为「任务」或「应用」）间环境的 **隔离** 和 **快速重建**。

![nvidia-docker 架构图。内容描述：由下至上依次为：NVIDIA GPUs(@Server)、CUDA Driver(@Host OS)、Docker Engine(@Host OS)、CONTAINER(@Container OS User Space,CUDA Toolkit、Applications)](https://cloud.githubusercontent.com/assets/3028125/12213714/5b208976-b632-11e5-8406-38d379ec46aa.png)

#### 获取 Docker 镜像

除了 AI Max 平台预置的 Docker 镜像，还可在以下位置自行下载镜像：

- 下载镜像 - AIMAX 机器学习镜像库
- 手动上传

#### 利用 Docker 镜像快速恢复工作状态

只有交互式开发模式下可以实现「将容器保存为镜像」的功能，这意味着所有的环境变量、软件包等配置都被保存在新的镜像中，实现了环境的快速重建。

## 如何训练

共有两种训练方式，其入口分别在：

- 模型训练 - 任务训练

- 模型训练 - 交互式开发

它们的环境都依托于 Docker 镜像，不同之处在于前者相当于一个简化的 PBS 作业提交界面，后者更类似本机训练。

### 任务训练

关键设置项：

- 程序代码入口文件

- 执行器 [python3 / python / shell]

- 机器学习库 [tensorflow / pytorch]

- 工作空间

- 超参数调整【可添加任意数目的键值对】

- 资源分配

- 选择镜像

### 交互式开发

关键设置项：

- 执行器 [Jupyer / Desktop / JupyterLab / Terminal]

- 工作空间

- 资源分配

- 选择镜像


## 如何可视化


