1. 查看显卡的使用情况  
$ nvidia-smi
2. 从零开始训练  
./darknet detector train cfg/voc.data cfg/yolov3-voc.cfg darknet53.conv.74 
3. 断点继续训练  
./darknet detector train cfg/voc.data cfg/yolov3-voc.cfg backup/voc/yolov3-voc.backup -gpu 0

4. 查看文件夹的空间  
df -h
5. 回到账号的home  
cd -
6. 多GPU训练  
./darknet detector train cfg/voc.data cfg/yolov3-voc.cfg backup/voc/yolov3-voc.backup -gpus 0,1,2,3
6.1 【2>&1 | tee paul_train_log.txt】存储了训练日志  
./darknet detector train cfg/voc_my_cfg.data cfg/yolov3-voc.cfg backup/yolov3-voc_97000.weights -gpus 0,1,2,3 2>&1 | tee paul_train_log.txt

7. 测试自己训练的权重  
./darknet detect cfg/yolov3-voc.cfg backup/voc/yolov3-voc_final.weights data/person.jpg

8. 建立链接  
sudo ln -s 地址 .

9. 修改某个文件夹的权限，使其能被  
sudo chmod -R 777 caffe/

10. more 浏览某个文件 esc退出  

11. rm -rf 文件夹   
迭代移除文件夹

12. 修改密码  
1、sudo su转为root用户  
2、sudo passwd user(user 是对应的用户名)   

13. 查找位于当前路径下包含某变量的文件，具体到某个文件的第几行
grep -rnI il_in_ .  
grep -rnI ConvolutionRistretto .  
grep -rnI REGISTER_LAYER_CLASS .  

14. 查看git的版本并初始化  
git log  
git init  

14.1. git中加入文件  
git add *.cpp  
git add *.c  
git add *.h  

15. 显示文件状态（查看哪些文件被add到git中了），也用于找到有改动的文件  
git status  

16. 提交新版本  
git commit -s  
git commit -m <message>
 
17. 邮箱和账户名   
git config --global user.email "chensuyue@hik.com"  
git config --global user.name "chensuyue"  

18. 找到某个文件中修改的细节    
git diff [file]    

19. 回到上一个版本  
git checkout [file]  

20. 查看电脑资源使用情况  
htop  如果没有就安装 sudo apt-get install htop  

