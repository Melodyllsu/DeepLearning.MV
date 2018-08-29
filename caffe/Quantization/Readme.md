# Quantization and Training of Neural Networks for Efficient Integer-Arithmetic-Only Inference
- by google  
参考地址： https://blog.csdn.net/xiaoxiaowenqiang/article/details/81713131  
## 主要贡献   
增加了量化区间的利用率，提高量化精度

## 实现过程
a. 记录各层 激活输入、卷积核参数、激活输出的参数范围 max min,而量化范围为0~255 uint_8

b. 计算量化参数，缩放尺度S  和  零点(偏移量) Z
   float S     = (max-min）/ (255-0);
   uint8_t Z = round(0 – min/S) ;

c. 量化输入in_ 和 卷积参数 w_
   in_quan = in_/S_in + Z_in;
   w_quan = w_/S_w  + Z_w;

d. 浮点矩阵乘法 变成 量化卷积乘法
    out_ = sum( in_(i) * w_(i) );// 浮点矩阵乘法
         = (S_in * S_W) * sum(  (in_quan-Z_in) * (w_quan – Z_w) );
    ==> out_quan = out_/S_out  + Z_out;
    ==>          = 
       (S_in * S_W/ S_out) * sum(  (in_quan-Z_in) * (w_quan – Z_w) ) + Z_out
       式中   (S_in * S_W/ S_out) 仍为浮点数，所以也需要转换成整数
 浮点乘子 real_multiplier = S_in * S_W / S_out;
 量化乘子 quantized_multiplier = round(real_multiplier * (1 << 31)); 扩大2^31次方变成32位整数

e.  之后再将整数结果转换成 浮点结果 用于后续计算
     out_ =(out_quan  -Z_out) * S_out;
## 量化方法  
    量化就是把float型的数值映射到int8或者uint8来进行卷积运算  
    缩放系数 S = (max_x - min_x)/(255-0)
    偏置零点 Z = - min_x/S
    r = S * (q-Z) 反量化
    q = r/S + Z   量化
    r - 需要量化的float型数值  
    q - 量化后的uint8类型数值  
    Z - 量化前r = 0时，量化后q的数值  
    S - 为了能把量化后的q还原到r, 引入了一个缩放系数

例如一直input的最大值是30.0，最小值是-10.0，则量化后的值为   
Quantized | Float  
--------- | -----  
0         | -10.0  
255       | 30.0  
128       | 10.0  

    如何把float类型的乘法用int8替代，paper中的公式写的很明白，  
    输入： r1 = S1 * (q1 - Z1)
          r2 = S2 * (q2 - Z2)
    乘法： out = r1 * r2 = S1 * (q1 - Z1) * S2 * (q2 - Z2)  
          q_out = out / S3 + Z3 
                =  S1 * S2 / S3 * (q1 - Z1) * (q2 - Z2)  + Z3
                = M * (q1 - Z1) * (q2 - Z2)  + Z3   公式(4)
          乘子 M = S1 * S2 / S3
    本来公式中全部都是int8类型了，只有的个 M 仍然是float类型的，  
    但是据经验值M是一个大于0小于1的数值，于是我们对M做一些小操作：
    M = M0 / 2^n    
    令M0在[0.5, 1)范围内， 
    举个例子， M = 0.3， 那么M0 = 0.3 * 2， 于是n = 1，然后我们把M0量化到整形数，  
    具体是16位还是32根据机器决定，以32位为例，M0 = M0 * (1 << 31 ),  
    取整后 M0 就是一个32位的整型数，此时n = 32，  
    因此公式(4)中加号后半部分全部为 整型乘法 和 移位操作(   
    这里M0和另外一部分都为32位的整型，其乘积结果应该是64位的整型) 

# Incremental Network Quantization: Towards Lossless CNNs with Low-precision Weights    
参考链接：https://blog.csdn.net/cookie_234/article/details/75386737  
## 前言  
INQ神经网络无损低比特量化技术  
给定任意结构的全精度浮点神经网络模型，能将其转换成无损的低比特二进制模型；   
文章分析现有的量化压缩方法有两点不足：1.量化过程中的精度损失仍然不可忽视；2.大多数量化压缩方法只适用于处理特定的模型结构和特定类型的层，一定程度限制了泛化能力和量化压缩的性能（这里指的是很多方法对fc层的压缩比较有效，最终得到的比较大的压缩率很大一部分贡献是fc）；另外还有重训练时间长等问题；  
## 方法  
**提出了渐进式神经网络量化的思想，引入了三种操作：参数分组，量化，重训练。**  
简单的说就是在训练得到一个网络模型后，首先将这个全精度浮点网络模型中的每一层参数分为两组，第一组中的参数直接被量化固定，另一组参数通过重训练来补偿量化给模型造成的精度损失。然后这三个操作依次迭代应用到刚才的第二组完成重训练之后的全精度浮点参数部分，直到模型全部量化为止。   
可以说参数分组分成的这两个部分是互补的作用，一个建立低精度模型的基础，另一个通过retrain补偿精度损失；这样迭代最终得到渐进式的量化和精度提升。  
## 权重分组   
**分组的方式文中讨论了两种：random strategy　和　pruning-inspired strategy。**  
1. random strategy:随机的把权值分成两个不重叠的部分，相当于是按相等的概率对待每一个值，有同等的概率被分到两组中；  
2. pruning-inspired strategy:基于prune的策略是：逐层确定每层自己的thresholds（一般是given splitting ratio），取weights的绝对值和thresholds比较，最后分成两个不重叠的组。分成的两部分肯定一组值大一点一组值小一点，那么该选取哪部分先做quant呢？文中说取大的部分去做quant，大的这部分被认为是low-precision base.     
## 分组量化  
量化的目的是给定一个训练好的全精度的CNN网络模型，将全部的weights从32位浮点数转为2的整数次幂或者0，而不损失精度。 
文章中采用的量化的形式是：量化最后Wl的每个值是和Pl中的值对应的，也就是2的整数次幂或0；其中只有n1一个超参数，n1是根据这个公式得到的，s就是简单的对所有weights取绝对值取最大。。。bitwidth=b是由我们设定的，n1知道了，n2可以通过n1和b求得.  


# Ristretto   
本文介绍了几种网络压缩的方法，压缩特征图和参数。
    方法包括：
        定点法（Fixed Point Approximation）、
        动态定点法（Dynamic Fixed Point Approximation）、
        迷你浮点法（Minifloat Approximation）和
        乘法变移位法（Turning Multiplications Into Bit Shifts）  
## 1、 定点法（Fixed Point Approximation） 
    所谓定点数和浮点数，
    是指在计算机中一个数的小数点的位置是固定的还是浮动的：
    如果一个数中小数点的位置是固定的，则为定点数；
    如果一个数中小数点的位置是浮动的，则为浮点数。
    一般来说，定点格式可表示的数值的范围有限，但要求的处理硬件比较简单。
    而浮点格式可表示的数值的范围很大，但要求的处理硬件比较复杂。
    
    IL.FL
    固定整数位二进制长度和小数位二进制长度。
    最大表示数： 
       Xmax = 2^(IL-1) - 2^(-FL)
    
    32浮点数----->8位定点(Q4.4)
           ----->16位定点 ( Q8.8    Q9.7)
           
     例如 0 1 1 0 1 1 0 1 , FL = 2
     符号位: 0
     尾数部分全部作为整数位： 1 1 0 1 1 0 1 B  = 109D 
     小数部分最后两位，所以整体需要除以2^(2)
     则表示的数为: R = (-1)^0 * 109 * 2^(-2) = 27.25  
## 2、 动态定点法（Dynamic Fixed Point Approximation）
    CNN的不同部分具有显着的动态范围，
    在大的层中，输出是数以千计的积累的结果，因此网络参数比层输出小得多。
    定点只具有有限的能力来覆盖宽动态范围。
    使用B位来表示，其中一位符号位s, fl是分数长度(小数长度)，其余为整数位，
    除去符号位s，后面的都是尾数，每个尾数位数字为xi

    n = (-1)^s * 2^(-fl)*sum(2^i * xi)  , 0 =< i <= B-2
    第一项确定符号，
    第二项确定分数比例，
    第三项确定总值大小(所有尾数对应的十进制的值)。

    由于网络中的中间值具有不同的范围，所以希望将定点数值分组为具有常数f1(不同小数长度)的组中。
    所以分配给小数部分的比特数在 同一组内是恒定的，但与其他组相比是不同的。

    这里每个网络层分为三组：
        一个用于层输入，
        一个用于权重，
        一个用于层输出。
    这可以更好地覆盖层激活和权重的动态范围，因为权重通常特别小，层的输入输出通常比较大(上一层各个神经元累计)。
## 3、 迷你浮点法（Minifloat Approximation）
    IEEE-754 32位浮点数:
                struct MYFLOAT
                {
                bool bSign : 1;                // S 符号，表示正负，1位
                char cExponent : 8;            // E 指数，8位,存储的时候，比实际的多 127
                unsigned long ulMantissa : 23; // M 尾数，23位
                };
                // B = (-1)^S * 2^E * M
                // 不过指数部分在存储的时候回 加上127(使用移位存储, 正负表达的范围较平均)
                // 所以 指数部分按二进制数转到10进制数后需要减去 127， E = E' - 127
        bSign --- cExponent --- ulMantissa
        符号位 --- 指数位    --- 尾数位
        
    迷你浮点数 16bit  5位指数部分 10位位数部分(精度, 1/(2^(10)) = 0.0009765, 精确到小数点后3位 )  
    
## 4、  乘法变移位法（Turning Multiplications Into Bit Shifts）
    直接量化成 二进制位的形式，最高位位符号位S，后面的二进制指数部分
    例如4bits
    1100
    第一个1为符号位，后面3个表示指数部分(-1~-8) 
    n=(−1)^s * 2^exp  
    
    
