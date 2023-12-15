# learn yolov5  
 
## 制作数据集    
1. 爬取图片(需要添加噪声) 
2. 将图片裁剪成相同形状，才能放入yolov5模型中 ，yolov5会自行处理
3. 将数据集分成training、validation、test三个集，比例为6：2：2 



## 训练模型  
### 选用yolov5s模型  


train、val和test数据集需要在image中和labels的文件夹中名字相同  
原理： 
组织目录 根据下面的示例组织您的训练和验证图像和标签。YOLOv5 假设“/coco128”位于“/datasets”目录**“/yolov5”目录**旁边**。**YOLOv5 通过用“/labels/”替换每个图像路径中最后一个“/images/”实例，自动为每个图像定位标签。  
```  
../datasets/coco128/images/im0.jpg  # image
../datasets/coco128/labels/im0.txt  # label
```  
  
### 训练  
1. train.py 
 
- --cfg models/yolov5s_solo.yaml
  * 
- --epochs  10
-  --data data/person.yaml
-  --epochs 20 
- --weights weights/yolov5s.pt
- --batch-size 2

```

python train.py --cfg models/yolov5s_solo.yaml --data data/person.yaml --weights weights/yolov5s.pt --epoch 20 --batch-size 2

```
|Epoch|GPU_mem|box_loss|obj_loss|cls_loss|Instances|Size|
 |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
 |训练周期数|当前GPU的内存使用情况|边界框|物体置信度|类别|训练批次中的实例数|图像的尺寸|  
 ***  
 2. val.py



 3. test.py

4. detect.py  
- parser.add_argument('--source', type=str, default=ROOT / 'data/video', help='file/dir/URL/glob/screen/0(webcam)')


### 存在的问题  
1. 制作数据集的时候，用的百度的Easydata的智能打标签实现的，存在较多误差，需要自行更改，
2. image和labels中的train、val、test名字必须一模一样，且为这个标准，模型内定的
3. 


***  
