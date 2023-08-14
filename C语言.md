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

简单来说：**指针就是地址**

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

## 案例：猜数字游戏

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>


void menu() {
    printf("**********************************\n");
    printf("*** 0:exit ******* 1:play ********\n");
    printf("**********************************\n");
}

void game(){
    int ret = rand() / 300;
    int guess = 0;
    while (1){
        printf("猜测一个数字：");
        scanf("%d", &guess);
        if (guess > ret){
            printf("猜大了\n");
        } else if (guess < ret){
            printf("猜小了\n");
        } else{
            printf("猜对了\n");
            break;
        }
    }

}

int main() {
    srand((int) time(NULL));
    int input = 0;
    do {
        menu();
        printf("请输入你的选择：");
        scanf("%d", &input);
        switch (input) {
            case 1:
                printf("开始游戏\n");
                game();
                break;
            case 0:
                printf("结束游戏\n");
                break;
            default:
                printf("输入错误\n");
                break;
        }
    } while (input);

    return 0;
}
```

# 函数

## 函数初始

### 案例：交换函数

```c
#include <stdio.h>

// 交换函数
// 如果想要通过函数来改变全局变量，可以通过变量地址来改变
void Swap(int* x, int* y){
    int tmp;
    tmp = *x;
    *x = *y;
    *y = tmp;
}

int main(){
    int a = 10;
    int b = 20;
    int* pa = &a;
    int* pb = &b;
    printf("a=%d;b=%d\n", a,b);
    Swap(pa, pb);
    printf("a=%d;b=%d", a,b);
    return 0;
}
```

## 函数递归

C语言中的递归函数就是python中的闭包

```c
#include <stdio.h>

void my_strlen(char* str){
    int count = 0;
    while (1){
        if (str[count] != '\0'){
            count ++;
        } else{
            break;
        }
    }
    printf("count = %d", count);
}

int main() {
    char arr[] = "abcdef";
    // 在C语言中，传递一个数组当实参时，实际传递的时这个数组的指针地址
    my_strlen(arr);
    return 0;
}
```

# 数组

## 数组的遍历

一维数组的创建，是在内存中开辟一块连续的内存空间来存储的

```c
#include <stdio.h>
#include <string.h>

int main() {
    // 字符串数组遍历
    char arr1[] = "abcdefg";
    for (int i = 0; i < strlen(arr1); ++i) {
        printf("%c\n", arr1[i]);
    }

    // 数字数组遍历
    int arr2[] = {0,1,2,3,4,5,6};
    // 计算数组的大小除以每个元素的大小得到数组元素的数量
    for (int i = 0; i < sizeof arr2 / sizeof arr2[0]; ++i) {
        printf("%d\n",arr2[i]);
    }
    
    for (int i = 0; i < sizeof arr2 / sizeof arr2[0]; ++i) {
    	printf("arr[%d]->%p\n", i, &arr2[i]);
    }
    return 0;
}

// 输出
arr[0]->00000092961ffb70
arr[1]->00000092961ffb74
arr[2]->00000092961ffb78
arr[3]->00000092961ffb7c
arr[4]->00000092961ffb80
arr[5]->00000092961ffb84
arr[6]->00000092961ffb88
```

## 二维数组的创建

在一维数组中有包含几个数组就是几行，每个二维数组中包含几个元素呗称为几列；`arr[行][列]`

```c
int main() {
    int arr[2][4] = {{1, 2, 3, 4},
                     {5, 6, 7, 8}};
    return 0;
}
```

### 二维数组的遍历

二维数组的存储依然是在内存中开辟一块连续的内存地址来存储

```c
#include <stdio.h>
#include <string.h>

int main() {
    int arr[2][4] = {{1, 2, 3, 4},
                     {5, 6, 7, 8}};

    // 二维数组的遍历
    for (int i = 0; i < sizeof arr / sizeof arr[0]; ++i) {
        for (int j = 0; j < sizeof arr[0] / sizeof arr[0][0]; ++j) {
            printf("%d\n", arr[i][j]);
        }
    }

    // 二维数组的内存地址遍历
    for (int i = 0; i < sizeof arr / sizeof arr[0]; ++i) {
        for (int j = 0; j < sizeof arr[0] / sizeof arr[0][0]; ++j) {
            printf("arr[%d][%d] -> %p\n", i, j, &arr[i][j]);
        }
    }
    return 0;
}

// 输出
arr[0][0] -> 0000001c90dff7e0
arr[0][1] -> 0000001c90dff7e4
arr[0][2] -> 0000001c90dff7e8
arr[0][3] -> 0000001c90dff7ec
arr[1][0] -> 0000001c90dff7f0
arr[1][1] -> 0000001c90dff7f4
arr[1][2] -> 0000001c90dff7f8
arr[1][3] -> 0000001c90dff7fc
```

# 指针详解

指针其实就是内存地址，在64位系统中，指针的大小是：64 / 8 = 8个字节，32位系统中，指针的大小是：34 / 4 = 8 个字节。

## 指针与指针类型

**指针的类型决定了指针进行解应用操作时能访问的空间大小**

```c
#include <stdio.h>

