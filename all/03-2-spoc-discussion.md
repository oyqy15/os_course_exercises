# lec6 SPOC思考题


NOTICE
- 有"w3l2"标记的题是助教要提交到学堂在线上的。
- 有"w3l2"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。

## 与视频相关思考题

### 6.1	非连续内存分配的需求背景
 1. 为什么要设计非连续内存分配机制？

 连续内存存在各种缺陷，动态修改困难，内存利用率低。非连续内存分配机制是为了提高内存管理的灵活性以及利用效率。

 当程序需要一段内存，但内存中没有满足大小需求的连续区域时，分配就会失败。这个时候，就需要非连续的分配方案，能把不连续的内存都用上来满足空间需求。


 1. 非连续内存分配中内存分块大小有哪些可能的选择？大小与大小是否可变?
 
 段式和页式，不固定和固定。大块和小块。
 
 

 1. 为什么在大块时要设计大小可变，而在小块时要设计成固定大小？小块时的固定大小可以提供多种选择吗？

 大小可变让大块更加灵活，易于分配。
 
 小块作为分配的基本单位，固定大小易于管理。

### 6.2	段式存储管理
 1. 什么是段、段基址和段内偏移？

 段表示**访问方式**和**存储数据**等属性相同的一段地址空间。
 
 段基址：段在物理内存中的起始地址
 
 段内偏移：数据单元在段内相对于起始地址的位置


 1. 段式存储管理机制的地址转换流程是什么？为什么在段式存储管理中，各段的存储位置可以不连续？这种做法有什么好处和麻烦？

 step1.根据段号在段表里查到段描述符
 
 step2.根据段描述符找到段基址
 
 step3.将虚拟地址的段内偏移加上段基址得到最终的物理地址
 
 段和段之间的位置不会有影响。一个段的位置不会影响另一个段的访问，所以可以不连续。
 
 好处是大小不固定，不连续。
 
 麻烦：地址转换，段表的维护。段在物理内存中的维护。


### 6.3	页式存储管理
 1. 什么是页（page）、帧（frame）、页表（page table）、存储管理单元（MMU）、快表（TLB, Translation Lookaside Buffer）和高速缓存（cache）？

 页是虚拟内存的一个基本内存块
 
 帧是物理内存的一个基本内存块
 
 页表是记录页号和帧号对应关系的一个表格
 
 mmu是运行页地址转换到虚地址这个功能的程序
 
 cache：cpu中的对部分内存的临时存储
 
 tlb：cpu中对部分页表的临时存储
 
 
 1. 页式存储管理机制的地址转换流程是什么？为什么在页式存储管理中，各页的存储位置可以不连续？这种做法有什么好处和麻烦？

 找到页表项
 
 通过页表项找到帧号
 
 然后通过帧内偏移和帧的起始地址加起来可以得到物理地址
 
 程序中的虚拟地址空间是连续的，物理内存中的帧地址是由虚拟地址转换而来的，存储位置并没有要求，于是可以不连续。
 
 好处：可以不连续，大小固定，易于管理
 坏处：访问慢


### 6.4	页表概述
 1. 每个页表项有些什么内容？有哪些标志位？它们起什么作用？
 
 对应帧的起始物理地址
 存在位：表示是否有对应的物理帧
 修改位：表示对应的页面是否被修改了
 引用：在过去一段时间内是否有对该物理页帧的访问，引用
 
 
 
 1. 页表大小受哪些因素影响？

 进程数
 内存空间
 页面大小

### 6.5	快表和多级页表
 1. 快表（TLB）与高速缓存（cache）有什么不同？
 
 tlb的用处是完成虚拟页地址到物理帧地址的映射
 
 cache的用处是完成内存地址到物理内存单元中数据的映射
 
 1. 为什么快表中查找物理地址的速度非常快？它是如何实现的？为什么它的的容量很小？
 
 快表是将近期使用的页表项缓存在cpu中，使用关联存储实现。
 
 受限于cpu的制作，cpu内部没法提供很大的存储空间
 
 1. 什么是多级页表？多级页表中的地址转换流程是什么？多级页表有什么好处和麻烦？


### 6.6	反置页表
 1. 页寄存器机制的地址转换流程是什么？
 
 每个帧与一个页寄存器关联
 
 逻辑页表作地址转换
 
 使用位，页号，保护位（可读可写）
 
 1. 反置页表机制的地址转换流程是什么？

 逻辑地址和进程号共同做hash去搜索反置页表项
 
 1. 反置页表项有些什么内容？

 pid，逻辑页号，标志位

