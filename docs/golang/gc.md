---
hide:
  - footer
---

## GC

GC，全称 Garbage Collection，即垃圾回收，是一种自动内存管理的机制。


## STW 是什么意思？ 

STW 可以是 Stop the World 的缩写，也可以是 Start the World 的缩写。通常意义上指指代从 Stop the World 这一动作发生时到 Start the World 这一动作发生时这一段时间间隔，即万物静止。STW 在垃圾回收过程中为了保证实现的正确性、防止无止境的内存增长等问题而不可避免的需要停止赋值器进一步操作对象图的一段过程。

