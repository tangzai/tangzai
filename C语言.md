# C语言初识

## 第一个C语言程序

### 注释符

```
单行注释：//
多行注释：/**/
```

### `main()`函数

C语言中的`main()`函数是程序的执行入口，程序将main函数内的代码块从上到下执行。==main函数有且只能有一个==

```c
#include <stdio.h>      // 包含头文件，包含C语言本地函数库

int main()              // 主函数（程序入口，程序会在这里开始执行，只能有一个）
{
    printf( "hello world");
    return 0;
}

/*
    多行注释
*/
```

## 关键字

### 格式：

```
类型 变量名:

// 在内存中开辟1个字节大小的内存空间，取名位ch，并赋值位'a'
char ch = 'a';
```

### 数字类型相关的关键字

+ `char`字符型，占1个字节
+ `short`短整型，占2个字节
+ `int`整形，在32位系统占4个字节
+ `long`长整型，在32位系统中占4个字节
+ `float`单精度浮点型，在32位系统中占4个字节
+ `double`双精度浮点型，在32位系统中占8个字节，精度比单浮点型更高
+ `signed`有符号（正负）的意思，用于保存`char、整型`数据的时候使用`sigened`修饰，可保存正数、负数还有0（默认省略）
+ `unsigned`无符号的意思，在定义`char，整型`数据的时候修饰使用`unsigned`，只能保存正数还有0
+ `void`空类型的关键字，用来修饰函数，代表该函数没有参数或者没有返回值

### 演示

```c
#include <stdio.h>      // 包含头文件

int main()              // 主函数（程序入口，程序会在这里开始执行，只能有一个）
{
    char a;
    short int b;
    int c;
    long int d;
    float e = 3.8f;
    double f = 3.8;
    printf("sizeo(a) =%d\n", sizeof(a));
    printf("sizeo(b) =%d\n", sizeof(b));
    printf("sizeo(c) =%d\n", sizeof(c));
    printf("sizeo(d) =%d\n", sizeof(d));
    printf("sizeo(e) =%d\n", sizeof(e));
    printf("sizeo(f) =%d\n", sizeof(f));
    return 0;
}

// 输出
PS D:\CLan> cd "d:\CLan\" ; if ($?) { gcc test.c -o test } ; if ($?) { .\test }
sizeo(a) =1
sizeo(b) =2
sizeo(c) =4
sizeo(d) =4
sizeo(e) =4
sizeo(f) =8
```

## 变量和常量

### 变量

可随时改变的量被称为变量：身高、体重

### 常量

一经确定不可随意更改的量被称为常量：性别、血型

### 全局变量和局部变量的区别

+ 定义在函数之外的变量被称为全局变量，全局变量可在程序的任何位置被调用
+ 定义在函数之内的变量被称为局部变量，局部变量只能在该程序内部被调用

```c
# include <stdio.h>

int age = 100;
int main(int argc, char const *argv[])
{
    /* code */
    int age = 10;
    printf("%d ", age);
    return 0;
}

// 输出
PS D:\CLan> cd "d:\CLan\" ; if ($?) { gcc test00.c -o test00 } ; if ($?) { .\test00 }
10 
```

### 生命周期

+ 全局变量的生命周期在程序的运行开始，在程序的结束结束
+ 局部变量的生命周期在进作用域开始，出作用域结束

## 常量

### 常量的分类

+ 字面常量
+ const 修饰的常变量
+ `#` define定义的标识符的常量
+ 枚举常量

### 演示

```c
# include <stdio.h>
// # defind 定义的标识符常量
# define MAX 100

// 枚举常量
enum Color{
    RED,
    BULUE,
    GREEN
};

int main(int argc, char const *argv[])
{
    /* code */
    // 字面常量
    10;
    printf("%d\n", 10);
    // const 修饰的常变量
    const int num = 10;
    printf("%d\n", num);

    printf("MAX=%d\n", MAX);

    enum Color bob = RED;
    printf("Color=%d\n", bob);

    return 0;
}

// 输出
PS D:\CLan> cd "d:\CLan\" ; if ($?) { gcc test00.c -o test00 } ; if ($?) { .\test00 }
10
10
MAX=100
Color=0
```

