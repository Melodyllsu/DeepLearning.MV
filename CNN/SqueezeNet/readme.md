> 代码链接：https://github.com/DeepScale/SqueezeNet  
> 参考链接：https://blog.csdn.net/xbinworld/article/details/50897870  
https://blog.csdn.net/u010402786/article/details/77720483  


**What's new in SqueezeNet v1.1?**

|                 | SqueezeNet v1.0                  | SqueezeNet v1.1                  |
| :------------- |:-------------:| :-----:|
| conv1:          | 96 filters of resolution 7x7     | 64 filters of resolution 3x3     |
| pooling layers: | pool_{1,4,8}                     | pool_{1,3,5}                     |
| computation     | 1.72 GFLOPS/image                | 0.72 GFLOPS/image: *2.4x less computation* |
| ImageNet accuracy        | >= 80.3% top-5                   | >= 80.3% top-5                   |    


SqueezeNet v1.1 has 2.4x less computation than v1.0, without sacrificing accuracy.   

- SqueezeNet的工作为以下几个方面： 
1. 提出了新的网络架构Fire Module，通过减少参数来进行模型压缩
2. 使用其他方法对提出的SqeezeNet模型进行进一步压缩
3. 对参数空间进行了探索，主要研究了压缩比和3∗3卷积比例的影响  
- 摘要    
近来深层卷积网络的主要研究方向集中在提高正确率。对于相同的正确率水平，更小的CNN架构可以提供如下的优势：  
（1）在分布式训练中，与服务器通信需求更小  
（2）参数更少，从云端下载模型的数据量小  
（3）更适合在FPGA等内存受限的设备上部署  
基于这些优点，本文提出SqeezeNet。它在ImageNet上实现了和AlexNet相同的正确率，但是只使用了1/50的参数。更进一步，使用模型压缩技术，可以将SqueezeNet压缩到0.5MB，这是AlexNet的1/510。  

## 基本策略  
  **1. 将3x3卷积核替换为1x1卷积核。**  
  这一策略很好理解，因为1个1x1卷积核的参数是3x3卷积核参数的1/9，这一改动理论上可以将模型尺寸压缩9倍。  
  **2. 减小输入到3x3卷积核的输入通道数。**  
  为了保证减小网络参数，不仅仅需要减少3x3卷积核的数量，还需减少输入到3x3卷积核的输入通道数量，即式中C的数量。  
  **3. 尽可能的将降采样放在网络后面的层中。**    
  在卷积神经网络中，每层输出的特征图（feature map）是否下采样是由卷积层的步长或者池化层决定的。而一个重要的观点是：分辨率越大的特征图（延迟降采样）可以带来更高的分类精度，而这一观点从直觉上也可以很好理解，因为分辨率越大的输入能够提供的信息就越多。  
  **总结：** 以上三个策略中，前两个策略是针对如何降低参数数量，最后一个旨在最大化网络精度。  
## 网络架构 Fire Module  
  作者提出了一个类似inception的网络单元结构，取名为fire module。一个fire module 包含一个squeeze 卷积层（只包含1x1卷积核）和一个expand卷积层（包含1x1和3x3卷积核）。其中，squeeze层借鉴了inception的思想，利用1x1卷积核来降低输入到expand层中3x3卷积核的输入通道数。  
![File Module](https://www.github.com/DragonFive/CVBasicOp/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1502707058373.jpg)
- 为了保证1x1卷积核和3x3卷积核具有相同大小的输出，3x3卷积核采用1像素的zero-padding和步长
- squeeze层和expand层均采用RELU作为激活函数
- 在fire9后采用50%的dropout
- 由于全连接层的参数数量巨大，因此借鉴NIN的思想，去除了全连接层而改用global average pooling。
## 网络总体结构  
![squeezenet网络结构](https://www.github.com/DragonFive/CVBasicOp/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1502707143096.jpg)  
queezenet 1.0 : **9层fire module（2,3,4,5,6,7,8,9） + 2个卷积（1,10） + 3次maxpool（1,4,8）+ 1个avgpool（10，用于替代全连接层）**  
![squeezenet 参数数量](https://www.github.com/DragonFive/CVBasicOp/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1502707297973.jpg)

## 实验结果对比   
![不同压缩方法在ImageNet上的对比实验结果](https://github.com/Melodyllsu/HelloWorld/blob/master/PNG/squeezenet%E5%AE%9E%E9%AA%8C%E7%BB%93%E6%9E%9C%E5%AF%B9%E6%AF%94.png)  

  相比传统的压缩方法，SqueezeNet能在保证精度不损（甚至略有提升）的情况下，达到最大的压缩率，将原始AlexNet从240MB压缩至4.8MB，而结合Deep Compression后更能达到0.47MB.  
  
  




