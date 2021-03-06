# C语言标准库

## errno.h

errno.h里面有个全局变量`errno`，它可以把最后一次调用C的方法的错误代码保留。但是如果最后一次成功的调用C的方法，`errno`不会改变。因此，只有在C语言函数返回值异常时，再检测errno。

`errno`每个平台的实现都不一样，但是都会返回一个数字，每个数字代表一个错误类型。

### 如何转换errno的数字为相应的文字说明

1. 使用`strerrno`函数

```c
char *strerrno(int errno);
```

2. 使用`perror`函数

```c
void perror(const char* s);
```

`perror`用来将上一个函数发生错误的原因输出到标准错误`stderr`。
