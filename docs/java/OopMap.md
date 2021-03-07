# OopMap
OopMap，记录了在该类型的对象内什么偏移量上是什么类型的数据。
所以从对象开始向外的扫描可以是准确的
 
## 位置，
压缩存在内存，GC时才按需解压
HotSpot是用“解释式”的方式来使用OopMap的，每次都循环变量里面的项来扫描对应的偏移量。

## safePoint
每个被JIT编译过后的方法也会在一些特定的位置记录下OopMap，记录了执行到该方法的某条指令的时候，栈上和寄存器里哪些位置是引用。
这样GC在扫描栈的时候就会查询这些OopMap就知道哪里是引用了。

### 位置
这些特定的位置主要在： 
1、循环的末尾 
2、方法临返回前 / 调用方法的call指令后 
3、可能抛异常的位置

## JNI 有句柄表 不需要 OopMap
句柄表就可以得到所有从JNI方法能访问到的GC堆里的对象。 