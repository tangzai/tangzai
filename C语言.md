# C语言

## 1. 第一个C语言程序

### 1.1 Hello World

```c
// 导入一个文件 stdio.h htd 是一个标准库，std(standard), i(input), o(output)
// <> 表示导入系统文件， "" 表示导入自定义文件
#include <stdio.h>

// int 整形，如果出现在函数中，表示函数的返回值
// main 表示程序的主函数，程序有且只有一个主函数
int main(){
    // {} 函数体 代码体
    // printf 是 stdio.h 中系统提供的函数 表示在标准输出设备上打印字符串
    printf("hello world");
    // return 如果出现在其他函数中表示函数结束 如果出现在main函数中表示程序结束
    // 0 表示函数的返回值 要和函数返回值类型对应
    return 0;
}
```

### 1.2 使用gcc编译程序

默认情况下，c语言编写的文件后缀是`.c`

```
gcc -o <文件输出路径> <源文件>

C:\Users\19374\Desktop\CTF>gcc -o hello.exe hello.c
```

### 1.3 system 函数

```c
#include <stdlib.h>             // 导入带有 system 函数的头文件
#include <stdio.h>

int main(){
    int result = 0;
    result = system("dir");      // 执行成功返回0，失败返回任意数字；自带输出
    printf("%d\n", result);
    return 0;
}
```

### 1.4 C程序编译过程

#### 1.4.1 C程序编译步骤

1. 预编译：宏定义展开（将常量赋值到所有调用它的地方）、头文件展开（将头文件的内容复制到源文件中）、条件编译等，同时将代码中的注释删除，这里并不会检查语法
2. 编译：检查语法，将预处理后文件编译生成汇编文件
3. 汇编：将汇编文件生成目标文件（二进制文件）
4. 链接：C语言写的程序时需要依赖各种库的，所以编译周会还需要把库连接到最终的可执行程序去

#### 1.4.2 gcc编译过程

**分步编译**

```
预处理：gcc -E hello.c -o hello.i
编译：gcc -S hello.i -o hello.s
汇编：gcc -c hello.s -o hello.o
链接：gcc hello.o -o hello
```

## 2. 数据类型

### 2.1 C语言数据类型分类

![image-20240428235927122](C%E8%AF%AD%E8%A8%80.assets/image-20240428235927122.png)

### 2.2 常量和变量

```c
#include <stdio.h>
#define MAX 100         // 注意：没有分号结尾的（常量）

int main() {
    const int price = 3;		// 定义常量
    float weight = 6.666;

    float sum = price * weight;
    // 占位符 %f 表示输出一个浮点型 float 默认保留6位小数 会四舍五入
    // 占位符 %.2f 表示只保留两位小数 会四舍五入
    printf("%.2f", sum);
    printf("宏定义常量：%d", MAX);
    return 0;
}
```

### 2.3 整形

#### 2.3.1 整形变量的定义和输出

**注意：**C语言不能直接书写二进制

```c
#include <stdio.h>

int main() {
    int a = 100;

    printf("%d\n", a);                 // 100
    printf("8进制：%o\n", a);           // 8进制：144
    printf("十六进制：%x\n", a);         // 十六进制：64
    printf("十六进制：%X\n", a);         // 十六进制：64
    printf("无符号数：%u\n", a);         // 无符号数：100

    int b = 0123;                           // 定义八进制值
    int c = 0xabc;                          // 定义十六进制值
    return 0;
}
```

#### 2.3.2 整形输入

```c
#include <stdio.h>

int main() {
    int a = 0;
    printf("输入一个数并打印：");
    // &运算符 表示取地址运算符
    scanf("%d", &a);
    printf("%d", a);

    return 0;
}
```

#### 2.3.3 整形大小和取值范围

```c
#include <stdio.h>

int main() {
    int a = 10;
    short b = 20;
    long c = 30;
    long long d = 40;

    printf("%d\n", a);
    printf("%hd\n", b);
    printf("%ld\n", c);
    printf("%lld", d);

    return 0;
}
```

### 2.4 Sizeof 关键字

计算类型在内存中占用的字节大小

```c
#include <stdio.h>

int main() {
    printf("%d\n", sizeof (int ));     // 4
    printf("%d\n", sizeof (short ));     // 2
    printf("%d\n", sizeof (long ));     // 4
    printf("%d\n", sizeof (long long ));     // 8
    return 0;
}
```

### 2.5 字符型

字符型变量只能用于存储一个**单一**的字符

字符串变量实际上并不是把改字符本身放到变量内存单元中去，而是**将该字符对应的ASCII编码放到变量的存储单元中**。**char的本质就是一个1字节大小的整形**

```c
#include <stdio.h>

int main() {
    char ch = 'a';
    printf("%c\n", ch);              // a
    printf("%d\n", ch - 32);        // 65
    printf("%c\n", ch - 32);        // A
    printf("%d", sizeof ch);        // 1
    return 0;
}
```

### 2.6 浮点型

实型变量也可以称为浮点型变量，浮点型变量时用来存储小数数值的。在C语言中，浮点型变量分为两种：单精度浮点型（float0）、双精度浮点型（double），但是double型变量所表示的浮点数比float型变量更精确

由于浮点型变量是由有限的存储单元组成的，因此只能提供有限的有效数字。在有效位意外的数字将被舍去，这样可能产生一些误差

不以f结尾的常量时double类型，以f结尾的常量是（如3.14f）是float类型

