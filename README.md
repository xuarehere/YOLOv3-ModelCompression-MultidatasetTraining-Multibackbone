# YOLOv3-ModelCompression-MultidatasetTraining

本项目包含两部分内容：

1、提供多个主流目标检测数据集的预处理后文件及训练方法。

2、提供包括剪植，量化，知识蒸馏的主流模型压缩算法实现。

3、提供多backbone训练目前包括Darknet-YOLOv3，Tiny-YOLOv3，Mobilenetv3-YOLOv3。

其中：

源码使用Pytorch实现，以[ultralytics/yolov3](https://github.com/ultralytics/yolov3)为YOLOv3源码仓库。基于BN层的剪植方法由[coldlarry/YOLOv3-complete-pruning](https://github.com/coldlarry/YOLOv3-complete-pruning)提供，感谢学长在模型压缩领域的探索。

# 最近更新
 2020年1月4日 提供Visdrone数据集剪裁后的下载链接和训练方法。
 
 2020年1月19日 提供Dior，Bdd100k，visdrone训练完成，并完成转化的.weights文件。
 
 2020年3月1日 实现基于mobilenetv3 backbone的YOLOv3。
 
 2020年4月7日 实现基于mobilenetv3的两种backbone模型，YOLOv3-mobilenet和YOLOv3tiny-mobilene-small
 ，提供预训练模型，将正常剪植算法扩展到基于mobilenet的两个模型和YOLOv3tiny模型，删除tiny剪植。

 2020年4月27日 更新mobilenetv3的模型预训练，添加了层剪植方法，方法来自于[tanluren/yolov3-channel-and-layer-pruning/yolov3](https://github.com/tanluren/yolov3-channel-and-layer-pruning)，
 感谢大佬的分享。
 
 2020年5月22日 更新了[ultralytics/yolov3](https://github.com/ultralytics/yolov3)为YOLOv3源码仓库的最新优化，更新YOLOv4网络结构和权重文件。

# 环境部署
1.由于采用[ultralytics/yolov3](https://github.com/ultralytics/yolov3)的YOLO实现，环境搭建详见[ultralytics/yolov3](https://github.com/ultralytics/yolov3)。这里简要说明：

- `numpy`
- `torch >= 1.1.0`
- `opencv-python`
- `tqdm`

可直接`pip3 install -U -r requirements.txt`搭建环境，或根据该.txt文件使用conda搭建。

# 目前支持功能

|<center>功能</center>|<center></center>|
| --- |--- |
|<center>训练</center>|
|<center>正常训练</center>|<center>√</center>|
|<center>tiny训练</center>|<center>√</center>|
|<center>mobilenetv3训练</center>|<center>√</center>|
|<center>mobilenetv3-small训练</center>|<center>√</center>|
|<center>多数据集</center>|
|<center>Dior数据集训练</center>|<center>√</center>|
|<center>bdd100k数据集训练</center>|<center>√</center>|
|<center>visdrone数据集训练</center>|<center>√</center>|
|<center>剪植</center>|
|<center>稀疏化训练</center>|<center>√</center>  |
|<center>正常剪枝</center>|<center>√</center>|
|<center>规整剪枝</center>|<center>√</center>  |
|<center>极限剪枝(shortcut)</center>|<center>√</center> |
|<center>层剪植</center>|<center>√</center> |
|<center>量化</center>|
|<center>BNN量化</center>|<center>√</center>  |
|<center>BWN量化</center>|<center>√</center>  |
|<center>stage-wise 逐层量化</center>|<center>√</center>  |
|<center>知识蒸馏</center>|<center>√</center>  |

# 可用指令

`python3 train.py --data ... --cfg ... `为训练模型指令，使用coco预训练模型时需要-pt指令。

`python3 test.py --data ... --cfg ... ` 为mAP测试指令。

`python3 detect.py --data ... --cfg ... --source ...`为推理检测指令，source默认地址为data/samples,输出结果保存在output文件中，检测资源可以为图片，视频等。

# 一、多数据集训练
本项目提供针对YOLOv3仓库的预处理数据集，配置文件(.cfg)，数据集索引文件(.data)，数据集类别文件(.names)以及使用k-means算法重新聚类的anchor box尺寸(包含用于yolov3的9框和tiny-yolov3的6框)。

基于Darknet-53的YOLOv3-608网络mAP

|<center>数据集</center>|<center>mAP</center>|
| --- |--- |
|<center>Dior遥感数据集</center>|<center>0.56</center>|
|<center>bdd100k自动驾驶数据集</center>|<center>0.38</center>|
|<center>visdrone无人机航拍数据集</center>|<center>0.286</center>|

下载地址如下，下载并解压后将文件夹拷贝至data目录下即可使用。

- [COCO2017](https://pan.baidu.com/s/1UXKs7ilSQi_P6ijiLgKTUg)
  
  提取码：4f06

- [COCO权重文件](https://pan.baidu.com/s/1JZylwRQIgAd389oWUu0djg)

  提取码：k8ms
  
训练指令

```bash
python3 train.py --data data/coco2017.data --batch-size ... --weights weights/yolov3-608.weights -pt --cfg cfg/yolov3/yolov3.cfg --img-size ... --epochs ...
```


- [Dior遥感数据集](https://pan.baidu.com/s/1z0IQPBN16I-EctjwN9Idyg)
  
  提取码：vnuq

- [Dior权重文件](https://pan.baidu.com/s/11reX-oNtO-8fKmPIZdt2ow)

  提取码：lgj5
  
训练指令

```bash
python3 train.py --data data/dior.data --batch-size ... --weights weights/yolov3-608.weights -pt --cfg cfg/yolov3/yolov3-onDIOR.cfg --img-size ... --epochs ...
```


- [bdd100k无人驾驶数据集](https://pan.baidu.com/s/157Md2qeFgmcOv5UmnIGI_g)
  
  提取码：8duw
  
- [bdd100k权重文件](https://pan.baidu.com/s/1E8aDznODhmH4DwPpV_Gbpg)

  提取码：q076
  
训练指令

```bash
python3 train.py --data data/bdd100k.data --batch-size ... --weights weights/yolov3-608.weights -pt --cfg cfg/yolov3/yolov3-bdd100k.cfg --img-size ... --epochs ...
```

- [visdrone数据集](https://pan.baidu.com/s/1CPGmS3tLI7my4_m7qDhB4Q)
  
  提取码：dy4c
  
- [visdrone权重文件](https://pan.baidu.com/s/1Sf20FGAa-vLF8CCLpKSIug)

  提取码：ynuf
  
训练指令

```bash
python train.py --data data/visdrone.data --batch-size ... --weights weights/yolov3-608.weights -pt --cfg cfg/yolov3/yolov3-visdrone.cfg  --img-size ... --epochs ...
```

## 1、Dior数据集
DIRO数据集是地球观测社区中最大、最多样化和公开可用的目标检测数据集之一。其中船舶和车辆的实例数较高，在小型实例和大型实例之间实现了良好的平衡。图片采集自Google Earth。

[数据集详细介绍](https://cloud.tencent.com/developer/article/1509762)

### 检测效果
![检测效果](https://github.com/SpursLipu/YOLOv3-ModelCompression-MultidatasetTraining/blob/master/image_in_readme/2.jpg)
![检测效果](https://github.com/SpursLipu/YOLOv3-ModelCompression-MultidatasetTraining/blob/master/image_in_readme/3.jpg)

## 2、bdd100k数据集
bdd100是一个大规模、多样化的驾驶视频数据集，共包含十万个视频。每个视频大约40秒长，研究者为所有10万个关键帧中常出现在道路上的对象标记了边界框。数据集涵盖了不同的天气条件，包括晴天、阴天和雨天、以及白天和晚上的不同时间。

[官网](http://bair.berkeley.edu/blog/2018/05/30/bdd/)

[原数据集下载](http://bdd-data.berkeley.edu)

[论文](https://arxiv.org/abs/1805.04687)

### 检测效果
![检测效果](https://github.com/SpursLipu/YOLOv3-ModelCompression-MultidatasetTraining/blob/master/image_in_readme/1.jpg)

## 3、Visdrone数据集
VisDrone2019数据集由中国天津大学机器学习和数据挖掘实验室的AISKYEYE团队收集。基准数据集包含288个视频片段，由261,908个帧和10,209个帧组成静态图像，由各种安装在无人机上的摄像头捕获，涵盖了广泛的方面，包括位置（从中国相距数千公里的14个不同城市中拍摄），环境（城市和乡村），物体（行人，车辆，自行车等）和密度（稀疏和拥挤的场景）。该数据集是在各种情况下以及在各种天气和光照条件下使用各种无人机平台（即具有不同模型的无人机）收集的。这些框架使用超过260 万个边界框手动标注，这些边界框是人们经常感兴趣的目标，例如行人，汽车，自行车和三轮车。还提供了一些重要属性，包括场景可见性，对象类别和遮挡，以提高数据利用率.

[官网](http://www.aiskyeye.com/)

### 检测效果
![检测效果](https://github.com/SpursLipu/YOLOv3-ModelCompression-MultidatasetTraining/blob/master/image_in_readme/4.jpg)
![检测效果](https://github.com/SpursLipu/YOLOv3-ModelCompression-MultidatasetTraining/blob/master/image_in_readme/5.jpg)

# 二、多种网络结构
在mobilenetv3基础上设计了两种网络结构

|结构名称 |<center>backbone参数量</center>|<center>后处理参数量</center> |<center>总参数量</center> |<center>GFLOPs</center> |<center>mAP0.5</center> |<center>mAP0.5:0.95</center> |
| --- | --- | --- | --- | --- | --- | --- |
|YOLOv3                      |38.74M  |20.39M  |59.13M  |117.3   |0.667  |0.394  |
|YOLOv3tiny                  |6.00M   |2.45M   |8.45M   |9.9     |0.275  |0.115  |
|YOLOv3-mobilenetv3          |2.84M   |20.25M  |23.09M  |32.2    |0.459  |0.282  |
|YOLOv3tiny-mobilenetv3-small|0.92M   |2.00M   |2.92M   |2.9     |0.301  |0.162  |
|YOLOv4                      |-       |-       |61.35M  |107.1   |0.704  |0.483  |
## 训练指令
1、YOLOv3
```bash
python3 train.py --data data/coco2017.data --batch-size ... -pt --weights weights/yolov3-608.weights --cfg cfg/yolov3/yolov3.cfg --img_size ...
```

权重文件下载
- [COCO预训练权重文件](https://pan.baidu.com/s/1JZylwRQIgAd389oWUu0djg)

  提取码：k8ms

2、YOLOv3tiny
```bash
python3 train.py --data data/coco2017.data --batch-size ... -pt --weights weights/yolov3tiny.weights --cfg cfg/yolov3tiny/yolov3-tiny.cfg --img_size ...
```

- [COCO预训练权重文件](https://pan.baidu.com/s/1iWGxdjR3TWxEe37__msyRA)

  提取码：udfe
  
3、YOLOv3tiny-mobilenet-small
```bash
python3 train.py --data data/coco2017.data --batch-size ... -pt --weights weights/yolov3tiny-mobilenet-small.weights --cfg cfg/yolov3tiny-mobilenet-small/yolov3tiny-mobilenet-small-coco.cfg --img_size ...
```

- [COCO预训练权重文件](https://pan.baidu.com/s/1vWRcn5A95PoYhBtB2rWH8A)

  提取码：yixp

4、YOLOv3-mobilenet
```bash
python3 train.py --data data/coco2017.data --batch-size ... -pt --weights weights/yolov3-mobilenet.weights --cfg cfg/yolov3-mobilenet/yolov3-mobilenet-coco.cfg --img_size ...
```

- [COCO预训练权重文件](https://pan.baidu.com/s/1hR2SYvb5mjh_ysepCtFnmw)

  提取码：iayy

5、YOLOv4
```bash
python3 train.py --data data/coco2017.data --batch-size ... -pt --weights weights/yolov4.weights --cfg cfg/yolov4/yolov4.cfg --img_size ...
```

- [COCO预训练权重文件](https://pan.baidu.com/s/1jAGNNC19oQhAIgBfUrkzmQ)

  提取码：njdg
  
# 三、模型压缩

## 1、剪植

### 剪植特点
|<center>剪枝方案</center> |<center>优点</center>|<center>缺点</center> |
| --- | --- | --- |
|正常剪枝   |不对shortcut剪枝，拥有可观且稳定的压缩率，无需微调。  |压缩率达不到极致。  |
|极限剪枝   |极高的压缩率。  |需要微调。  |
|极限剪枝2  |采用shortcut融合的方法提升剪植精度。  |针对shortcut最优的方法。|
|规整剪枝   |专为硬件部署设计，剪枝后filter个数均为8的倍数，无需微调。 |为规整牺牲了部分压缩率。 |
|层剪枝     |以ResBlock为基本单位剪植，利于硬件部署。 |但是只能剪backbone，剪植率有限。 |
|层通道剪植 |先进行通道剪植再进行层剪植，剪植率非常高。 |可能会影响精度。 |

### 步骤

1.正常训练

```bash
python3 train.py --data ... -pt --batch-size ... --weights ... --cfg ...
```

2.稀疏化训练

`-sr`开启稀疏化，`--s`指定稀疏因子大小，`--prune`指定稀疏类型。

其中：

`--prune 0`为正常剪枝和规整剪枝的稀疏化

`--prune 1`为极限剪枝的稀疏化

`--prune 2`为层剪植稀疏化

指令范例：

```bash
python3 train.py --data ... -pt --batch-size 32 --accumulate 1 --weights ... --cfg ... -sr --s 0.001 --prune 0 
```

3.模型剪枝

- 正常剪枝
```bash
python3 normal_prune.py --cfg ... --data ... --weights ... --percent ...
```
- 规整剪枝
```bash
python3 regular_prune.py --cfg ... --data ... --weights ... --percent ...
```
- 极限剪枝
```bash
python3 shortcut_prune.py --cfg ... --data ... --weights ... --percent ...
```

- 极限剪枝2
```bash
python3 slim_prune.py --cfg ... --data ... --weights ... --percent ...
```

- 层剪植
```bash
python3 layer_prune.py --cfg ... --data ... --weights ... --shortcut ...
```

- 层剪植+通道剪植
```bash
python3 layer_channel_prune.py --cfg ... --data ... --weights ... --shortcut ... --percent ...
```


需要注意的是，这里需要在.py文件内，将opt内的cfg和weights变量指向第2步稀疏化后生成的cfg文件和weights文件。
此外，可通过增大代码中percent的值来获得更大的压缩率。（若稀疏化不到位，且percent值过大，程序会报错。）

## 2、量化

### 量化方法
`--quantized` 表示选取量化方法，默认值为-1，表示不采用任何量化方法。

`--quantized 0` 使用BNN量化方法。

BinaryNet: Training Deep Neural Networks withWeights and Activations Constrained to +1 or -1
[参考论文](https://arxiv.org/abs/1602.02830)

`--quantized 1` 使用BWN量化方法

XNOR-Net: ImageNet Classification Using Binary Convolutional Neural Networks
[参考论文](https://arxiv.org/abs/1603.05279v4)

### stage-wise 训练策略
`--qlayers`可以用于选取Darknet中的量化区间，默认为自深层到浅层, 默认值为-1表示无量化层，有效范围为0-74，取0时表示量化所有层，取74时表示无量化层，大于74则无意义。

![Darknet](https://github.com/SpursLipu/YOLOv3-ModelCompression-MultidatasetTraining/blob/master/image_in_readme/Darknet-YOLOv3.jpg)
如：

`--qlayers 63` 表示量化Darknet主体网络中最后四个重复的残差块。

`--qlayers 38` 表示量化Darknet主体网络中从倒数第二八个重复的残差块开始，量化到Darknet主体网络结束。

以此类推，量化时可根据具体情况选择何是的量化层数，以及量化进度，推荐`--qlayers`值自74逐渐下降。

量化指令范例：

```bash
python train.py --data ... --batch-size ... --weights ... --cfg ... --img-size ... --epochs ... --quantized 1 --qlayers 72
```

## 3、知识蒸馏

### 蒸馏方法
蒸馏方法采用基于Hinton于2015年提出的基本蒸馏方法，并结合检测网络做了部分改进。

Distilling the Knowledge in a Neural Network
[参考论文](https://arxiv.org/abs/1503.02531)

`--t_cfg --t_weights` 在命令中加入这两个选项即可以开始蒸馏训练。

其中：

`--t_cfg`表示教师网络配置文件。

`--t_weights`表示教师网络权重文件。

蒸馏指令范例：

```bash
python train.py --data ... --batch-size ... --weights ... --cfg ... --img-size ... --epochs ... --quantized 1 --qlayers 72 --t_cfg ... --t_weights ...
```

该指令将量化与蒸馏相结合，通过未量化的教师网络提升量化的学生网络，提高学生网络的mAP。
