**问题**
   + **为什么数组是从0开始？**

         1.减少寻址时减法问题。下标表示偏移量，如果是1开始化，a[k]_address = base_address + (k-1)*type_size。

         2.历史原因：因为C语言作为高级语言的鼻祖，后来语言效仿是为了减少学习成本。实际上很多语言的数组并不是从零开始的比如Matlab，甚至还有一些语言支持负数下标，比如Python。

   + **如何实现随机访问？**

         1.是利用了存储的相对位置，来寻址定位数据即：a[k]_address = base_address + k * type_size

         2.数组支持随机访问，根据下标随机访问的时间复杂度为O(1),反之，如果不根据下标访问，即使是二分查找也是O(log n)

   + **更新为什么低效？有什么优化措施？**
   
      更新:
      
            指定下标更新时，时间复杂度为O(l),其他元素需要移动

      插入:

         1.时间复杂度：

         最好时间复杂度为O(1),插入尾部，不需要移动其他元素；

         最坏时间复杂度为O(n),插入头部，所有元素向后移动；

         平均时间复杂度为O(n),(1+2+3+...+n)/n=O(n),插入每个位置元素的概率是一样的


         2.优化 

         针对特殊情况，即无排序情况
         插入第K个位置时，将原来的数据移动到数组末尾，新数据直接放入该位置上，时间复杂度为O(1),不需要移动其他数据。

   + **访问越界有什么问题？如何避免**

         1.问题(以C语言为例)

         无限循环问题，原因C语言可以访问任意内存位置，除了受限内存之外。
         当数组越界时，修改的内存位置恰好是循环条件变量的地址，则有可能出现循环

         2.避免

         java编译器做了越界异常检查

         3.应用

         很多计算机病毒利用代码中数组越界可以访问非法地址的漏洞，来攻击系统。
   + **容器是否完全可以代替数组？为什么**
   
   1.ArrayList优势

         优势一：可以将许多数组操作细节封装起来。
         优势二：支持动态扩容。
         使用技巧：做好在创建的时候指定ArrayList数据大小，避免多次内存申请和数据搬移。

   2.ArrayList劣势

         劣势一：无法存储基本类型，需要Autoboxing and unboxing这些操作会有性能损耗。
         劣势二：如果简单操作数组，ArrayList重点的大部分方法使用不到。
         劣势三：多维数组时不直观。

   3.总结

         业务开发时直接使用容器类，省时省力。
         做底层开发时（比如开发网络框架），性能需要到极致，数组大于容器。

   + **二维数组的表示方法**
  
         对于 m * n 的数组，a [ i ][ j ] (i < m,j < n)的地址为：
         address = base_address + ( i * n + j) * type_size


**思考**

+ **越界引起的死循环1**
```
函数体内的局部变量存在栈上，且是连续压栈。在Linux进程的内存布局中，栈区在高地址空间，从高向低增长。
变量i和arr在相邻地址，且i比arr的地址大，所以arr越界正好访问到i。当然，前提是i和arr元素同类型，否则那段代码仍是未决行为。
```
+ **越界引起的死循环2**
```
例子中死循环的问题跟编译器分配内存和字节对齐有关 数组3个元素 加上一个变量a 。4个整数刚好能满足8字节对齐 所以i的地址恰好跟着a2后面 导致死循环
。如果数组本身有4个元素 则这里不会出现死循环。。因为编译器64位操作系统下 默认会进行8字节对齐 变量i的地址就不紧跟着数组后面了。
```
+ **越界引起的死循环3**
```
关于数组越界访问导致死循环的问题，我也动手实践了一下，发现结果和编译器的实现有关，gcc有一个编译选项（-fno-stack-protector）用于关闭堆栈保护功能。
默认情况下启动了堆栈保护，不管i声明在前还是在后，i都会在数组之后压栈，只会循环4次；如果关闭堆栈保护功能，则会出现死循环。请参考：https://www.ibm.com/developerworks/cn/linux/l-cn-gccstack/index.html
```
**实践**

windows C运行例子完成gcc
```
int main(int argc, char* argv[]){
    int i = 0;
    int arr[3] = {0};
    for(; i<=3; i++){
        arr[i] = 0;
        printf("hello world\n");
    }
    return 0;
}
```
[例子](../../algorithm/others/README.md) 