```c
#include <stdio.h>

int main() {
    float a = 3.14f;
    double b = 3.14;

    printf("a=%f, b=%lf\n", a, b);							\\ a=3.140000, b=3.140000
    printf("size_a=%d, size_b=$d", sizeof(a), sizeof (b));		\\ size_a=4, size_b=$d
    return 0;
}
```

## 3. 计算机内存数值存储方式

### 3.1 原码、反码、补码

**正数的原码反码和补码都是它本身；**

**负数的反码符号位不变，其他位逐位取反**

**负数的补码在反码的基础上+1**

```
# 由于 char 在内存中占位位一个字节，即char 10 可用 8bit表示，且第一位为符号位
# 符号位0表示正数，1表示负数

char 10
原码: 0000 1010
反码: 0000 1010
补码: 0000 1010

char -10
原码: 1000 1010
反码: 1111 0101
补码: 1111 0110
```

**在计算机中，数值一律用补码存储**，即`char 10`在计算机的内存中为`0a`，`char -10`在计算机内存中的表现形式为：`f6`

![image-20240429175056397](C%E8%AF%AD%E8%A8%80.assets/image-20240429175056397.png)

![image-20240429175108711](C%E8%AF%AD%E8%A8%80.assets/image-20240429175108711.png)

在计算机系统中，数值一律用补码来存储，主要原因是：

+ 同一了零的编码
+ 将符号位和其他位同一处理
+ 将减法运算转变位加法运算
+ 两个用补码表示的数相加时，如果最高位（符号位）有进位，则进位被舍弃

```
# 计算机 76 - 32 的运算，由于 76-32=76+(-32)，且计算机没有减法，所以这里需要计算-32的补码
76原码：0100 1100
76反码：0100 1100
76补码：0100 1100

-32原码：1010 0000
-32反码：1101 1111
-32补码：1110 0000

0100 1100
+
1110 0000
=
1 0010 1100 补码  符号位溢出位正数，得：44

同理计算：76-82
76原码：0100 1100
76反码：0100 1100
76补码：0100 1100

-82原码：1101 0010
-82反码：1010 1101
-82补码：1010 1110

0100 1100
+
1010 1110
=
1111 1010 补码	由于是负数，所以需要转回补码
1111 1001 反码
1000 0110 补码    得：-6
```

### 3.2 各个类型的取值范围

以char为例：之所以负数比正数的取值范围多一位是因为`1000 0000 `直接定义为-128

以char为例：`unsigned`由于没有负数，所以也没有符号位，那么取值范围为`2^8`

| 数据类型       | 占用空间 | 取值范围                                    |
| -------------- | -------- | ------------------------------------------- |
| short          | 2字节    | -32768 到 32767 （-2^18 ~ 2^18 - 1）        |
| int            | 4字节    | -2147483648 到 2147483647（-2^31 ~ 2^31-1） |
| long           | 4字节    | -2147483648 到 2147483647（-2^31 ~ 2^31-1） |
| unsigned short | 2字节    | 0 到 65535（0 ~ 2^16-1）                    |
| unsigned int   | 4字节    | 0 到 4294967295（0 ~ 2^32-1）               |
| unsigned long  | 4字节    | 0 到 4294967295（0 ~ 2^32-1）               |

### 3.3 数值溢出

当超过一个数据类型能够存放最大的范围时，数值会溢出

有符号位最高位溢出的区别：符号位溢出会导致数的正负发生改变，但最高数的溢出会导致最高位丢失

```
# 以char为例，char占用的空间位1字节（8bit），那么现在有a=127，b=2，a+b=-127，推理如下：
127补码：0111 1111
2补码：  0000 0010

127+2补码：1000 0001
127+2反码：1000 0000
127+2原码：1111 1111 = -127
```

```c
#include <stdio.h>

int main() {
    char a = 127;
    a = a + 2;

    printf("%d", a);		// -127
    return 0;
}
```

## 4. 字符串格式化输入和输出

### 4.1 字符串常量

+ 字符串时内存中一段连续的char空间，以`\0`（数字0）结尾

+ 字符串常量是由双**引号括**起来的字符序列，如"chaina","C"都是合法的字符串常量

```
字符常量：'a'
字符串常量："a\0"
```

每个字符串的结尾，编译器会自动添加一个结束标志位`\0`，即`"a"`包含两个字符`a`和`\0`

```c
#include <stdio.h>

int main() {
    char a = 'a';       // 字符常量
    char a = "a";       // 报错
    return 0;
}
```

### 4.2 printf函数与getchar函数

**常用输出控制符**

| 转换说明 | 输出                                                         |
| -------- | ------------------------------------------------------------ |
| %a       | 浮点数、十六进制数和p记数法(C99/C11)                         |
| %A       | 浮点数、十六进制数和p记数法(C99/C11)                         |
| %c       | 单个字符                                                     |
| %d       | 有符号十进制整数                                             |
| %e       | 浮点数，e记数法                                              |
| %E       | 浮点数，e记数法                                              |
| %f       | 浮点数，十进制记数法                                         |
| %g       | 根据值的不同，自动选择%f或%e。%e格式用于指数小于-4或者大于或等于精度时 |
| %G       | 根据值的不同，自动选择%F或%E。%E格式用于指数小于-4或者大于或等于精度时 |
| %i       | 有符号十进制整数（与%d相同）                                 |
| %o       | 无符号八进制整数                                             |
| %p       | 指针                                                         |
| %s       | 字符串                                                       |
| %u       | 无符号十进制整数                                             |
| %x       | 无符号十六进制整数，使用十六进制0f                           |
| %X       | 无符号十六进制整数，使用十六进制0F                           |
| %%       | 打印一个百分号                                               |

