参考地址：   https://blog.csdn.net/hjimce/article/details/72831171  
 https://blog.csdn.net/xbinworld/article/details/77587406

# 前言  
在inception和resnet网络提出并相对完善以后，网络结构的设计就不再爆发式出现了，这两大类网路涵盖了大部分应用的卷积网络结构。
# 轻量级网络的设计目的  
在保证一定的识别精度情况下，尽可能减少网络规模（参数量、计算量）。最直接的设计目标就是用于手机等移动终端中（CPU），让移动设备也具备AI计算的能力。  
# Google MobileNet  
MobileNet的新东西不多，主要是用到了depthwise separable convolutions，包括单通道卷积以及1*1卷积，这样做的好处是把参数大大降低，计算量也大大降低。 
![mobilenet基本结构](https://github.com/Melodyllsu/HelloWorld/blob/master/PNG/mobilenet01.png)  
 **基本思想：**  
 Depthwise Conv是指只有Spatial Weight的卷积，而没有Channel方向的关系，因此每一个filter只对一个channel计算Conv，  
 大大减少了计算量和参数量；而Channel之间的feature融合通过后面的1*1常规卷积来完成；在Relu之前都带上BN层。  

