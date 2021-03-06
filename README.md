# LowTHelpEachOther
This repository just for LowT(Bob &amp; Tsingwong)

[toc]

# 2020

## 2020.04

### 2020.04.07

#### Bob: JAVA 中 Array 与 ArrayList 的差异
大部分语言（不包含 JavaScript）中的 Array 长度都是数组的属性之一，故不同长度的数组是不同类型的数组。

这一点的好处虽然她说不出来，但是缺点是给数组的增删造成了一定的困扰。

于是 ArrayLis 产生了，底层还是调用的 Array ，简单可以理解为随着数组长度的变化，当数组长度不够用的时候，ArrayList 会把当前底层的 Array 复制到另一个足够存放这些数据的 Array 中。

举个栗子，之前是长度为 2 的 ArrayList，存放了两个数字，这时候想在往里面存放一个数字时，ArrayList 的长度可能就会变成 4 ，以动态地满足其长度。

补充知识：
- 其他语言中也存在类似的，如 go 中的 array 和 slice
- ArrayList 的底层操作中存在一个赋值操作，即将变量名指向新数组的内存地址的操作；这个在数据中可以引申出：有哪些方法是改变数组本身，有哪些是不改变数组本身的？

#### Tsingwong: 词法作用域与动态作用域

作用域：可以理解为**引擎如何查找变量**以及**在何处找到变量**的规则。例如当前函数中需要有个参数 a，那么应该怎么查找这个 a，然后去哪里查找这个 a 呢？

大部分我们熟知的语言都是词法作用域（也被成为是静态作用域），有少部分语言使用的是动态作用域（Pascal、Emacs Lisp 等）

先来阐述下概念
- 词法作用域是词法变量在编译时静态确定的，所以它关注的是：**在何处定义声明的**
- 动态作用域是动态变量在运行时被动态确定的，所以它关注的是： **在何处被调用**，基于调用栈

举个栗子：

```javascript
function foo() {
  console.log( a );
}
function bar() {
  var a = 3;
	foo();
}
var a = 2;
bar();
```

这里是一段 JavaScript 代码。

假设 JavaScript 是词法作用域（当然不用假设，它就是），词法作用域关注声明在何处，`foo` 内部存在一个作用域，这里面显然查找不到 `a`，`foo` 声明在全局作用域，所以继续在全局作用域找到 `a = 2`，所以这里输出的结果是 2。

那么假设 JavaScript 是动态作用域（这次是真的假设了），动态作用域关注是调用位置，函数的调用栈是：`global -> bar -> foo`，那么同样的在 `foo` 内部查找不到 `a`，这时候会沿着调用栈查找，于是会在 `bar` 中找到 `a`，所以这里输出的结果是 3。

遮蔽效应：作用域查找到第一个匹配的变量后停止，内层的变量会“遮蔽”外层的同名变量。

### 2020.04.08

#### Bob：去除文件中重复行

```bash
cat xxx | sort |uniq >> xx_new
```

依次来讲解下：

##### cat

```bash
cat [-benstuv] [file ...]
```

concatenate files and print on the standard output（连接文件并打印到标准输出上）

常用参数：

- `-b`：对**非空行输出行号**，从 1开始计数
- `-e`: 一行的结束用 `$` 表示
- `-n`：**所有行输出行号**，从 1 开始计数
- `-s`：有连续两行以上的空白行，就代换为一行的空白行 

常见有三种用法：

1. 一次性显示整个文件： `cat -n xxx`
2. 创建一个文件：`cat > filename`
3. 将几个文件合并成一个文件：`cat flie1 file2 .... > file`



##### 管道符 |

将两个命令隔开，管道符左边命令的输出就会作为管道符右边命令的输入。连续使用管道意味着第一个命令的输出会作为第二个命令的输入，第二个命令的输出又会作为第三个命令的输入。



##### sort

```bash
sort [-bcCdfghiRMmnrsuVz] [-k field1[,field2]] [-S memsize] [-T dir]
          [-t char] [-o output] [file ...]
```



sort or merge records (lines) of text and binary files（对文本和二进制文件的记录(行)进行排序或合并）

常用参数：

- `-b`：忽略每行前面开始出的空格字符
- `-c`：只检查文件是否已排序，而不执行排序
- `-f`：忽略大小写比较
- `-m`：将几个排序好的文件合并
- `-n`：按照数值大小排序
- `-o<输出文件>`：将排序后的结果存入指定的文件，解决排序结果输出到原文件的问题，注：**使用 `>` 会导致原文件为空**
- `-r`：倒序排序
- `-R`：随机打乱后，排序。注：真正的随机重排要用 `shuf xxx`
- `-u`：对相同的行只保留一行。即 `sort|unqi` 等效于 `sort -u`
- `-t <分隔字符>`： 指定排序时所用的栏位分隔字符
- `-k`：指定列数从 1 开始
- `--parallel`：排序非常大量的資料，希望加速計算的速度的話

