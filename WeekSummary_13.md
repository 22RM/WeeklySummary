> Author: 韩栋林
>
> date: 2023/11/25
>
> brief:  Summary of week 13 

# 第二周任务流程

## 数据集的训练与训练结果的检验

### 将大家标注的图片汇总后按照要求写出yaml文件，开始训练

![yaml](https://cdn.jsdelivr.net/gh/cabbageB/images@main/img/202311271718262.png)

不一定非要将数据集弄到yolo的目录下，只要路径正确就行

```shell
# 训练过程中的参数
--batch-size 2
--epoches 300
--weights yolov5s.pt
其它参数为默认值
```

### 从9点训练到第二天早上快8点，检测结果如下

![图片检测](https://cdn.jsdelivr.net/gh/cabbageB/images@main/img/202311271724686.jpg)

可以看到置信度高达0.97，说明训练效果还可以。

### 使用本地摄像头检测，改变距离与位置，检测训练结果

```shell
# 检测过程中的一些参数
--source 0 # 调用本地摄像头
--weights best.pt

```



> 第一次检测,贴近摄像头

![靠近摄像头](https://cdn.jsdelivr.net/gh/cabbageB/images@main/img/202311271725472.png)

如果是贴近摄像头进行检测，效果其实还可以。

> 第二次检测，倾斜图片

![倾斜图片](https://cdn.jsdelivr.net/gh/cabbageB/images@main/img/202311271726706.png)

这是倾斜一定角度下的检测结果，随着倾斜角度的增加，检测的置信度会逐渐下降。

> 第三次检测，拉开一定距离

![短距离](https://cdn.jsdelivr.net/gh/cabbageB/images@main/img/202311271726277.png)

可以看到拉开一定距离后，检测框的置信度很低，检测效果不好。

> 第四次检测，拉开1m左右

![拉开一米](https://cdn.jsdelivr.net/gh/cabbageB/images@main/img/202311271727492.png)

![拉开1m(2)](https://cdn.jsdelivr.net/gh/cabbageB/images@main/img/202311271810451.png)

可以看到，拉开一米正对摄像头时，检测效果很不稳定，甚至是无法识别到。

> 强光环境下

![强光检测](https://cdn.jsdelivr.net/gh/cabbageB/images@main/img/202311271811516.png)

可以看到，强光环境下，即使是非常清晰的图像也无法识别到。

## 图像增强

### 数据处理

> 关于矩形训练（rec）

一般我们默认的图片大小是640X640，这种情况下一些图片会难免出现一些黑边，在实际训练过程中，黑边也会被纳入计算，这样就会增加我们的计算量，进行矩形训练同个batch里做rectangle宽高等比变换，减少黑边，减少计算量。

```python
# Rectangular Training 是否采用矩形训练，可以减少计算量（重新计算每个batch里的shape，减少黑边）
        if self.rect:
            # Sort by aspect ratio
            s = self.shapes  # wh
            ar = s[:, 1] / s[:, 0]  # aspect ratio h/w
            irect = ar.argsort()  # 对所有的比例进行排序
            # 对所有数据根据排序的结果重新排列
            self.im_files = [self.im_files[i] for i in irect]
            self.label_files = [self.label_files[i] for i in irect]
            self.labels = [self.labels[i] for i in irect]
            self.segments = [self.segments[i] for i in irect]
            self.shapes = s[irect]  # wh
            ar = ar[irect]

            # Set training image shapes 对每个batch里的shape重新计算
            shapes = [[1, 1]] * nb
            for i in range(nb):
                ari = ar[bi == i]
                mini, maxi = ari.min(), ari.max()
                 # 如果maxi<1,说明所有图片的高都比宽小，也就是上下的黑边较多，因此我们取maxi，这样可以最大程度的减少黑边
                if maxi < 1: 
                    shapes[i] = [maxi, 1]
                elif mini > 1:  # 同理
                    shapes[i] = [1, 1 / mini]

            self.batch_shapes = np.ceil(np.array(shapes) * img_size / stride + pad).astype(int) * stride

```

![原图](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311282312031.png)![默认尺寸](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311282311232.png)

![矩形训练后的图片](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311282312750.png)结果如图所示。



> 关于图像的平移、旋转、缩放等操作

对于图像的这些操作，首先是初始化一个3x3的矩阵，然后得到每个操作对应的矩阵，矩阵中会含有相应操作的一些参数。

```py
 # Center 中心点的平移
    C = np.eye(3) # 初始化3x3的一个矩阵
    C[0, 2] = -im.shape[1] / 2  # x translation (pixels)
    C[1, 2] = -im.shape[0] / 2  # y translation (pixels)

    # Perspective 透明度变化
    P = np.eye(3)
    P[2, 0] = random.uniform(-perspective, perspective)  # x perspective (about y)
    P[2, 1] = random.uniform(-perspective, perspective)  # y perspective (about x)

    # Rotation and Scale 旋转与缩放
    R = np.eye(3)
    a = random.uniform(-degrees, degrees)
    # a += random.choice([-180, -90, 0, 90])  # add 90deg rotations to small rotations
    s = random.uniform(1 - scale, 1 + scale)
    # s = 2 ** random.uniform(-scale, scale)
    R[:2] = cv2.getRotationMatrix2D(angle=a, center=(0, 0), scale=s)

    # Shear 正切
    S = np.eye(3)
    S[0, 1] = math.tan(random.uniform(-shear, shear) * math.pi / 180)  # x shear (deg)
    S[1, 0] = math.tan(random.uniform(-shear, shear) * math.pi / 180)  # y shear (deg)

    # Translation 平移
    T = np.eye(3)
    T[0, 2] = random.uniform(0.5 - translate, 0.5 + translate) * width  # x translation (pixels)
    T[1, 2] = random.uniform(0.5 - translate, 0.5 + translate) * height  # y translation (pixels)

```

最后将所有矩阵做乘法

```py
# Combined rotation matrix
 # order of operations (right to left) is IMPORTANT，将所有矩阵乘起来，得到最终变换的矩阵
    M = T @ S @ R @ P @ C 
```

完成对图片的平移、旋转、缩放等操作后，对应的标签也会改变。

### yolo的backcone网络学习

不同的训练模型区别在于深度因子与宽度因子

> ```py
># yolov5s
> depth_multiple: 0.33 
> width_multiple: 0.50 
> # yolov5m
> depth_multiple: 0.67
> width_multiple: 0.75
> # yolov5l
> depth_multiple: 1.0  
> width_multiple: 1.0
> # yolov5x
> depth_multiple: 1.33
> width_multiple: 1.25
> ```

**backcone网络**：FAN + PAN

这里涉及到神经网路，有点难以理解，提到了卷积，在知乎上查了一下还是不太理解，应该就是处理信号与图像等的一种技术，暂时不深入学习。

~~学不了一点跑路了~~

## 关于目标检测

**1.重新制作一个数据集**

实验室制作的数据集中大多无倾斜角度、弱光环境下、强光环境下的图片，如果我们在制作数据集时把这些特殊情况也拍下来训练，那么在识别过程中的效果会不会更好。

**2.数据增强**

目前我们在使用的数据增强可能是对于数据集中的每张图片进行几何变换与像素变换，但是根据去年学长们的情况，数据集增强到2万多张效果还是不行，所有我们不能仅仅通过数据增强取完成目标检测。

**3.一些想法**

我们能不能直接从检测入手，处理摄像头获取的每一帧照片，对获取的每一帧图片进行亮度调节、加噪声、饱和度调节等操作，使其变为易于识别的图片。