**printf()中的标记**

| 修饰符 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| -      | 待打印项左对齐。即，从字段的左侧开始打印该项。示例：“%-10s”  |
| +      | 有符号值若为正，则在值前面显示加号；若为负，则在值前面显示减号。示例：“%+6.2f” |
| 空格   | 空格	有符号值若为正，则在值前面显示前导空格（不显示任何字符）；若为负，则在值前面显示减号+标记覆盖一个空格。示例：“%6.2f” |
| #      | 把结果转化成另一种形式。如果是%o格式，则以0开始；如果是%x或%X格式，则以0x或0X开始；对于所有的浮点数格式，#保证了即使后面没有任何数字，也打印一个小数点字符。对于%g和%G格式，#防止结果后面的0被删除。示例：“%#o”、“%8.0f”、“%+#10.3e” |
| 0      | 对于数值格式，用前导0代替空格填充字段宽度。对于整数格式，如果出现-标记或指定精度，则忽略该标记 |

**printf附加格式**

| 字符                | 含义                                                         |
| ------------------- | ------------------------------------------------------------ |
| 1（字母1）          | 附加在d,u,x,o签前面，表示长整数                              |
| -                   | 左对齐                                                       |
| m（代表一个整数）   | 数据最小宽度                                                 |
| 0（数字0）          | 将输出的前面补上0知道占满指定列宽为止，不可以搭配使用-       |
| m,n（代表一个整数） | m指定域宽，即对应的输出项在输出设备上所占的字符数，n值精度，用于说明输出的实型数的小数位数。对数值型的来说，未指定n时，隐含的精度为n=6位 |

### 4.3 putchar和getchar函数

```c
#include <stdio.h>

int main() {
    char ch = 'a';
    putchar(ch);			// 输出一个字符
    char ch2 = getchar();   // 接收一个字符
    putchar(ch2);			
    return 0;
}
```

## 5. 类型转换

隐式转换：遵循一定的规则，由编译系统自动完成（自动四舍五入）

强制类型转换：把表达式的运算结果前置转换为许哦徐的数据类型（不做四舍五入）

类型转换原则：占用内存字节数少（值域小）的类型，像占用内存字节数多（值域大）的类型转换，以保证精度不降低

![image-20240429223650965](C%E8%AF%AD%E8%A8%80.assets/image-20240429223650965.png)

```c
#include <stdio.h>

int main() {
    float a = 3.14f;
    printf("%d", (int)a);       // 3
    return 0;
}
```

## 6. 程序流程结构

### 6.1 if语句

```
if(条件表达式1){}else if(条件表达式2)else{}
```

```c
#include <stdio.h>

int main() {
    int score = 80;
    if (score > 90){
        printf("优秀");
    } else if(score < 90 && score > 60){
        printf("良好");
    } else{
        printf("不及格");
    }
    return 0;
}
```

### 6.2 三目运算符

```
条件表达式 ? True : False
```

```c
#include <stdio.h>

int main() {
    int num1 = 80;
    int num2 = 60;
    int result = num1 > num2 ? 1 : 0;
    printf("%d", result);

    return 0;
}
```

### 6.3 switch 语句

```c
#include <stdio.h>

int main() {
    int choice = 1;;

    switch (choice) {
        case 1:
            printf("选择1");
            break;
        case 2:
            printf("选择2");
            break;
        default:
            printf("没有选择");
            break;
    }
    return 0;
}
```

### 6.4 while循环语句

```c
#include <stdio.h>

int main() {
    int i = 1;

    while (i <= 10){
        printf("%d\n", i);
        i++;
    }
    return 0;
}
```

### 6.5 do...while...

```c
#include <stdio.h>

int main() {
    int i = 1;

    do {
        printf("%d\n", i);
        i++;
    } while (i <= 10);
    return 0;
}
```

### 6.6 for循环

```c
#include <stdio.h>

int main() {
    for (int i = 0; i < 10; ++i) {
        printf("%d\n", i);
    }
    return 0;
}
```

### 6.7 跳转语句goto、break、continue

这里只介绍goto

```c
#include <stdio.h>

int main() {
    printf("1");
    goto FLAG;
    printf("2");
    printf("3");
    printf("4");
    FLAG:
    printf("5");
    printf("6");
    return 0;
}

# 输出：
1
5
6
```

## 7. 数组和字符串

### 7.1 数组的定义和使用

**数组就是在内存中连续的相同类型的变量空间**。同一个数组所有的成员都是相同的数据类型，同时所有的成员在呢次中的地址是连续的

```
数组在内存中的大小 = 元素类型 * 元素个数
```

```c
#include <stdio.h>

int main() {
    //C语言中数组的大小的定义只能是常量！
    int arr[10] = {0,1,2,3,4,5,6,7,8,9};
    // 通过数组下标取值
    printf("%d\n", arr[5]);
    // 通过数组下标赋值
    arr[5] = 100;

    // 数组大小
    printf("元素大小=%d\n", sizeof arr);
    // 元素大小
    printf("元素大小=%d\n", sizeof arr[0]);
    // 元素个数
    printf("元素个数=%d\n", sizeof arr / sizeof arr[0]);

    // 循环遍历数组
    for (int i = 0; i < sizeof arr / sizeof arr[0]; ++i) {
        printf("%d\n", arr[i]);
    }

    // 数组下标越界
    printf("%d\n", arr[-1]);        // 报错；这里跟python不同，python是倒取，C语言是没有这个下标
    return 0;
}
```

