# 关于数据增强

Author:ZhenHaoCl

Date: 2023/11/26

brief:关于本周数据增强的学习

## 如何实现

* 目前有两种思路。对数据集操作和改网络。后者难度较大，故现在在尝试前者
* 对数据集的操作主要包括以下部分
  * Mosaic（马赛克）
    * 此方法yolov5自带，且默认开启
  * 随机旋转，平移，错切，hsv增强 
    * yolo自带
    * 可通过对超参数文件的修改，来调整启用的概率
  * Albumentations数据增强包
    * 需要下载albumentations方可使用，可直接` pip install albumentations`下载
    * 这个类内置了很多数据增强的方法，可参考[最快最好用的数据增强库「albumentations」 一文看懂用法_Tina姐的博客-CSDN博客](https://blog.csdn.net/u014264373/article/details/114144303)



## 具体操作

![](.\assert\image1.png)

* 可以看到，训练时是默认数据增强的





<img src=".\assert\image2.png" alt=" " style="zoom:60%;" />

<img src=".\assert\image3.png" style="zoom:60%;" />

* dataloader.py文件中的LoadImagesAndLabels类中的getitem（）方法可以实现对图片随机旋转，平移，错切，hsv增强 等增强操作
* 我们只需调整超参数文件中的对应操作的概率即可，如下图

![](.\assert\image4.png)







<img src=".\assert\image5.png" style="zoom:60%;" />

* 与以上在超参数文件中调参不同，在下载了albumentations包后，我们需要自行进入agumentation.py文件中调参





##  效果与总结

* 在通过上述方法进行数据增强后，训练出的模型识别效果有所改善，但是还远不能达到要求
* 需要注意的是，每种增强方法启用概率的值不能太高（一般设置为0.1-.05之间），否则反而会使训练出的模型欠拟合