## 字符串

字符串的结束标志是一个\0的转义字符。在计算字符长度的时候\0是结束标志，不算做字符串内容

```c
# include <stdio.h>
// # defind 定义的标识符常量


int main(int argc, char const *argv[])
{
    /* code */
    char arr1[] = "abc";
    // 使用 \0 标识字符串末尾
    char arr2[] =  {'a','b','c','\0'};
    printf("arr1=%s\n", arr1);
    printf("arr2=%s\n", arr2);
    return 0;
}

// 案例二
# include <stdio.h>

int main(){
    // char 类型默认只存储一个字符，所以这里需要使用 字符数组 来存储
    char input[20] = {0};
    printf("输入密码：");
    // 使用 %s 来提取内容，所以会在遇到换行或者空白内容时停止读取内容
    scanf("%s", input);
    printf("input=%s", input);
}
```

## if判断初识

```c
# include <stdio.h>


int main(int argc, char const *argv[])
{
    int input = 0;

    printf("你要好好学习吗？(1/0) > ");
    scanf("%d", &input);
    if (input){
        printf("拿offer");
    }else{
        printf("卖红薯");
    }
       
}

// 输出
PS D:\CLan> cd "d:\CLan\" ; if ($?) { gcc test00.c -o test00 } ; if ($?) { .\test00 }
你要好好学习吗？(1/0) > 0
卖红薯
PS D:\CLan> cd "d:\CLan\" ; if ($?) { gcc test00.c -o test00 } ; if ($?) { .\test00 }
你要好好学习吗？(1/0) > 1
拿offer
```

## 函数初识

```c
# include <stdio.h>

// 用户自定义函数Add
int Add(int num1, int num2){
    return num1 + num2;
}

int main(int argc, char const *argv[])
{
    // 定义变量
    int num1 = 0;
    int num2 = 0;
    // 接收用户输入
    printf("两数之和(x/y)：");
    scanf("%d%d", &num1,&num2);
    // 计算赋值
    int sum = Add(num1, num2);
    // 输出
    printf("sum = %d", sum);
}

// 输出
PS D:\CLan> cd "d:\CLan\" ; if ($?) { gcc test00.c -o test00 } ; if ($?) { .\test00 }
两数之和(x/y)：60 60
sum = 120
```

## 数组初识

> C语言给定了数组的定义：一种相同类型的元素的集合

```c
# include <stdio.h>

int main(int argc, char const *argv[])
{
    // 定义一个容量为10的整型数组
    int arr1[10];
    // 定义一个容量为10的字符串数组
    char arr2[10];
    // 定义一个容量为10的浮点型数组
    float arr3[10];

    // 数组的遍历输出
    int arr4[10] = {1,2,3,4,5,6,7,8,9,10};
    for (size_t i = 0; i < 10; i++)
    {
        printf("num = %d\n", arr4[i]);
    }
    
}


// 输出
PS D:\CLan> cd "d:\CLan\" ; if ($?) { gcc test00.c -o test00 } ; if ($?) { .\test00 }
num = 1
num = 2
num = 3
num = 4
num = 5
num = 6
num = 7
num = 8
num = 9
num = 10
```

## static关键字

### static关键字简介

在C语言中，`static` 是一个关键字，它可以应用于变量、函数和代码块，具有不同的含义和作用域。

1. 静态变量： 当 `static` 修饰一个变量时，它表示该变量具有静态存储期和块作用域。静态变量在程序执行期间一直存在，不会随着函数的调用而创建和销毁。它只会被初始化一次，并且保持其值在多次函数调用之间持久。静态变量默认初始化为0。
2. 静态函数： 当 `static` 修饰一个函数时，它表示该函数具有内部链接（internal linkage），即该函数只在定义它的源文件中可见，无法被其他源文件调用。静态函数对于限制函数的作用范围和避免命名冲突很有用。
3. 静态代码块： 在C语言中，没有直接支持静态代码块的概念。但是可以使用静态变量来模拟静态代码块的效果。通过在函数内部定义一个带有 `static` 修饰符的变量，可以使该变量只在首次进入函数时被初始化一次，实现类似静态代码块的行为。