### 7.2 数组倒置

```c
#include <stdio.h>

int main() {
    int arr[10] = {0,1,2,3,4,5,6,7,8,9};
    int i = 0;
    int j = sizeof arr / sizeof arr[0] - 1;

    while (i < j){
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;

        i++;
        j--;
    }

    for (int k = 0; k < sizeof arr / sizeof arr[0]; ++k) {
        printf("%d\n", arr[k]);
    }
    return 0;
}
```

### 7.3 二维数组

```
arr[行][列] = {}
```

```c
#include <stdio.h>

int main() {
    int arr[2][3] = {{1,2,3}, {4,5,6}};
    // 数组行数
    printf("数组行数 = %d\n", sizeof arr / sizeof arr[0]);
    printf("数组列数 = %d\n", sizeof arr[0] / sizeof arr[0][0]);

    // 二位数组遍历
    for (int i = 0; i < sizeof arr / sizeof arr[0]; ++i) {
        for (int j = 0; j < sizeof arr[0] / sizeof arr[0][0]; ++j) {
            printf("%d\n", arr[i][j]);
        }
    }

    // address: arr = arr[0] = arr[0][0]
    printf("%p\n", &arr);               // 0000002b56bff9d0
    printf("%p\n", &arr[0]);            // 0000002b56bff9d0
    printf("%p\n", &arr[0][0]);         // 0000002b56bff9d0

    return 0;
}
```

### 7.4 多维数组

超过2维的数组就叫多维数组

```c
#include <stdio.h>

int main() {
    int arr[2][3][4] = {
            {
                    {1, 2, 3, 4},
                    {5, 6, 7, 8},
                    {9, 10, 11, 12}
            },
            {
                    {11, 12, 13, 14},
                    {15, 16, 17, 18},
                    {19, 20, 21, 22}
            }
    };

    for (int i = 0; i < sizeof arr / sizeof arr[0]; ++i) {
        for (int j = 0; j < sizeof arr[0] / sizeof arr[0][0]; ++j) {
            for (int k = 0; k < sizeof arr[0][0] / sizeof arr[0][0][0]; ++k) {
                printf("%d\t", arr[i][j][k]);
            }
            printf("\n");
        }
    }
    return 0;
}
```

### 7.5 字符数组与字符串

#### 7.5.1 字符数组与字符串区别

+ C语言中没有字符串这种数据类型，可以通过char的数组替代
+ 字符串一定是一个char的数组，但char的数组未必是字符串
+ **数字0（和字符`\0`等价）结尾的char数组就是一个字符串**，但如果char数组没有以数字0结尾，那么就不是一个字符串，只是普通字符数组，所以**字符串是一种特殊char的数组**

```c
#include <stdio.h>

int main() {
    char arr1[6] = {'h', 'e', 'l', 'l', 'o'};       // 数据不够，后面自动补0
    char arr2[] = "hello";		// 后面自带 \

    // \0 = 0 但不等于 '0'(字符串)
    printf("%s\n", arr1);       // hello
    printf("%s\n", arr2);       // hello
    return 0;
}
```

### 7.6 字符串的输入输出

#### 7.6.1 `gets()`

```
#include <stdio.h>
char *gets(char *s);
功能：从标准输入读入字符，并保存到指定的内存空间，直到出现换行符或读到文件结尾为止
参数：
	s: 字符串首地址
返回值：
	成功：读入的字符串
	失败：NULL
```

`gets(str)`与s`can("%s", str)`的区别：

+ `gets(str)`允许输入的字符串含有空格
+ `scanf("%s", str)`不允许含有空格

注意：由于`scanf()`和`gets()`无法直到字符串s大小，必须遇到换行符或读到文件结尾为止才接收输入，因此容易导致字符数组越界（缓冲区溢出）的情况

```c
#include <stdio.h>

int main() {
    char arr[100];

    // 获取用户输入，直到出现换行为止
    gets(arr);
    printf("%s", arr);

    // 获取用户输入，直到出现换行为止，与gets()功能一样
    scanf("%[^\n]", arr);
    printf("%s", arr);

    return 0;
}
```

#### 7.6.2 `fgets()`

```
#include <stdio.h>
char *fgets(char *s, int size, FILE * stream)

功能：从stream指定的文件内容读入字符，保存到所指定的内存空间，直到出现换行符，读到文件结尾或是已读了size - 1个字符为止，最后追自动加上字符\0作为字符串结束
参数：
	s：字符串
	size：指定最大读取字符串的长度(size - 1)
	stream：文件指针，如果读键盘输入的字符串，固定写stdin
返回值：
	成功：成功读取的字符串
	读到文件尾或出错
```

`fgets()`在读取一个用户通过键盘输入的字符串的时候，同时把用户输入的回车也作为字符串的一部分。通过scanf和gets输入一个字符串的时候，不包含结尾的`\n`，**但通过fgets结尾多了`\n`**。fgets()函数是安全的，不存在缓冲区溢出的问题

#### 7.6.3 `puts`

```
#include <stdio.h>
int puts(const char *s);
功能：标砖设备输出字符串，在输出完成后自动输出一个\n
参数：
	s：字符串首地址
返回值：
	成功：非负数
	失败：-1
```

```c
#include <stdio.h>

int main() {
    char arr[10];
    gets(arr);
    puts(arr);          // 输出自带换行

    return 0;
}
```

#### 7.6.4 `fputs`