实用的指令

```bash
# 列出目前最耗 CPU 的 3 個行程
ps aux | sort -nrk 3,3 | head -n 3 | nl
```



##### uniq



```bash
uniq [-c | -d | -u] [-i] [-f num] [-s chars] [input_file [output_file]]
```



 report or filter out repeated lines in a file（查找或过滤文件中重复的行）

**注：该命令只过滤相邻的重复行．**

常用参数

- `-c`：在每列旁边显示该行的重复次数
- `-d`：仅显示重复出现的行列
- `-f<栏位>`：忽略指定栏
- `-s<字符位置>`：忽略指定字符
- `-w<字符位置>`：指定要比较的字符

##### >> 与 >

**输出/输入重导向**
`>    >>  <  <<  :>  &>  2&>  2<>>&  >&2`  

文件描述符(File Descriptor)，用一个数字（通常为0-9）来表示一个文件。
常用的文件描述符如下：

| 文件描述符 | 名称         | 常用缩写 | 默认值 |
| ---------- | ------------ | -------- | ------ |
| 0          | 标准输入     | stdin    | 键盘   |
| 1          | 标准输出     | stdout   | 屏幕   |
| 2          | 标准错误输出 | stderr   | 屏幕   |

我们在简单地用 < 或 > 时，相当于使用 `0<` 或 `1>`。
** cmd > file**

把 cmd 命令的输出重定向到文件 file 中。如果 file 已经存在，则清空原有文件，使用 bash 的 noclobber 选项可以防止复盖原有文件。

** cmd >> file**

把 cmd 命令的输出重定向到文件 file 中，如果 file 已经存在，则把信息加在原有文件后面。

** cmd < file**

使 cmd 命令从 file 读入

** cmd << text**

从命令行读取输入，直到一个与 text 相同的行结束。除非使用引号把输入括起来，此模式将对输入内容进行 shell变量替换。如果使用 <<-  ，则会忽略接下来输入行首的 tab ，结束行也可以是一堆 tab 再加上一个与 text 相同的内容。

** cmd <<< word**

把 word（而不是文件word）和后面的换行作为输入提供给 cmd。

** cmd <> file **

以读写模式把文件 file 重定向到输入，文件 file 不会被破坏。仅当应用程序利用了这一特性时，它才是有意义的。



**cmd >&n** 

把输出送到文件描述符 n



**cmd m>&n** 

把输出到文件符 m 的信息重定向到文件描述符 n

**cmd >&-** 

关闭标准输出

**cmd <&n** 

输入来自文件描述符 n

**cmd m<&n** 

m 来自文件描述各个 n

**cmd <&-**

 关闭标准输入



