# 1. 量化网络
```
./build/tools/ristretto quantize \
	--model=models/SqueezeNet/train_val.prototxt \
	--weights=models/SqueezeNet/squeezenet_v1.0.caffemodel \
	--model_quantized=models/SqueezeNet/RistrettoDemo/quantized.prototxt \
	--trimming_mode=dynamic_fixed_point --gpu=0 --iterations=2000 \
	--error_margin=3
```
- ristretto.cpp通过.sh文件得到需要设置的参数
```
DEFINE_string(model, "",//模型prototxt文件，默认值，说明
    "The model definition protocol buffer text file..");
DEFINE_string(weights, "",//caffe模型的权重
    "The trained weights.");
DEFINE_string(trimming_mode, "",//量化模式
    "Available options: dynamic_fixed_point, minifloat or "
    "integer_power_of_2_weights.");
DEFINE_string(model_quantized, "",//量化后的输出
    "The output path of the quantized net");
DEFINE_string(gpu, "",//选择gpu的型号
    "Optional: Run in GPU mode on given device ID.");
DEFINE_int32(iterations, 50,//选择迭代次数
    "Optional: The number of iterations to run.");
DEFINE_double(error_margin, 2,//设定误差允许的范围，默认值为2%
    "Optional: the allowed accuracy drop in %");
```

## tips：
- ##字符串连接

## Q&A
- Q: ristretto.cpp 与 quantization.cpp是如何联系的？A: 貌似是通过注册函数quantize。

- Q: 查询quantize产生的日志，对照quantization.cpp 150行左右输出。

- Q: 查看产生参数范围的代码。