```
#include <stdio.h>
int fputs(const char * str, FILE * stream)
功能：将str所指定的字符串写入到stream指定的文件中，字符串结束符\0不写入文件

参数：
	str：字符串
	stream：文件指针，如果把字符串输出到屏幕，固定为stdout
返回值：
	成功：0
	失败：-1
```



```
#include <stdio.h>

int main() {
    char arr[10];

    fgets(arr, sizeof arr, stdin);
    fputs(arr, stdout);

    return 0;
}
```

#### 7.6.5 strlen

```
#include <string.h>
size_t strlen(const char * s);
功能：计算指定字符串的长度，不包含字符串结束符\0
参数：
	s:字符串首地址
返回值：
	字符串的长度，size_t为unsigned int类型
```

```c
#include <stdio.h>
#include <string.h>

int main() {
    char arr[] = "hello world";
    // 返回字符串长度，返回 unsigned int
    unsigned int len = strlen(arr);
    printf("%d", len);
    return 0;
}
```

## 8. 函数

### 8.1 案例：随机数

```
#include <time.h>
time_t time(time_t *t);
功能：获取当前系统事件
参数：常设置为NULL
返回值：当前系统事件 time_t 相当于long类型，单位为秒

#include <stdlib.h>
void srand(unsigned int seed);
功能：用来设置rand()产生随机数相同的随机种子
参数：如果每次seed相等，rand()产生随机数相等
返回值：无

#include <stdlib.h>
int rand(void);
功能：返回一个随机数值
参数：无
返回值：随机数
```

### 8.2 C语言函数注意事项

1. 形参列表：在定义函数时指定的形参，在未出现函数调用时，它们并不占内存中的存储单元，因此称它们形式参数或虚拟参数，简称形参，表示它们并不是实际存在的数据，**所以形参里的变量不能赋值**

2. 返回值：函数的返回值时通过函数中的return语句获得的，return后面的值可以是一个表达式

   尽量保证return语句中表达式的值和函数返回是同一类型

### 8.3 无参函数和有参函数

```c
#include <stdio.h>

// 无参函数
void void_fuc(void){
    printf("This is void_func\n");
}

int arg_add(int a, int b){
    return a + b;
}

int main(){
    void_fuc();
    int sum = arg_add(10, 20);
    printf("%d\n", sum);
    return 0;
}
```

### 8.4 exit 函数

该函数用于将程序退出

```c
#include <stdio.h>
#include <stdlib.h>

void quit(){
    exit(0);
}

int main(){
    quit();     // 执行到这里，程序就会退出
    return 0;
}


```

## 9. 多文件编程

### 9.1 分文件编程

```c
# fun.h
#pragma once
extern int arg_add(int a, int b);		// 函数声明

# func.c
int arg_add(int a, int b){
    return a + b;
}

# test.c
#include <stdio.h>
#include "fun.h"		// 导入自定义头文件


int main(){
    printf("max = %d", arg_add(10, 30));
}

```

### 9.2 防止头文件重复包含

为了避免同一个文件被include多次，`C/C++`中有两种方式，一张是`#ifndef`方式，一种是`#pragme once`方式，相当于php的`include_onice`

```c
# 方法一
#ifndef __SOMEFILE_H__
extern int arg_add(int a, int b);
#endif
```

```c
# 方法二
#pragma once
extern int arg_add(int a, int b);		// 函数声明
```

## 10. 指针

### 10.1 数据在内存中的存储

以Windows为例，Liunx可能会不同

```c
#include <stdio.h>


int main() {
    int a = -666985;
    printf("%p\n", &a);
    getchar();
    return 0;
}

// 这里 a = -666985
a原码：		1000 0000 0000 1010 0010 1101 0110 1001
a反码：		1111 1111 1111 0101 1101 0010 1001 1010
a补码：		1111 1111 1111 0101 1101 0010 1001 1011
a补码十六进制：   F    F    F    5    D    2    9    7
Windows以小端存储的形式，即以字节为单位从后往前写，得：97 D2 F5 FF
```

![image-20240430221517226](C%E8%AF%AD%E8%A8%80.assets/image-20240430221517226.png)

```c
#include <stdio.h>


int main() {
    int a = 1000;
    printf("%p\n", &a);
    getchar();
    return 0;
}


// 这里 a = 1000
整数的原码、反码、补码都相同，得：
a = 			0000 0000 0000 0000 0000 0011 1110 1000
a补码的十六进制：    0   0    0    0    0    3    E    8
Windows以小端存储的形式，即以字节为单位从后往前写，得：E8 03 00 00
```

![image-20240430221834657](C%E8%AF%AD%E8%A8%80.assets/image-20240430221834657.png)

### 10.2 指针基础知识

```c
#include <stdio.h>


int main() {
    int a = 1000;
    // 指针变量的定义：数据类型* 为指针变量，要存什么类型的指针就要声明什么数据类型的指针变量
    int* p = &a;

    printf("%p\n", &a);     // 00000020cd7ff7d4
    printf("%p\n", p);      // 00000020cd7ff7d4

    // 指针变量间接修改变量的值：*p 为取值，即从 p 所记录的地址中取值
    *p = 50;
    printf("%d\n", a);      // 50
    printf("%d\n", *p);     //50

    // 指针大小：64位系统的指针大小为8（8字节），32为系统的指针大小为4（4字节）
    printf("%d\n", sizeof(int*));       // 00 00 00 20 cd 7f f7 d4 = 8byte
    return 0;
}

```

### 10.3 野指针和空指针