#### Tsingwong：移动零

 [move-zeroes](https://leetcode-cn.com/problems/move-zeroes/) 

这题的标签是：数组 、双指针。

方法大致有以下三种：

1. 暴力解题法：构建一个新数组，循环老数组，非 0 push 到新数组中，0 计数；新数组补 0；新老数组赋值（这一步经常会被忘记，要知道数组是引用类型）。时间复杂度 O(n)，空间复杂度 O(n)
2. 双指针补零：额外维护一个慢指针，循环数组，非 0 将慢指针处的变量置为快指针的变量且慢指针 + 1；数组补0 ，从慢指针当前位置到最后补 0 即可。时间复杂度O(n)，空间复杂度O(1)
3. 双指针交换：有点类似冒泡排序，冒泡排序的判断条件是大小，这里的判断条件是否等于 0。额外维护一个慢指针，循环数组，非 0 交换快慢指针处的变量即可。时间复杂度O(n)，空间复杂度O(1)



```javascript
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function(nums) {
	let tempnums = []
  let countZero = 0
  for(let i = 0; i < nums.length; i++) {
    if (nums[i] !== 0) {
      tempnums.push(nums[i])
    } else {
      countZero ++
    }
  }
  
  while(countZero--) {
    tempnums.push(0)
  }
  
  for(let i = 0; i< nums.length;i++) {
    nums[i] = tempnums[i]
  }
};
```

```javascript
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function(nums) {
	let pointerZero = 0
  for(let i = 0; i < nums.length;i++) {
    if (nums[i] !== 0) {
      nums[pointerZero++] = nums[i] 
    }
  }
  
  for(;pointerZero <nums.length;pointerZero++) {
    nums[pointerZero] = 0
  }
};
```

```javascript
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function(nums) {
	let pointerZero = 0
  for(let i = 0; i < nums.length;i++) {
    if (nums[i] !== 0) {
      [nums[pointerZero++],nums[i] ] = [nums[i],nums[pointerZero] ]
    }
  }
};
```

### 2020.04.14 

#### Tsingwong：三数之和

[3sum](https://leetcode-cn.com/problems/3sum/)

标签：数组、双指针

思路：遇事不决先排序，双指针夹壁就是刚。k 固定，i 为 k +1, j 为数组最后一个值，逐渐夹壁。需要特殊处理的是相同值。

```javascript
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
var threeSum = function(nums) {
  let res = []
  let sortNums = nums.sort((a, b) => a - b)
  for(let k = 0; k < nums.length -2; k++ ) {
    let i = k + 1
    let j = nums.length - 1
    while(i < j) {
      if(nums[k] + nums[i] +nums[j] === 0) {
        res.push([nums[k], nums[i], nums[j]])
        while (i < j && nums[i] ===nums[i+1]) {
          i++
        }
        while (i < j && nums[j] ===nums[j-1]) {
          j--
        }
        i++
        j--
      } else if (nums[k] + nums[i] +nums[j] > 0) {
        j--
      } else {
        i++
      }
    }
    while(k < nums.length-2 && nums[k] === nums[k+1]) {
      k = k+1
    }
  }
  return res
};
```



### 2020.04.15

#### Tsingwong：值传递和引用传递

```javascript
function foo(x) {
  x.push(4)
  x // [1,2,3,4]
  
  x = [4,5,6]
  x.push(7)
  x // [4,5,6,7]
}
let a = [1,2,3]
foo(a)
a // [1,2,3,4] 而不是 [4,5,6,7]
```

这是一道经典面试题目

简单值（即基本类型值）总是通过 **值复制**的方式来赋值/传递，包含 null、undefined、字符串、数字、布尔值和ES6 中的 symbol。

复合值--对象（包括数组和封装对象）和函数，则总是通过 **引用复制** 的方式来赋值/传递。

由于引用指向的是值本身而非变量，所以一个引用无法改变另一个引用的指向。

需要注意数组有些方法是返回新数组的：

- concat()---连接两个或更多的数组，并返回结果
- every()---检测数组元素的每个元素是否都符合条件。
- some()---检测数组元素中是否有元素符合指定条件。

- filter()---检测数组元素，并返回符合条件所有元素的数组。

- indexOf()---搜索数组中的元素，并返回它所在的位置。

- join()---把数组的所有元素放入一个字符串。

- toString()---把数组转换为字符串，并返回结果。

- lastIndexOf()---返回一个指定的字符串值最后出现的位置，在一个字符串中的指定位置从后向前搜索。

- map()---通过指定函数处理数组的每个元素，并返回处理后的数组。

- slice()---选取数组的的一部分，并返回一个新数组。

- valueOf()---返回数组对象的原始值。



会改变原来数组的有：

- pop()---删除数组的最后一个元素并返回删除的元素。

- push()---向数组的末尾添加一个或更多元素，并返回新的长度。

- shift()---删除并返回数组的第一个元素。

- unshift()---向数组的开头添加一个或更多元素，并返回新的长度。

- reverse()---反转数组的元素顺序。

- sort()---对数组的元素进行排序。

- splice()---用于插入、删除或替换数组的元素。



### 2020.07.20

#### Tsingwong：Linux统计文件夹下的文件数目

首先介绍下几个 linux 命令 `ls`、`grep`、`wc`。

- `ls` 多用于列出当前文件夹下面的文件和文件夹信息。

- `grep` 管道符，其输入为前面命令的输出
- `wc` 统计标准输出的数据

进一步介绍：

- `ls -l`：长列表输出该目录下文件信息(注意这里的文件是指目录、链接、设备文件等)，每一行对应一个文件或目录，`ls -lR`是列出所有文件，包括子目录。

- `grep "^-"`：过滤`ls`的输出信息，只保留一般文件，只保留目录是`grep "^d"`。
- `wc -l`：·统计输出信息的行数，统计结果就是输出信息的行数，一行信息对应一个文件，所以就是文件的个数。

##### 统计当前目录下文件的个数（不包括目录）

```bash
ls -l | grep "^-" | wc -l
```

##### 统计当前目录下文件的个数（包括子目录）

```bash
ls -lR| grep "^-" | wc -l
```

##### 统计目录下文件夹(目录)的个数（包括子目录）

```bash
ls -lR | grep "^d" | wc -l
```

##### 统计当前目录下文件夹个数（不包括子目录）

```bash
ls -l | grep "^d" | wc -l
```

