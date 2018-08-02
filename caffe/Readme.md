* [Caffe学习系列]（http://www.cnblogs.com/denny402/tag/caffe/default.html?page=2）
- 绘制prototxt流程图： http://ethereon.github.io/netscope/#/editor  

- [caffe源码目录说明](https://www.hongweipeng.com/index.php/archives/1096/)

- [Google Protocol Buffers介绍](https://blog.csdn.net/fengbingchun/article/details/49977903)

- [.proto文件的中文注释](https://blog.csdn.net/mac_lzq/article/details/63725009)

- [caffe 源码解析](https://blog.csdn.net/tianrolin)

- [caffe 代码解析](http://alanse7en.github.io/caffedai-ma-jie-xi-4/)

###  构建网络
1. 使用Caffe自带的网络配置文件lenet_train_test.prototxt，开始启动训练网络.  
2. caffe.cpp文件的 main() 函数中通过宏隐式的调用了函数 train()  
3. 在train()中创建solver智能指针，调用了CreateSolver()函数（该函数根据配置文件创建对应的Solver对象（默认为SGDSolver子类对象）。此处工厂模式和一个关键的宏REGISTER_SOLVER_CLASS(SGD)发挥了重要作用）。
4. 一个SGDSolver对象就被动态创建出来了。在Solver基类的构造函数中，调用了成员函数Init()实现初始化,其中包含初始化训练网络，初始化测试网络，迭代次数清零.  
5. 构建网络的代码便藏身在成员函数InitTrainNet()中。最后一行语句动态创建Net对象，并构造了智能指针对象net_。    
6. Net::Init()成员函数完成网络初始化。 

### 训练网络  
1. 在函数train()中，使用前一步创建好的solver智能指针对象调用函数Solve()。
2. 在Solve()函数内部调用Solver::Step()函数实现iteration的迭代。从起始迭代补数到终止迭代步数。
3. 在Step()函数的while循环中，实现调用网络类Net::ForwardBackward()成员函数进行正向传导和反向传导。
```c
template <typename Dtype>
class Net
{
    Dtype ForwardBackward()
    {
        Dtype loss;
        Forward(&loss);  // 正向传导
        Backward();      // 反向传导
        return loss;
    }
}
```
4. 正向传导函数Forward()调用了ForwardFromTo(int start, int end)函数，在该函数中，将整个网络从start到end遍历每一层，调用各层的（layers_为指向父类layer的智能指针）layers_[i] -> Forward(bottom_vecs_[i], top_vecs_[i]).若该层为loss层则计算layers_loss,否则返回0.  
> 虽然Forward()不是虚函数，但是它包装了虚函数Forward_cpu()和Forward_gpu()，分别对应CPU版本和GPU版本。其中Forward_cpu()为父类Layer的纯虚函数，必须被子类重载。而Forward_gpu()在父类Layer中的实现为直接调用Forward_cpu()，于是该虚函数的实现为可选。总的来说，正因为这两个虚函数，所以不同层有不同的正向传导计算方法。  
> 在Step()函数的while循环中,终止条件是max_iter. ForwardBackward()外部还有一层for循环，终止条件是iter_size，为了在内存有限的情况下扩大实际batch = batch_size × iter_size.  每一组iter_size计算一次loss。
5. 反向传导函数Backward()调用了BackwardFromTo(int start, int end)函数。   
> 注意反向传导比正向传导多了一个参数bottom_need_backward_。在实现反向传导时，首先判断当前层是否需要反向传导的层，不需要则直接返回。  
6. 正向传导和反向传导结束后，再调用SGDSolver::ApplyUpdate()成员函数进行权重更新。(根据选择的策略不同，调用solvers目录中不同的优化函数)。 
7. 最后将迭代次数++iter_，继续while循环，直到迭代次数完成。

