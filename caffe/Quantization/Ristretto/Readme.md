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

*以上脚本及prototex中应注意路径的配置

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

# ResNet

- 第一组
## quantization 结果（2000）
```
I0710 00:38:40.907178 20641 quantization.cpp:277] Network accuracy analysis for
I0710 00:38:40.907184 20641 quantization.cpp:278] Convolutional (CONV) and fully
I0710 00:38:40.907191 20641 quantization.cpp:279] connected (FC) layers.
I0710 00:38:40.907196 20641 quantization.cpp:280] Baseline 32bit float: 0.88
I0710 00:38:40.907210 20641 quantization.cpp:281] Dynamic fixed point CONV
I0710 00:38:40.907217 20641 quantization.cpp:282] weights: 
I0710 00:38:40.907222 20641 quantization.cpp:284] 16bit: 	0.88
I0710 00:38:40.907230 20641 quantization.cpp:284] 8bit: 	0.8775
I0710 00:38:40.907238 20641 quantization.cpp:284] 4bit: 	0.001
I0710 00:38:40.907274 20641 quantization.cpp:287] Dynamic fixed point FC
I0710 00:38:40.907281 20641 quantization.cpp:288] weights: 
I0710 00:38:40.907289 20641 quantization.cpp:290] 16bit: 	0.88
I0710 00:38:40.907315 20641 quantization.cpp:290] 8bit: 	0.879375
I0710 00:38:40.907323 20641 quantization.cpp:290] 4bit: 	0.79075
I0710 00:38:40.907331 20641 quantization.cpp:292] Dynamic fixed point layer
I0710 00:38:40.907351 20641 quantization.cpp:293] activations:
I0710 00:38:40.907357 20641 quantization.cpp:295] 16bit: 	0.03475
I0710 00:38:40.907377 20641 quantization.cpp:298] Dynamic fixed point net:
I0710 00:38:40.907395 20641 quantization.cpp:299] 8bit CONV weights,
I0710 00:38:40.907402 20641 quantization.cpp:300] 8bit FC weights,
I0710 00:38:40.907408 20641 quantization.cpp:301] 32bit layer activations:
I0710 00:38:40.907423 20641 quantization.cpp:302] Accuracy: 0.0293125
I0710 00:38:40.907444 20641 quantization.cpp:303] Please fine-tune.
```
Q1：可见16bit的输入不符合要求，那么使用的是32bit的int还是float，既然其余两个的精度很高，为什么平均精度会这么低。在嵌入式设备中这里的精度是怎么选择的。
A1: 使用的是float

## funetuning后精度
```
I0711 18:20:30.194677 16329 caffe.cpp:309] Loss: 0
I0711 18:20:30.194707 16329 caffe.cpp:321] accuracy = 0.0291875
I0711 18:20:30.194721 16329 caffe.cpp:321] accuracy_top5 = 0.09175
```
## 原始精度
```
I0711 18:34:07.630069 17645 caffe.cpp:309] Loss: 0
I0711 18:34:07.630157 17645 caffe.cpp:321] accuracy = 0.88
I0711 18:34:07.630223 17645 caffe.cpp:321] accuracy_top5 = 0.978875

```

# VggNet
- 第一组
## quantization 结果（1000）
```
I0711 18:35:49.334671 15096 quantization.cpp:277] Network accuracy analysis for
I0711 18:35:49.334677 15096 quantization.cpp:278] Convolutional (CONV) and fully
I0711 18:35:49.334682 15096 quantization.cpp:279] connected (FC) layers.
I0711 18:35:49.334689 15096 quantization.cpp:280] Baseline 32bit float: 0.77464
I0711 18:35:49.334700 15096 quantization.cpp:281] Dynamic fixed point CONV
I0711 18:35:49.334738 15096 quantization.cpp:282] weights: 
I0711 18:35:49.334758 15096 quantization.cpp:284] 16bit: 	0.77104
I0711 18:35:49.334784 15096 quantization.cpp:284] 8bit: 	0.770839
I0711 18:35:49.334810 15096 quantization.cpp:284] 4bit: 	0.001
I0711 18:35:49.334820 15096 quantization.cpp:287] Dynamic fixed point FC
I0711 18:35:49.334825 15096 quantization.cpp:288] weights: 
I0711 18:35:49.334830 15096 quantization.cpp:290] 16bit: 	0.77204
I0711 18:35:49.334838 15096 quantization.cpp:290] 8bit: 	0.77244
I0711 18:35:49.334846 15096 quantization.cpp:290] 4bit: 	0.765439
I0711 18:35:49.334872 15096 quantization.cpp:290] 2bit: 	0.00739999
I0711 18:35:49.334882 15096 quantization.cpp:292] Dynamic fixed point layer
I0711 18:35:49.334913 15096 quantization.cpp:293] activations:
I0711 18:35:49.334934 15096 quantization.cpp:295] 16bit: 	0.77304
I0711 18:35:49.334944 15096 quantization.cpp:295] 8bit: 	0.755839
I0711 18:35:49.334950 15096 quantization.cpp:295] 4bit: 	0.15168
I0711 18:35:49.334959 15096 quantization.cpp:298] Dynamic fixed point net:
I0711 18:35:49.334964 15096 quantization.cpp:299] 8bit CONV weights,
I0711 18:35:49.334969 15096 quantization.cpp:300] 4bit FC weights,
I0711 18:35:49.334976 15096 quantization.cpp:301] 8bit layer activations:
I0711 18:35:49.334981 15096 quantization.cpp:302] Accuracy: 0.736039
I0711 18:35:49.335003 15096 quantization.cpp:303] Please fine-tune.
```

## funetuning后精度
```
I0712 02:56:09.988304 29455 solver.cpp:331]     Iteration 2000, loss = 1.00953
I0712 03:01:59.951297 29455 solver.cpp:418]     Test net output #0: accuracy = 0.775083
I0712 03:01:59.951407 29455 solver.cpp:418]     Test net output #1: accuracy_top5 = 0.94352
```

## 原始精度
```
I0712 03:19:40.112874 39099 caffe.cpp:309] Loss: 0
I0712 03:19:40.113019 39099 caffe.cpp:321] accuracy = 0.775023
I0712 03:19:40.113091 39099 caffe.cpp:321] accuracy_top5 = 0.9435
```
