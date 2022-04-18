# 在 Colab 运行 Faster R-CNN 时的问题简记


## 0x00 相关链接

- 使用 Pytorch 框架下实现的 Faster R-CNN：[jwyang/faster-rcnn.pytorch](https://github.com/jwyang/faster-rcnn.pytorch/tree/pytorch-1.0)
- 主要参考文章：[使用 colab 訓練 faster-rcnn](https://www.twblogs.net/a/5cb44531bd9eee48d788c415)

## 0x01 操作步骤

### 克隆仓库

使用 `-b` 参数指定 [分支]^(branch) 为 `pytorch-1.0`，否则后续编译出错

```python
!git clone -b pytorch-1.0 https://github.com/jwyang/faster-rcnn.pytorch.git
```

### 下载预训练模型、数据集

```python
# 进入下载好代码的主目录
import os
os.chdir('faster-rcnn.pytorch')

# 下载预训练模型
!ls
!mkdir data
os.chdir('data')
!mkdir pretrained_model
os.chdir('pretrained_model')
# 下载 res101
!wget https://filebox.ece.vt.edu/~jw2yang/faster-rcnn/pretrained-base-models/resnet101_caffe.pth
# 下载 vgg16
!wget https://filebox.ece.vt.edu/~jw2yang/faster-rcnn/pretrained-base-models/vgg16_caffe.pth

# 下载数据集
os.chdir('../') #返回上一級目录即data/下
!wget http://host.robots.ox.ac.uk/pascal/VOC/voc2007/VOCtrainval_06-Nov-2007.tar
!wget http://host.robots.ox.ac.uk/pascal/VOC/voc2007/VOCtest_06-Nov-2007.tar
!wget http://host.robots.ox.ac.uk/pascal/VOC/voc2007/VOCdevkit_08-Jun-2007.tar
# 解压缩
!tar xvf VOCtrainval_06-Nov-2007.tar
!tar xvf VOCtest_06-Nov-2007.tar
!tar xvf VOCdevkit_08-Jun-2007.tar
# 解压后的文件夹名称需要修改否则网络训练过程中会找不到路径。报错见问题 2
!mv VOCdevkit VOCdevkit2007
```

### 编译

回到上一级路径，然后进入 `lib` 编译

```python
os.chdir('../lib')
!python setup.py build develop
```

### 训练

首先修改编译好文件 `faster-rcnn.pytorch/lib/roi_data_layer/minibatch.py`  。原因见问题 1。

```python
!sed -i 's/from scipy.misc import imread/from matplotlib.pyplot import imread/g' roi_data_layer/minibatch.py
```

给定超参数开始训练：

```python
!cd /content/faster-rcnn.pytorch && CUDA_VISIBLE_DEVICES=0 python trainval\_net.py \
                --dataset pascal_voc \
                --net res101 \
                --bs 4 \
                --nw 0 \
                --lr 0.004 \
                --lr_decay_step 8 \
                --epochs 10 \
                --cuda
```

## 0x02 已解决问题

### 问题 1：无法导入 `imread`

开始训练时文件 `faster-rcnn.pytorch/lib/roi_data_layer/minibatch.py` 无法从 `scipy.misc` 导入 `imread` 。

```python
Traceback (most recent call last):
  File "trainval_net.py", line 28, in <module>
    from roi_data_layer.roibatchLoader import roibatchLoader
  File "/content/faster-rcnn.pytorch/lib/roi_data_layer/roibatchLoader.py", line 14, in <module>
    from roi_data_layer.minibatch import get_minibatch, get_minibatch
  File "/content/faster-rcnn.pytorch/lib/roi_data_layer/minibatch.py", line 15, in <module>
    from scipy.misc import imread
ImportError: cannot import name 'imread'
```

参考 https://stackoverflow.com/a/49586881 改为从 `matplotlib.pyplot` 导入。

>  [`imread`](https://docs.scipy.org/doc/scipy-1.2.1/reference/generated/scipy.misc.imread.html#scipy.misc.imread) is deprecated in SciPy 1.0.0, and will be removed in 1.2.0. Use `imageio.imread` instead.
>
> https://docs.scipy.org/doc/scipy-1.2.1/reference/generated/scipy.misc.imread.html
>
> 所以也可以 `!pip install scipy==1.0.0` ，印象中好像 pip3 不行

### 问题 2：找不到数据集

开始训练时找不到数据集。

```python
Traceback (most recent call last):
  File "trainval_net.py", line 197, in <module>
    imdb, roidb, ratio_list, ratio_index = combined_roidb(args.imdb_name)
  File "/content/faster-rcnn.pytorch/lib/roi_data_layer/roidb.py", line 116, in combined_roidb
    roidbs = [get_roidb(s) for s in imdb_names.split('+')]
  File "/content/faster-rcnn.pytorch/lib/roi_data_layer/roidb.py", line 116, in <listcomp>
    roidbs = [get_roidb(s) for s in imdb_names.split('+')]
  File "/content/faster-rcnn.pytorch/lib/roi_data_layer/roidb.py", line 109, in get_roidb
    imdb = get_imdb(imdb_name)
  File "/content/faster-rcnn.pytorch/lib/datasets/factory.py", line 66, in get_imdb
    return __sets[name]()
  File "/content/faster-rcnn.pytorch/lib/datasets/factory.py", line 25, in <lambda>
    __sets[name] = (lambda split=split, year=year: pascal_voc(split, year))
  File "/content/faster-rcnn.pytorch/lib/datasets/pascal_voc.py", line 56, in __init__
    self._image_index = self._load_image_set_index()
  File "/content/faster-rcnn.pytorch/lib/datasets/pascal_voc.py", line 107, in _load_image_set_index
    'Path does not exist: {}'.format(image_set_file)
AssertionError: Path does not exist: /content/faster-rcnn.pytorch/data/VOCdevkit2007/VOC2007/ImageSets/Main/trainval.txt
```

因为下载完的数据集解压缩后默认文件夹是 `VOCdevkit`，而代码中写死了去 `VOCdevkit2007` 找。

## 0x03 补充：TensorBoard 在 Colab 里的用法简记

### 为什么这是个问题？

Colab 是远程操作，不像本机使用的话环回地址加端口号就访问得到，Colab 宿主机的 IP 不允许公网直接访问，需要反代才能正常访问 Colab 开放出来的 Tensorbaord 端口。如果不反代就只能用 `tensorboardX`。

### 操作步骤
1. 安装 TensorBoard for Pytorch：[TensorBoardX](https://github.com/lanpa/tensorboardX)

   ```python
   !pip install tensorboardX
   ```

   根据 TensorBoardX 项目主页的教程我们其实还需要安装 TensorBoard，但 [通过 pip 安装 TensorFlow 时，也会自动安装 TensorBoard](https://www.tensorflow.org/guide/summaries_and_tensorboard?hl=zh-cn)。 Colab 作为 Google 亲儿子，自然已经安装妥当。

2. 在程序中引入（修改 faster-rcnn.pytorch 项目中的已有代码）

   ```python
   from tensorboardX import SummaryWriter
   ……
   logger = SummaryWriter("logs")# 指定传递给TensorBoardX用来绘图的数据的存放位置：
                                   # 项目根目录下的logs文件夹，其在训练开始后自动创建
   ……
   info = {
               'loss': loss_temp,
               'loss_rpn_cls': loss_rpn_cls,
               'loss_rpn_box': loss_rpn_box,
               'loss_rcnn_cls': loss_rcnn_cls,
               'loss_rcnn_box': loss_rcnn_box
             }
   logger.add_scalars("logs_s_{}/losses".format(args.session), info, (epoch - 1) * iters_per_epoch + step)
   ```

3. 使用 [魔法函数]^(MagicCommands) 启用 TensorBaordX

   魔法函数是 IPython 内核的特性，意在简明地解决数据处理过程中的常见问题。

   > %load_ext
   >
   > Load an IPython extension by its module name.
   >
   > https://ipython.readthedocs.io/en/stable/interactive/magics.html

   ```python
   %load_ext tensorboard
   # %reload_ext tensorboard
   %tensorboard --logdir "/content/faster-rcnn.pytorch/logs"
   ```

   这个 code cell 执行完毕后不会终止而是默默等待数据，接着再执行训练 code cell 这边就会出曲线了。

### 更多 Tensorboard 教程：

- https://www.tensorflow.org/guide/summaries_and_tensorboard?hl=zh-cn
- https://colab.research.google.com/drive/1NbEqqB42VSzYt-mmb4ESc8yxL05U2TIV#scrollTo=ZnDqxE2msZbd
- https://zhuanlan.zhihu.com/p/39115355