指针变量也是变量，是变量就可以任意赋值，不要越界即可（32位为4字节，64位为8字节），但是，**任意数值赋值给指针变量没有意义，因为这样的指针就成了野指针**，此指针指向的区域是未知（操作系统不允许操作此指针指向的内存区域）。所以，**野指针不会直接引发错误，操作野指针指向的内存区域才会出问题**

```c
#include <stdio.h>


int main() {
    // 野指针：指针变量指向一个未知的空间
    int* p = 100;       // Unable to read memory
    // 操作系统将 0-255 作为系统占用不允许访问操作
    // 操作野指针对应的内存空间可能报错
    getchar();

    return 0;
}
```

但是，野指针和有效指针变量保存的都是数值，为了标志此指针变量没有指向任何变量（空闲可用），C语言中，可以把NULL赋值给此指针，这样就标志此指针为空指针，没有任何指针

```c
#include <stdio.h>


int main() {
    // 空指针是指内存地址编号为0的空间
    int* p = NULL;
    // 操作空指针对应的空间一定会报错
    *p = 100;                       // err
    // 空指针可以用作条件判断
    if (p == NULL){
        printf("空指针");
    }
    return 0;
}

```

### 10.4 万能指针

`void *`指针可以指向任意变量的内存空间

```c
#include <stdio.h>


int main() {
    int a = 100;
    // 万能指针可以接收任意类型变量的内存地址
    void* p = &a;
    // 在通过万能指针修改变量的指时 需要找到变量对应的指针类型
    *(int *) p = 200;
    printf("%d\n", a);				// 200
    printf("%d\n", *(int *)p);		// 200
    return 0;
}
```

### 10.5 const 修饰的指针

在编辑程序时，指针作为函数参数，如果不想修改指针对应内存空间的值，需要使用const修饰指针数据类型

`const`修改数据类型不能修改指针指向的值，`const`修改变量不能修改变量的值，`const`修饰的常量可以通过加一级指针修改

```c
#include <stdio.h>


int main() {
    int a = 10;
    int b = 20;

    // const 修饰指针类型：可以修改指针变量的值，不可以修改指针指向内存空间的值
    const int *p = &a;
    p = &b;
    printf("%d\n", *p);

    // 可以修改指针指向内存空间的值：不可以修改指针变量的值
    int* const p1 = &a;
    *p1 = 200;
    printf("%d\n", *p1);

    // const 修改指针变量和指针类型：两个都不可以改
    const int* const p2 = &a;
    // * 是降维度，& 是升维度
    int **pp2 = &p2;
    // **pp2 --> &p2 --> &a, &a = 300;
    **pp2 = 300;
    printf("%d\n", a);
    return 0;
}

```

## 11. 指针和数组

### 11.1 指针和数组的关系

```c
#include <stdio.h>


int main() {
    int arr[11] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11};
    int* p = &arr;

    for (int i = 0; i < sizeof arr / sizeof arr[0]; ++i) {
        printf("%d\n", *p ++);
    }

    // 000000ff119ffdd8 - 000000ff119ffdb0 = 0x28 = 0010 1000 = 32 + 8 = 40 / sizeof(int) = 10，得到步长！
    // 两个指针相减 等到的结果是两个指针的偏移量（步长）
    int step = p - arr;
    printf("p = %p\n", p);          // p = 000000ff119ffdd8
    printf("arr = %p\n", arr);      // arr = 000000ff119ffdb0
    printf("step = %p\n", &step);   // step = 000000ff119ffdac

//    printf("%p", *(int *)(p-arr));

    // 查看两个指针地址详减的结果
    printf("%p\n", (void *)p - (void *)arr);

    // 指针p是变量，arr是常量
    printf("指针size: %d\n", sizeof p);			// 指针size: 8
    printf("数组size: %d\n", sizeof arr);			// 数组size: 44
    return 0;
}

```

### 11.2 数组做为函数的形参

```c
#include <stdio.h>

// 数组作为形参会退化为指针
void func(int arr[], int len) {
    for (int i = 0; i < len; ++i) {
        printf("%d\n", *(arr + i));
    }
}

int main() {
    int arr[] = {1,2,3,4,5,6,7,8};
    func(arr, sizeof arr / sizeof arr[0]);
    return 0;
}

```

### 11.3 指针加减运算

#### 11.3.1 指针的运算

+ 指针计算不是简单的整数相加
+ 如果是一个 `int *`，`+1`的结果是增加一个int的大小
+ 如果是一个`char *`，`+1`的结果是增加一个char的大小

**Tips：**运算符是否可以对指针操作取决于运算的结果的指针是否是野指针！如果是野指针就是不允许，因为野指针没有意义

### 11.4 指针的加法运算案例

```c
#include <stdio.h>

// 数组作为形参会退化为指针
void copy1(char *dest, char *src) {
    int i = 0;
    // *(src + i) = src[i]
    while (src[i] != '\0') {
        dest[i] = src[i];
        i++;
    }
    dest[i] = '\0';
}

void copy2(char *dest, char *src) {
    int i = 0;
    while (*(src + i)) {
        *(src + i) = *(dest + i);
        i++;
    }
    *(dest + i) = '\0';
}

void copy3(char *dest, char *src) {
    while (*src) {
        *dest = *src;
        dest++;
        src++;
    }
    *dest = '\0';
}

int main() {
    char arr[] = "LOVE YOU";
    char dest[100];
    copy1(dest, arr);
    copy2(dest, arr);
    copy3(dest, arr);
    printf("%s", dest);
    return 0;
}

```

