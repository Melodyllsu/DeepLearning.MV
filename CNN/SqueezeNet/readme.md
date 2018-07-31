> 代码链接：https://github.com/DeepScale/SqueezeNet  
> 参考链接：


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

