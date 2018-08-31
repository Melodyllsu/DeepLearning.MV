参考地址： https://blog.csdn.net/mulinb/article/details/53421864  

## PTAM是keyframe-based SLAM派系里最出名的一个算法（单目） (2007-2008)   
code: https://github.com/Oxford-PTAM/PTAM-GPL  
- 它开创了多线程SLAM的时代，后来的多数keyframe-based SLAM都是基于这个框架。
PTAM受到了广泛采用bundle adjustment (BA)的Nister算法的启发，将tracking和mapping分成两个单独的线程，这样既可以不影响tracking的实时体验，
又可以在mapping线程中放心使用BA来提高精度（另外BA也没有必要对所有frame做，只对一些keyframe进行BA即可）。  
这样以来，由于BA的引入，PTAM的精度得到了大幅提高，连Davison自己都承认MonoSLAM被PTAM clearly beaten了.  
- PTAM的数据结构主要包括keyframe和3D map point。Keyframe保存的是camera pose及一个4-level的image pyramid (从640x480到80x60)。
3D map point保存的是3D坐标、patch normal、以及来自哪个keyframe的哪一层。
系统运行时通常有大约100个keyframes和几千个3D map points。  
- **性能**  
PTAM在paper [4]中report的速度是在Intel Core2 Duo E6700 2.66 GHz处理器上处理640x480的frame能达到30fps，
tracking中主要时间花费在3D到2D投影后的搜索correspondence上。
另外mapping线程中的bundle adjustment一般还是比较慢，为了跟上map expanding，global BA会经常被打断。  


## ORB-SLAM （单目，双目，RGBD）(2014-2015)   
code: https://github.com/raulmur/ORB_SLAM2   
paper： http://webdiis.unizar.es/~raulmur/MurMontielTardosTRO15.pdf   
- 最近比较火的ORB-SLAM算法，是keyframe-based SLAM派系的一个集大成者。ORB-SLAM算法基本沿用了PTAM的框架，
将近几年来被验证有效的module都加了进来，做出一个稳定性和精度很高、可以用于室内/室外和小规模/大规模等各种场景的全能系统，刷爆各种benchmark，
并开源了质量很高的代码，还摘得了IEEE Transactions on Robotics的年度best paper award。  
- **性能**  
从paper中report的速度上来看，ORB-SLAM能在Intel Core i7-4700MQ (4 cores 2.40GHz)上track 512x382的视频流达到30fps。
鉴于这个CPU比PTAM的CPU要强不少，这个算法应该比PTAM慢不少。  

## LSD-SLAM（单目，双目，RGBD） (2013-2014)  
code： https://github.com/tum-vision/lsd_slam  
- Jakob Engel在ICCV 2013提出的semi-dense VO方法就是LSD-SLAM的前身。算法思想如下：算法仍然分为两个独立的线程分别做tracking和mapping，假设已知前两帧图像对应的depth map（这个初始的depth map可以通过传统的correspondence方法计算或者初始化为随机值），tracking线程通过最小化photometric error来求解相机姿态。  
- LSD-SLAM就是在上面这个VO算法基础上，加入keyframe的管理及优化、loop closure等机制使得上面的VO算法真正成为一个完整的SLAM系统。    

- **优缺点**  
算法优点：对map uncertainty的model比较好；对于low texutre场景应该比较鲁棒；可以建相对比较dense的地图  
算法缺点：算法目前计算量还是有点大，用i7的CPU勉强可以达到实时；从AR的效果看似乎抖动比较明显，似乎精度仍不如ORB-SLAM  
- **性能**    


## DSO（单目） (2016)  
code： https://github.com/JakobEngel/dso    
- DSO是LSD-SLAM的作者Jakob Engel最近放出的另一个大杀器，从其展示的实验结果看，无论是robustness，或是accuracy，或是计算速度，都完爆LSD-SLAM和ORB-SLAM。 
- **性能**  
算法的计算速度上来看，正常设置可以在Intel i7-4910MQ CPU上处理640x480图像达到realtime的速度，参数低配时可以处理424x320图像达到5倍realtime的速度（不知道具体多少，150fps?）。  

## SOFT-SLAM2（2017） ：面向自主无人机的高效立体视觉  
SOFT-SLAM: Computationally Efficient Stereo Visual SLAM for Autonomous UAVs  
code： nan  
能够输出高精度地图以及稠密环境地图的立体视觉SLAM系统  
目前在KITTI数据集中居于首位通过SOFT（基于特征跟踪的立体视觉里程计算法）进行位姿估计，建立一个基于特征点的位姿图SLAM方案。  
SOFT-SLAM具有两个独立的线程分别进行里程计和建图工作，并且支持大范围的回环检测以及保证系统安全一致性。  