### 11.5 指针数组

指针数组，它是数组，数组的每个元素都是指针类型

```c
#include <stdio.h>


int main() {
    // 指针数组
    int a = 10;
    int b = 20;
    int c = 30;
    int* arr[] = {&a, &b, &c};

    // 遍历指针数组
    for (int i = 0; i < sizeof arr / sizeof arr[0]; ++i) {
        printf("%d\n", *arr[i]);
    }

    int arr1[3] = {1,2,3};
    int arr2[3] = {4,5,6};
    int arr3[3] = {7,8,9};
    // 指针数组是一个特殊的二维数组
    int* address_arr[] = {&arr1, &arr2, &arr3};
    for (int i = 0; i < sizeof address_arr / sizeof address_arr[0]; ++i) {
        for (int j = 0; j <= sizeof address_arr[0] / sizeof *(address_arr[0]); ++j) {
            // 下面写法都意义：arr[i] = *(arr + i)
            printf("%d\n", address_arr[i][j]);
            printf("%d\n", *(*(address_arr +i) + j));
        }
    }
    return 0;
}

```

## 12. 指针和函数

### 12.1 函数形参改变实参的值

```c
#include <stdio.h>

void swap(int* num1, int* num2){
    int tmp = *num1;
    *num1 = *num2;
    *num2 = tmp;
}

int main() {
    int a = 10;
    int b = 20;
    swap(&a, &b);
    printf("a=%d, b=%d", a, b);
    return 0;
}
```

### 12.2 数组名的函数参数

数组名做函数参数，函数的形参会退化为指针

```c
#include <stdio.h>

// todo: 字符串追加函数
void append(char *char1, char *char2) {
    while (*char1) {
        // 指针 + sizeof char
        char1++;
    }

    while (*char2) {
        *char1 = *char2;
        char1++;
        char2++;
    }
    *char1 = '\0';
}

int main() {
    char arr1[] = "hello";
    char arr2[] = " world";
    append(arr1, arr2);
    printf("%s\n", arr1);
    puts(arr1);
    return 0;
}

```

## 13. 指针和字符串

### 13.1 主函数参数

```c
#include <stdio.h>
// int argc 表示传递参数的个数
// char* argv[]={'test.exe' '-o' '111' '222'} 表示参数的具体内容
int main(int argc, char* argv[]) {
    for (int i = 0; i < argc; ++i) {
        printf("%s\n", argv[i]);
    }
    return 0;
}

// 输出
D:\Clan>test.exe -o 111 222
test.exe
-o
111
222
```

### 13.2 字符串函数

#### 1)`strcpy()`

```
#include <string.h>
char* strcpy(char* dest, consr char* src);
功能：把src所指向的字符串复制到dest所指向的空间中，\0也会拷贝过去
参数：
	dest：目的字符串首地址
	src：源字符首地址
返回值：
	成功：返回dest字符串的首地址
	失败：NULL
```

注意：如果参数dest所指的内存空间不够大，可能会造成缓冲区溢出的情况

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char src[] = "hello world";
    char dst[100] = {0};

    strcpy(dst, src);
    printf("%s\n", dst);
    return 0;
}
```

#### 2)`strncpy()`

```
#include <string.h>
char* strncpy(char* dest, const char* src, size_t n);
功能：把src所指向的字符串复制到dest所指向的空间中，\0也会拷贝过去
参数：
	dest：目的字符串首地址
	src：源字符首地址
	n：指定需要拷贝字符串个数
返回值：
	成功：返回dest字符串的首地址
	失败：NULL
```

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char src[] = "hello world";
    char dst[100] = {0};

    strncpy(dst, src, 5);
    printf("%s\n", dst);
    return 0;
}
```

#### 3)`strcat()`

```
#include <string.h>
char *strcat(char* dest, const char* src);
功能：将src字符串连接到dest的尾部，\0也会追加过去
参数：
	dest：目的字符串首地址
	src：源字符串首地址
返回值：
	成功：返回dest字符串的首地址
	失败：NULL
```

注意：如果参数dest所指的内存空间不够大，可能会造成缓冲区溢出的情况

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char dest[] = "hello";
    char src[] = "world";

    strcat(dest, src);
    printf("%s", dest);
    return 0;
}
```

#### 4)`strncat()`

```c
#include <string.h>
char *strcat(char* dest, const char* src);
功能：将src字符串连接到dest的尾部，\0也会追加过去
参数：
	dest：目的字符串首地址
	src：源字符串首地址
    n：指定需要追加字符串个数
返回值：
	成功：返回dest字符串的首地址
	失败：NULL
```

#### 5)`strcmp()`

```
#include <string.h>
int strcmp(const char* s1, consr char* s2);
功能：比较s1和s2的大小，比较的是字符ASCII码大小
参数：
	s1：字符串1首地址
	s2：字符串2首地址
返回值：
	相等：0
	大于 >0：在不同操作系统strcmp结果会不同，返回ASCII值
	小于 <0
```

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char dest[] = "hello";
    char src[] = "hello";

    printf("%d", strcmp(dest, src));        // 0
    return 0;
}
```

#### 6)`strncmp()`

