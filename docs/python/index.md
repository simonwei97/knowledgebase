### 1. 函数参数 \*arg  和 \**kwargs 分别代表什么？

Python 中，函数的参数分为位置参数、可变参数、关键字参数、命名关键字参数。`#!python *args`代表可变参数，可以接收 0 个或任意多个参数，当不确定调用者会传入多少个位置参数时，就可以使用可变参数，它会将传入的参数打包成一个元组。`#!python **kwargs`代表关键字参数，可以接收用参数名=参数值的方式传入的参数，传入的参数的会打包成一个字典。定义函数时如果同时使用 `#!python *args`和`#!python **kwargs`，那么函数可以接收任意参数。

### 9. 什么是迭代器，为什么要使用它？

拥有 `#!python __iter__`、 `#!python __next__` 魔法方法的对象就是迭代器，保存的是获取数据的方式而不是结果，所以想用的时候就可以生成，节省内存空间，它是一个可以记住遍历的位置的对象。

### 10.什么是生成器，为什么要使用它？

生成器是特殊的迭代器，使用 yield 和 next 函数，有生成器表达式，生成器函数，都是为了节约内存。

### 11. 简述迭代器、生成器的区别？

生成器是迭代器，但使用方式不同，生成器函数使用 yield，还有生成器表达式，迭代器使用**next**进行元素迭代。

使用场景不同：在处理巨大的数据集合时，建议使用生成器来节省内存。在遍历容器对象时，使用迭代器很方便。

### 12. is 和 == 的区别？

- `#!python ==`判断是否相等
- `#!python is`不仅判断是否相等，还判断 **id 内存地址是否相等,两者都相等了才返回 True**

### 13. yield 和 return 的相同点和区别?

- 相同点：都是返回程序的执行结果；
- 区别：`#!python yield`返回结果但程序还没有结束，`#!python return`返回结果表示程序已经结束执行
