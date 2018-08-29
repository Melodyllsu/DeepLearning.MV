参考地址： https://blog.csdn.net/mulinb/article/details/53421864  

## PTAM是keyframe-based SLAM派系里最出名的一个算法 (2007-2008)   
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


## ORB-SLAM (2014-2015)   
code: https://github.com/raulmur/ORB_SLAM2   
- 最近比较火的ORB-SLAM算法，是keyframe-based SLAM派系的一个集大成者。ORB-SLAM算法基本沿用了PTAM的框架，
将近几年来被验证有效的module都加了进来，做出一个稳定性和精度很高、可以用于室内/室外和小规模/大规模等各种场景的全能系统，刷爆各种benchmark，
并开源了质量很高的代码，还摘得了IEEE Transactions on Robotics的年度best paper award。  
- **性能**  
从paper中report的速度上来看，ORB-SLAM能在Intel Core i7-4700MQ (4 cores 2.40GHz)上track 512x382的视频流达到30fps。
鉴于这个CPU比PTAM的CPU要强不少，这个算法应该比PTAM慢不少。  

## LSD-SLAM (2013-2014)  
code： https://github.com/tum-vision/lsd_slam  