int main() {
    int a= 123456;
    // 由于 int 类型占4个字节，所以可以访问的空间大小也是4个字节，即可以修改前4个字节的数据
    int* pa = &a;
    *pa = 0;
    // 由于 char 类型占1个字节，所以可以访问的空间大小也是1个字节，即可以修改前1个字节的数据
	char* ca = &a;
	*ca = 0;
    return 0;
}
```

**指针类型决定了指针的步长**

```c
#include <stdio.h>

int main() {
    int a= 123456;
    int* pa = &a;
    char* ca = &a;
    printf("%p\n",&a);
    printf("int：%p\n", pa+1);
    printf("char：%p\n", ca+1);
    return 0;
}


// 输出
0000009a1b9ffbfc
// 由于指针类型是：int，所以步长为4
int：0000009a1b9ffc00
// 由于指针类型是：char，所以步长为1
char：0000009a1b9ffbfd
```

## 野指针

在C语言中，野指针（Wild Pointer）是指指向无效内存地址或未初始化的内存的指针。野指针是一个危险的编程错误，可能导致程序崩溃或产生未定义行为。

以下是一些可能导致野指针的情况：

1. **未初始化的指针：** 当声明指针变量但没有初始化时，它的值是未定义的，它会指向一个随机的内存地址，这是一个野指针。

   ```
   cCopy code
   int* ptr; // 未初始化的指针
   ```

2. **指针释放后继续使用：** 当指针指向的内存已经被释放（比如通过`free()`函数释放动态分配的内存），但后续继续使用这个指针时，会导致野指针。

   ```
   cCopy codeint* ptr = (int*)malloc(sizeof(int));
   free(ptr);
   // 此后继续使用 ptr，这将导致野指针
   ```

3. **指针越界访问：** 当使用指针访问数组或其他数据结构时，如果指针越界访问了无效的内存，也会导致野指针。

   ```
   cCopy codeint arr[5] = {1, 2, 3, 4, 5};
   int* ptr = &arr[0];
   // 后续使用 ptr[10]，超出了数组边界，导致野指针
   ```

## 指针的加减

可以通过控制指针的步长来控制指针的值

```c
#include <stdio.h>

int main() {
    int arr[10] = {1,2,3,4,5,6,7,8,9,10};
    int* p = &arr;
    for (int i = 0; i < 10; ++i) {
        printf("%d\n", *p++);
    }
    return 0;
}


// 输出
1
2
3
4
5
6
7
8
9
10
```

此外，**数组名代表的是第一个指针地址**，我们也可以利用这一点进行操作

```c
#include <stdio.h>

int main() {
    int arr[10] = {1,2,3,4,5,6,7,8,9,10};
    int* p = &arr;
    for (int i = 0; i < 10; ++i) {
        printf("%d\n", *arr + i);
    }
    return 0;
}

// 输出
1
2
3
4
5
6
7
8
9
10
```

## 二级指针

二级指针实际上就是在指针的基础上在指向一个指针地址

```c
#include <stdio.h>

int main() {
    int arr[10] = {1,2,3,4,5,6,7,8,9,10};
    int* p = &arr;
    int** pp = &p;
    printf("%d", **pp);
    return 0;
}

// 输出
2
```

## 指针数组

指针数组：存储的内容是指针

```c
#include <stdio.h>

int main() {
    int a = 10;
    int b = 20;
    int c = 30;
    int* arr[3] = {&a, &b, &c};
    for (int i = 0; i < 3; ++i) {
        printf("%d\n",*arr[i]);
    }
    return 0;
}

// 输出
10
20
30
```

# 数据存储

在计算机中，整型分为：**无符号类型**和**有符号类型**两种，这两种类型的存储方式都是用**补码 - -> 十六进制**来存储

```c
int main() {
    int a = 20;
    // 00000000 00000000 00000000 00010100 - 原码
    // 00000000 00000000 00000000 00010100 - 反码
    // 00000000 00000000 00000000 00010100 - 补码
    // 0 0      0 0      0 0      1 4      - 十六进制
    int b = -10;
    // 1000000 00000000 00000000 00001010 - 原码
    // 1111111 1111111 111111111 11110101 - 反码
    // 1111111 1111111 111111111 11110110 - 补码
    // F F     F F     F F       F 6      - 十六进制
    return 0;
}
```

![image-20230721170847778](C%E8%AF%AD%E8%A8%80.assets/image-20230721170847778.png)

**有符号`char`和无符号`char`的区别**

```
有符号：认定最高位位符号位；范围：
01111111 -> 127
...
00000000 -> 0
...
11111111 -> -127
10000000 -> 128

其中，由于 1000000 在还原成原码的时候比较特殊，无法直接减一，所以直接认定他为128

无符号：认定为无符号位；范围：
11111111 -> 128
0000
```

