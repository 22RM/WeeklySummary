## 关于C++中std的使用

> Author： ChenMiao  
> Date: 2023/11/10  
> brief：在C++中，`std`是一个很常见的东西，但是具体`std`是什么，我们需要详细讲解  

### std is what?  

在C++中，`std`无处不在，例如下面的一段最基础的`Hello world`代码  

```c++
#include <iostrea>

using namespace std;

int main() {
    cout << "Hello world" << endl;
    // std::cout << "Hello world" << std::endl;
    return 0;
}
```  

...解释`What`  

### why has std?  

在C++中，**由于C++的各种作用域庞大，因此为了解决冲突问题就出现了`name space(命名空间)`这一个概念。而`std`，就是其中最为主要的一个`命名空间`**  

...解释`Why`  

### Where is std happend  

```c++  
#include <iostrea>

using namespace std;

int main() {
    cout << "Hello world" << endl;
    // std::cout << "Hello world" << std::endl;
    return 0;
}
```

我们可以在上面的代码中看见，`std::`和`namespace std`无处不在，这是因为...  

...解释`Where`  

### How...  

...解释`How`  

### Summary...  

...  
