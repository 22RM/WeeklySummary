> Author: 韩栋林
>
> data: 2023/12/1
>
> brief: 简单了解了一下多线程，还未深入研究

# Python进程与线程的学习

## 多任务介绍

所谓多任务就是在同一时间内执行多个任务。

我们之前所写的程序在我们调试过程中就可以看到，它是一个一个执行的，也就是说一个函数或者方法执行完成后，另一个函数或者方法才能执行，要想实现多个函数同时执行就需要多任务。

多任务的最好好处就是**充分利用CPU资源，提高程序的执行效率**。

**多任务的两种表现形式**

- 并发

![image-20231128224422290](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202312012340195.png)	

- 并行

![并行](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202312012341896.png)

## 进程的学习

![进程](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202312012341483.png)

### **多进程完成多任务**

![image-20231129145555140](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311291456235.png)

> 代码示例

```python
import multiprocessing
import time


def coding():
    for i in range(3):
        print('coding...')
        time.sleep(0.2)


def music():
    for i in range(3):
        print('music...')
        time.sleep(0.2)


if __name__ == '__main__':
    # 通过进程类创建进程对象
    coding_process = multiprocessing.Process(target=coding)
    music_process = multiprocessing.Process(target=music)
    # 启动进程
    coding_process.start()
    music_process.start()

```

### **进程执行带有参数的任务**

![image-20231129150745133](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311291507181.png)

```python
	# args元组传参,要保证参数顺序正确
    coding_process = multiprocessing.Process(target=coding, args=(3, 'hdl'))
    # kwargs字典传参
    music_process = multiprocessing.Process(target=music, kwargs={'count': 2})
```

### 获取进程编号

![image-20231129152135619](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311291521662.png)

### 进程间不共享全局变量

![image-20231129153616021](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311291536121.png)

![image-20231129153728962](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311291537014.png)

代码示例

```python
import multiprocessing
import time
# 全局变量
my_list = []


def write_data():
    for i in range(3):
        my_list.append(i)
        print('add:', i)
    print('write_data', my_list)


def read_data():
    print('read_data', my_list)


if __name__ == '__main__':
    # 创建写入数据进程
    write_process = multiprocessing.Process(target=write_data)
    # 创建读取数据进程
    read_process = multiprocessing.Process(target=read_data)

    write_process.start()
    time.sleep(1)
    read_process.start()
    
# 输出结果如下
add: 0
add: 1
add: 2
write_data [0, 1, 2]
read_data []
```

### 主进程和子进程的结束顺序

主进程会等待所有子进程执行结束再结束

```python
import multiprocessing
import time


# 工作函数
def work():
    for i in range(10):
        print('工作中...')
        time.sleep(0.2)


if __name__ == '__main__':
    # 创建子进程
    work_process = multiprocessing.Process(target=work)
    # 启动子进程
    work_process.start()

    # 延时1s
    time.sleep(1)
    print('主进程结束')
    
# 运行结果如下
工作中...
工作中...
工作中...
工作中...
工作中...
主进程结束
工作中...
工作中...
工作中...
工作中...
工作中...

```

可以看到虽然已经打印了主进程结束，但是此时work_process这个进程还在运行，因此主进程会等待子进程结束后再结束

为了保证主进程退出后子进程直接销毁，不再执行子进程中的代码，就需要设置守护主进程。

修改部分代码

```python
# 创建子进程
work_process = multiprocessing.Process(target=work)
# 设置守护主进程，主进程退出后子进程直接销毁，不再执行子进程中的代码
work_process.daemon = True
# 启动子进程
work_process.start()

# 延时1s
time.sleep(1)
# 第二种方式 直接销毁子进程
# work_process.terminate()
print('主进程结束')

# 运行结果如下
工作中...
工作中...
工作中...
工作中...
工作中...
主进程结束

可以看到主进程结束后，子进程直接被销毁
```

## 线程的学习

### 线程的概念与为什么使用多线程

![image-20231129184350103](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311291843181.png)

### 多线程的作用

![image-20231129184525615](https://cdn.jsdelivr.net/gh/cabbageB/images@main/images/202311291845699.png)

**总结**

1. 多线程是python程序中实现多任务的一种方式。
2. 线程是程序执行的最小单位，进程是分配资源的最小单位。
3. 同属一个进程的多个线程共享进程所拥有的全部资源。

### 多线程完成多任务

> 线程完成多任务示例

```python
# 导入线程模块
import threading
import time


def coding():
    for i in range(3):
        print('coding...')
        time.sleep(0.2)


def music():
    for i in range(3):
        print('music...')
        time.sleep(0.2)


if __name__ == '__main__':
    # 创建子线程
    coding_thread = threading.Thread(target=coding)
    music_thread = threading.Thread(target=music)

    # 启动子线程执行任务
    coding_thread.start()
    music_thread.start()
```

