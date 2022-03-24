# 实现思路



## 1、问题描述

题目：有10个文件，每个文件有1000万行，文件内容的每一行为一个自然数；需要，写一个程序，将所有数字排序，分为10个文件输出，如0号文件包含前1000万个数字，1号文件文件包含第1千万-2千万之间的数字，依次类推。

限制：如果使用`java`，`-Xmx`需要设置为`32MB`；其它语言也需限制内存为`32MB`或说明在内存使用上如何达到限制的。

要求：正确输出 使用多线程加分



## 2、问题分析

计算机内存是有限的，当数据无法完全存入内存时，则无法使用正常的排序算法一次完成排序，而必须利用磁盘空间的辅助进行外部排序，即利用有限的内存每次读入部分数据排序后得到一个顺串后暂时放到磁盘，最后将多个顺串进行归并直到最终完成排序，因为在归并过程中，只需从每个顺串中取出最小的一个数据进行比较即可，而不需要整个顺串都在内存中，所以解决了内存空间不足的问题。那么，原问题就可以分解成两个子问题，一个是如何生成顺串，另一个是如何将顺串进行归并。



## 3、算法思路

- 外部排序

  先将10个大文件分成若干个有序的小文件。

  假设每个自然数的类型是`unsigned long long`，则一个自然数需要`8Byte`的空间。一个大文件中1000万个自然数需要`76.29MB`的空间存储，而内存只有`32MB`，所以采用快速排序，将一个大文件分成5个小文件，每个文件200万的自然数，快速排序所需的辅助空间为O(n)，所以在满足内存的情况下能得到5个有序的小文件。最终能得到50个有序的小文件，每个小文件200万个自然数。

- 多路归并

  从外部排序得到的50个有序的小文件中，每次读取每个文件中最小的数放入内存，找出50个数中的最小数，然后输出到对应目标文件，再从此最小数的源文件中继续读一个数进来，直至所有的数均输出完毕。

- 时间复杂度

  $O(nlogn)$

生成数据所用时间：

<img src="https://gitee.com/hailong-sun/pictures/raw/master/iCloud/image-20220324154730337.png" alt="生成数据的时间" style="zoom: 50%;" />

<img src="https://gitee.com/hailong-sun/pictures/raw/master/iCloud/image-20220324154837258.png" alt="数据" style="zoom:50%;" />



## 4、数据说明

```c
const int M = 1e7;//每个大文件包含无序的1kw个数
const int N = 2e6;//每个小文件包含有序的200w个数

vector<int> nums;//用于存放读入的自然数
int deviation[51];//记录每个小文件已被读取的数量

void extern_sort();//外部排序，分解大文件成有序小文件
int main();//归并排序

data.txt //10个生成的大文件
small.txt//50个生成的小文件
out.txt//输出的答案
```



## 5、样例分析

生成2个文件，每个文件为10行自然数：

<img src="https://gitee.com/hailong-sun/pictures/raw/master/iCloud/image-20220324160152145.png" alt="生成的数据" style="zoom:50%;" />

经过外部排序，将其分解为4个小文件，每个文件为5行有序的自然数：

<img src="https://gitee.com/hailong-sun/pictures/raw/master/iCloud/image-20220324160415004.png" alt="小文件" style="zoom:50%;" />

进行归并排序后得到最终答案：

<img src="https://gitee.com/hailong-sun/pictures/raw/master/iCloud/image-20220324160616149.png" alt="答案" style="zoom:50%;" />
