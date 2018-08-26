参考地址：  
https://blog.csdn.net/xbinworld/article/details/77587406  
https://blog.csdn.net/Chris_zhangrx/article/details/78277957  
https://blog.csdn.net/u010402786/article/details/77720483  
src for caffe: https://github.com/farmingyard/ShuffleNet  
# 旷世科技 ShuffleNet  
## 创新点  
  **分组逐点卷积，通道重排**     
1. 提出了一个类似于ResNet的BottleNeck单元   
借鉴ResNet的旁路分支思想，ShuffleNet也引入了类似的网络单元。
不同的是，在stride=2的单元中，用concat操作代替了add操作，用average pooling代替了1x1stride=2的卷积操作，有效地减少了计算量和参数。
单元结构如图所示。  
2. 提出将1x1卷积采用group操作会得到更好的分类性能   
在MobileNet中提过，1x1卷积的操作占据了约95%的计算量，所以作者将1x1也更改为group卷积，使得相比MobileNet的计算量大大减少。  
3. 核心的shuffle操作将不同group中的通道进行打散，从而保证不同输入通道之间的信息传递。   
- ShuffleNet网络单元  
![ShuffleNet网络单元][1]  
- 
- 不同group间的shuffle操作  
![不同group间的shuffle操作][2]  

















  [1]: https://github.com/Melodyllsu/HelloWorld/blob/master/PNG/shufflenet01.png  
  [2]: https://github.com/Melodyllsu/HelloWorld/blob/master/PNG/shufflenet02.png
