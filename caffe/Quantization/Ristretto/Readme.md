# Ristretto: SqueezeNet 示例 构造一个8位动态定点SqueezeNet网络
* [参考地址](https://github.com/Melodyllsu/MVision/tree/master/CNN/Deep_Compression/quantization/Ristretto#ristretto-squeezenet-%E7%A4%BA%E4%BE%8B-%E6%9E%84%E9%80%A0%E4%B8%80%E4%B8%AA8%E4%BD%8D%E5%8A%A8%E6%80%81%E5%AE%9A%E7%82%B9squeezenet%E7%BD%91%E7%BB%9C)
## 步骤
1. 量化到动态定点  
运行量化网络：./examples/ristretto/00_quantize_squeezenet.sh
2. finetune 微调动态固定点参数  
./examples/ristretto/01_finetune_squeezenet.sh  
3. SqueezeNet动态固定点基准测试  
./examples/ristretto/02_benchmark_fixedpoint_squeezenet.sh
4. 原始全精度网络测试与上面的8位定点量化的测试结果作对比  
./examples/ristretto/benchmark_floatingpoint_squeezenet.sh

## 测试结果
- finetune后  
```
I0706 17:51:22.333389 49108 caffe.cpp:309] Loss: 1.3428
I0706 17:51:22.333420 49108 caffe.cpp:321] accuracy = 0.676482
I0706 17:51:22.333433 49108 caffe.cpp:321] accuracy_top5 = 0.874484
I0706 17:51:22.333446 49108 caffe.cpp:321] loss = 1.3428 (* 1 = 1.3428 loss)

```

- 原始精度网络  
```
I0706 17:52:51.232930  1906 caffe.cpp:309] Loss: 1.29234
I0706 17:52:51.232959  1906 caffe.cpp:321] accuracy = 0.687882
I0706 17:52:51.232973  1906 caffe.cpp:321] accuracy_top5 = 0.877103
I0706 17:52:51.232987  1906 caffe.cpp:321] loss = 1.29234 (* 1 = 1.29234 loss)
```
# mobilenet模型
```
MobileNets-v1:
    论文：https://arxiv.org/pdf/1704.04861.pdf
    分类准确率：
    top-1：70.81
    top-5：89.85 
    模型大小： 16.2 MB
    模型：https://github.com/shicai/MobileNet-Caffe/blob/master/mobilenet.caffemodel
    框架：https://github.com/Ewenwan/MVision/blob/master/CNN/MobileNet/mobilenet_v1_deploy.prototxt
MobileNets-v2:
    论文：https://arxiv.org/pdf/1801.04381.pdf
    分类准确率：
        top-1：71.90%
        top-5：90.49%
    模型大小：13.5 MB
    模型：https://github.com/shicai/MobileNet-Caffe/blob/master/mobilenet_v2.caffemodel
    框架：https://github.com/Ewenwan/MVision/blob/master/CNN/MobileNet/mobilenet_v2_deploy.prototxt
```