总结： `static` 关键字在C语言中用于提供变量、函数和代码块的额外特性。它的具体含义和作用范围取决于修饰的对象类型。静态变量在多次函数调用之间保持持久状态，静态函数具有内部链接限制，静态代码块通过静态变量的初始化来模拟静态代码块的行为。

### 演示：

```c
# include <stdio.h>

void text(){
    static int a = 0;
    a++;
    printf("a = %d\n", a);
}

int main(){
    int i = 0;
    while (i < 5){
        text();
        i++;
    }
    return 0;
}

// 输出
D:\Clan\cmake-build-debug\Clan.exe
a = 1
a = 2
a = 3
a = 4
a = 5
```

## `# define`关键字

### 定义常量

```c
# include <stdio.h>
# define MAX 10

int main(){
    printf("man = %d", MAX);
    return 0;
}

// 输出
man = 10
```



### 定义函数

```c
# include <stdio.h>
# define compare(x,y) (x>y?x:y)

int main(){
    int man = compare(2,1);
    printf("man = %d", man);
    return 0;
}

// 输出
man = 2
```

## 指针

### 内存地址的生成

一个64位的系统，最多能生成2^64次方个内存地址，一个内存地址占`64 / 8 = 8`字节

一个32位的系统，最多能生成2^32次方个内存地址，一个内存地址占`32 / 8 = 4`字节，从：

```
00000000 00000000 00000000 00000001
...
11111111 11111111 11111111 11111111
```

其中，一个==地址空间==占一个字节大小，则容量为：

```
2^32(B) / 1024(KB) / 1024(MB) / 1024 = 4GB
```

### 演示：

```c
# include <stdio.h>

int main(){
    int a = 10;
    int* p = &a;                        // int* 类型存储地址
    printf("p = %p\n", p);        // %p 格式化打印地址

    // 解引用操作符该改变值
    *p = 20;
    printf("a = %d", a);
}


// 输出
p = 0000007f835ffdb4
a = 20
```

## 结构体

在C语言中，结构体（Struct）是一种自定义的数据类型，它允许你将不同类型的数据组合在一起，形成一个更复杂的数据结构。结构体可以包含多个成员（也称为字段或属性），每个成员可以是不同的数据类型，如整数、浮点数、字符、数组、指针等。

```c
# include <stdio.h>

struct Book {
    char name[20];
    int price;
};

int main(){
    // 实例化结构体，并描述属性
    struct Book b1 = {"C语言程序设计", 55};
    printf("书名：%s\n", b1.name);
    printf("价格：%d\n", b1.price);
    // 修改属性
    b1.price = 10;
    printf("修改后的价格：%d\n", b1.price);

    // 指针操作
    struct Book* pb = &b1;
    printf("指针查询b1书名：%s\n", pb->name);
    printf("真正查询b1价格：%d\n", pb->price);
    return 0;
};

// 输出
书名：C语言程序设计
价格：55
修改后的价格：10
指针查询b1书名：C语言程序设计真正查询b1价格：10
```

# 分支和循环

## if函数详解

没啥说的，直接看代码示例

**1. 判断输入的数字是否是奇数**

```c
# include <stdio.h>

int main(){
    int num;
    printf("输入一个数字：");
    scanf("%d", &num);
    if (num % 2 != 0){
        printf("是奇数");
    } else{
        printf("是偶数");
    }

    return 0;
}
```

**2. 循环输出1-100的奇数**

```c
# include <stdio.h>

int main(){
    for (int i = 0; i <= 100; ++i) {
        if (i % 2 !=0 ){
            printf("%d\n",i);
        }
    }

    return 0;
}
```

## switch语句

```c
# include <stdio.h>

int main(){
    int num = 30;
    switch (num) {
        case 1:
        case 2:
        case 3:
        case 4:
        case 5:
            printf("工作日\n");
            break;
        case 6:
        case 7:
            printf("休息日\n");
            break;
        default:
            printf("错误输入\n");
            break;
    }
    return 0;
}
```

## while语句

```c
# include <stdio.h>

int main(){
    int num = 0;
    while (num < 10){
        num ++;
        printf("num=%d\n", num);
    }
    return 0;
}`
```