```
#include <string.h>
int strcmp(const char* s1, consr char* s2, size_t n);
功能：比较s1和s2的指定长度的大小，比较的是字符ASCII码大小
参数：
	s1：字符串1首地址
	s2：字符串2首地址
	n：指定的长度
返回值：
	相等：0
	大于 >0：在不同操作系统strcmp结果会不同，返回ASCII值
	小于 <0
```

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char dest[] = "hello";
    char src[] = "hell";

    printf("%d", strncmp(dest, src, 4));        // 0
    return 0;
}
```

#### 7)`sprintf()`

```
#include <stdio.h>
int sprintf(char* str, consr char* format, ...);
功能：根据参数formart字符串来转换并格式化数据，然后将结果输出到str指定的空间中，直到出现字符串结束符\0为止
参数：
	str：字符串首地址
	format：字符串格式，用法和printf()一样
返回值：
	成功：实际格式化的字符个数
	失败：-1
```

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char ch[100] = {0};
    sprintf(ch, "hello world");
    printf("%s\n", ch);                     // hello world
    sprintf(ch, "%d + %d = %d", 1, 1, 1 + 1);
    printf("%s\n", ch);                     // 1 + 1 = 2
    return 0;
}
```

#### 8)`sscanf()`

```
#include <stdio.h>
int sscanf(const char* str, const char* format, ...);
功能：从str指定的字符串读取数据，并根据参数format字符串来转换并格式化数据
参数：
	str：指定的字符串首地址
	format：字符串格式，用法和scanf()一样
返回值：
	成功：参数数目，成功转换的值的个数
	失败：-1
```

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    char ch[] = "2+2=4";
    int a;
    int b;
    int c;
    sscanf(ch, "%d+%d=%d", &a, &b, &c);
    printf("%d\n", a);          // 2
    printf("%d\n", b);          // 2
    printf("%d\n", c);          // 4
    return 0;
}
```

#### 9)`strchr()`

```
#include <string.h>
char* strchr(const char* s, int c);
功能：在字符串s中查找字母出现的位置
参数：
	s：字符串首地址
	c：匹配字母（字符）
返回值：
	成功：返回第一次出现的c地址(地址)
	失败：NULL
```

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char ch[] = "abcdefg";
    char need = 'c';
    printf("%p", strchr(ch, need));     // 000000b6c63ffc29
    return 0;
}
```

#### 10)`strstr()`

```
#include <string.h>
char* strstr(consr char* haystack, const char* needle);
功能：在字符串haystack中查找字符串needle出现的位置
参数：
	haystack：源字符串首地址
	needle：匹配字符串首地址
返回值：
	成功：返回第一次出现needle地址
	失败：NULL
```

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char ch[] = "abcdefg";
    char need[] = "efg";
    printf("%p", strstr(ch, need));     // 000000d3451ffabc
    return 0;
}
```

#### 11)`strtok()`

```
#include <string.h>
char* strtok(char* str, const char *delim);
功能：将字符串分割成一个个片段。当strtok()在参数s的字符串中发现参数delim中包含的分割字符时，则会将字符改成\0字符，当连续出现多个时只替换第一个为\0
参数：
	str：指向要分割的字符串
	delim：为分割字符串中包含的所有字符
返回值：
	成功：分割后字符串首地址
	失败：NULL
```

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char url[] = "www.baidu.com";
    char* p = strtok(url, ".");         // www
    printf("%s\n", p);
    p = strtok(NULL, ".");              // baidu
    printf("%s\n", p);
    p = strtok(NULL, ".");              // com
    printf("%s\n", p);
    return 0;
}
```

#### 12)`atoi()`

```
#include <stdlib.h>
int atoi(const char* nptr);
功能：atoi()会扫描nptr字符串，跳过前面的空格字符，直到遇到数字或正负号才开始做转换，而遇到非数字或字符串结束符\0才结束转换，并将结果返回返回值
参数：nptr：待转换的字符串
返回值：成功转换后整数
```

类似的函数有：

+ `atof()`：把一个小数形式的字符串转换为一个浮点数
+ `atol()`：把一个字符串转换为long类型

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    char ch[] = "123456abc";		// 123456
    printf("%d", atoi(ch));
    return 0;
}
```

## 14. 内存管理

### 14.1 静态全局变量

+ `static`局部变量的作用域也是在定义的函数内有效
+ `static`局部变量的生命周期和程序运行周期一样，同时`static`局部变量的值只初始化一次，但可以赋值多次
+ `static`局部变量若未赋值以处值，则有系统自动赋值’数值型变量字符赋初值0，字符型变量赋空字符

```c
#include <stdio.h>

// static关键字声明局部变量：只能在函数内部使用
// static关键字声明全局变量：只能在本文件中使用
void func(){
    // 静态变量只会初始化一次，可以多次赋值，有点像python的 yield 关键字，保存 变量状态
    // 生命周期：从程序创建到程序销毁
    static int num = 10;
    num++;
    printf("%d\n", num);
}

int main(int argc, char *argv[]) {
    for (int i = 0; i < 10; ++i) {
        func();
    }
    return 0;
}
```

### 14.2 全局函数和静态函数

在C语言中函数默认都是全局的，使用关键字static可以将函数声明为静态，函数定义为static就意外着这个函数只能在定义这个函数的文件中使用，在其他文件中不能调用，即使在其他文件中声明这个函数都没用

对于不同文件中的static函数名字可以相同

![image-20240503011642053](C%E8%AF%AD%E8%A8%80.assets/image-20240503011642053.png)

### 14.3 `extren`全局声明

`extern int a;`声明一个变量，这个全局变量在别的文件中已经定义了，这里时声明，而不是定义

```c
#func.c
int a = 10;

# test.c
#include <stdio.h>

extern int a;
int main(int argc, char *argv[]) {
    printf("%d", a);
    return 0;
}
```

