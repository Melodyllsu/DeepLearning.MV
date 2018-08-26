# 轻量级CNN网络设计---MobileNet,ShuffleNet,SqueezeNet
> 参考网址：https://blog.csdn.net/xbinworld/article/details/77587406  
https://blog.csdn.net/u010402786/article/details/77720483
  
  
# 常见的网络压缩方法  
> 自从AlexNet一举夺得ILSVRC 2012 ImageNet图像分类竞赛的冠军后，卷积神经网络（CNN）的热潮便席卷了整个计算机视觉领域。CNN模型在不断逼近计算机视觉任务的精度极限的同时，其深度和尺寸也在成倍增长。随之而来的是一个很尴尬的场景：如此巨大的模型只能在有限的平台下使用，根本无法移植到移动端和嵌入式芯片当中;另一方面，大尺寸的模型也对设备功耗和运行速度带来了巨大的挑战。  
- 常用的压缩模型技术  
    1. 奇异值分解(singular value decomposition (SVD))
    2. 网络剪枝（Network Pruning）：使用网络剪枝和稀疏矩阵
    3. 深度压缩（Deep compression）：使用网络剪枝，数字化和huffman编码
    4. 硬件加速器（hardware accelerator）  
    
- ![几种经典的压缩方法及对比](https://github.com/Melodyllsu/HelloWorld/blob/master/PNG/cnn%E5%B8%B8%E8%A7%81%E7%9A%84%E5%8E%8B%E7%BC%A9%E6%96%B9%E6%B3%95.PNG)
    
