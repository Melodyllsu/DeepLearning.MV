参考链接：http://alanse7en.github.io/caffedai-ma-jie-xi-1/

# Caffe主要包含了4个大类:  
    Solver: An interface for classes that perform optimization on Nets
    Net: Connects Layers together into a directed acyclic graph (DAG) specified by a NetParameter
    Layer: An interface for the units of computation which can be composed into a Net
    Blob: A wrapper around SyncedMemory holders serving as the basic computational unit through which Layers, Nets, and Solvers interact
    Blob是一个包装类，作为基础的计算单元，提供层，网络，求解器间之间的内存同步。
 
 1. 其中Solver这个类实现了优化函数的封装，其中有一个protected的成员:shared_ptr<Net > net_;，
 这个成员是一个指向Net类型的智能指针（shared_ptr），Solver正是通过这个指针来和网络Net来交互并完成模型的优化。
 不同的子类分别实现了不同的优化方法：SGDSolver, NesterovSolver, AdaGradSolver, RMSPropSolver, AdaDeltaSolver和AdamSolver。
 2. 类似地Layer这个类派生出了很多子类，这些子类实现了Data的读取和Convolution, Pooling, InnerProduct等各种功能的layer。
 3. Net则是对整个网络的一个封装，其中有一个成员为:vector<shared_ptr<Layer > > layers_;，
 这个vector中包含了整个网络中每一层layer的智能指针，Net通过调用这些layer各自的forward()和backward()接口实现了网络整体的ForwardBackward()。
 4. Blob则是Caffe对数据的封装，在整个网络的计算中，不管是数据还是网络的参数和梯度都是这个类的对象，均为num\*channel\*width\*height形式的数据。  
-----
# .proto文件
> 除了清晰的代码结构，让Caffe变得易用更应该归功于Google Protocol Buffer的使用。
Google Protocol Buffer是Google开发的一个用于serializing结构化数据的开源工具  
      
> http://alanse7en.github.io/caffedai-ma-jie-xi-2/    
    
Caffe使用这个工具来定义Solver和Net，以及Net中每一个layer的参数。
这使得只是想使用Caffe目前支持的Layer(已经非常丰富了)来做一些实验或者demo的用户可以不去和代码打交道，
只需要在*.prototxt文件中描述自己的Solver和Net即可，再通过Caffe提供的command line interfaces就可以完成模型的train/finetune/test等功能。
   
-----------------
# 命令行接口

