参考地址： 
1. deeplearning.ai  
2. https://www.jianshu.com/p/05f3e7ddf1e1  

- BN的地位：与激活函数层、卷积层、全连接层、池化层一样，BN(Batch Normalization)也属于网络的一层。
- BN层是对于每个神经元做归一化处理，甚至只需要对某一个神经元进行归一化，而不是对一整层网络的神经元进行归一化。  
既然BN是对单个神经元的运算，那么在CNN中卷积层上要怎么搞？  
假如某一层卷积层有6个特征图，每个特征图的大小是100*100，这样就相当于这一层网络有6×100×100个神经元，如果采用BN，就会有6×100×100个参数γ、β，这样岂不是太恐怖了。  
因此卷积层上的BN使用，其实也是使用了类似权值共享的策略，把一整张特征图当做一个神经元进行处理。