### 6.7	段页式存储管理
 1. 段页式存储管理机制的地址转换流程是什么？这种做法有什么好处和麻烦？

 段->页
 
 段式存储在内存保护里有优势
 
 页式存储在内存利用和优化转移到后备存储方面有优势
 
 这种做法可以方便的实现内存共享
 
 但是会引入页表较大，查询较慢的问题
 
 
 1. 如何实现基于段式存储管理的内存共享？

不同进程指向共享段表
 
 1. 如何实现基于页式存储管理的内存共享？

不同进程指向共享页表

## 个人思考题
（1） (w3l2) 请简要分析64bit CPU体系结构下的分页机制是如何实现的



## 小组思考题
（1）(spoc) 某系统使用请求分页存储管理，若页在内存中，满足一个内存请求需要150ns (10^-9s)。若缺页率是10%，为使有效访问时间达到0.5us(10^-6s),求不在内存的页面的平均访问时间。请给出计算步骤。



（2）(spoc) 有一台假想的计算机，页大小（page size）为32 Bytes，支持32KB的虚拟地址空间（virtual address space）,有4KB的物理内存空间（physical memory），采用二级页表，一个页目录项（page directory entry ，PDE）大小为1 Byte,一个页表项（page-table entries
PTEs）大小为1 Byte，1个页目录表大小为32 Bytes，1个页表大小为32 Bytes。页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐）。

PTE格式（8 bit） :
```
  VALID | PFN6 ... PFN0
```
PDE格式（8 bit） :
```
  VALID | PT6 ... PT0
```
其
```
VALID==1表示，表示映射存在；VALID==0表示，表示映射不存在。
PFN6..0:页帧号
PT6..0:页表的物理基址>>5
```
在[物理内存模拟数据文件](./03-2-spoc-testdata.md)中，给出了4KB物理内存空间的值，请回答下列虚地址是否有合法对应的物理内存，请给出对应的pde index, pde contents, pte index, pte contents。
```
1) Virtual Address 6c74
   Virtual Address 6b22
2) Virtual Address 03df
   Virtual Address 69dc
3) Virtual Address 317a
   Virtual Address 4546
4) Virtual Address 2c03
   Virtual Address 7fd7
5) Virtual Address 390e
   Virtual Address 748b
```

比如答案可以如下表示： (注意：下面的结果是错的，你需要关注的是如何表示)
```
Virtual Address 7570:
  --> pde index:0x1d  pde contents:(valid 1, pfn 0x33)
    --> pte index:0xb  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)

Virtual Address 21e1:
  --> pde index:0x8  pde contents:(valid 0, pfn 0x7f)
      --> Fault (page directory entry not valid)

Virtual Address 7268:
  --> pde index:0x1c  pde contents:(valid 1, pfn 0x5e)
    --> pte index:0x13  pte contents:(valid 1, pfn 0x65)
      --> Translates to Physical Address 0xca8 --> Value: 16
```

[链接](https://piazza.com/class/i5j09fnsl7k5x0?cid=664)有上面链接的参考答案。请比较你的结果与参考答案是否一致。如果不一致，请说明原因。

（3）请基于你对原理课二级页表的理解，并参考Lab2建页表的过程，设计一个应用程序（可基于python、ruby、C、C++、LISP、JavaScript等）可模拟实现(2)题中描述的抽象OS，可正确完成二级页表转换。

[链接](https://piazza.com/class/i5j09fnsl7k5x0?cid=664)有上面链接的参考答案。请比较你的结果与参考答案是否一致。如果不一致，提交你的实现，并说明区别。

（4）假设你有一台支持[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)的机器，请问你如何设计操作系统支持这种类型计算机？请给出设计方案。

 (5)[X86的页面结构](http://os.cs.tsinghua.edu.cn/oscourse/OS2018spring/lecture06)
---

## 扩展思考题

阅读64bit IBM Powerpc CPU架构是如何实现[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)，给出分析报告。


## interactive　understand VM

[Virtual Memory with 256 Bytes of RAM](http://blog.robertelder.org/virtual-memory-with-256-bytes-of-ram/)：这是一个只有256字节内存的一个极小计算机系统。按作者的[特征描述](https://github.com/RobertElderSoftware/recc#what-can-this-project-do)，它具备如下的功能。
 - CPU的实现代码不多于500行；
 - 支持14条指令、进程切换、虚拟存储和中断；
 - 用C实现了一个小的操作系统微内核可以在这个CPU上正常运行；
 - 实现了一个ANSI C89编译器，可生成在该CPU上运行代码；
 - 该编译器支持链接功能；
 - 用C89, Python, Java, Javascript这4种语言实现了该CPU的模拟器；
 - 支持交叉编译；
 - 所有这些只依赖标准C库。
 
针对op-cpu的特征描述，请同学们通过代码阅读和执行对自己有兴趣的部分进行分析，给出你的分析结果和评价。
