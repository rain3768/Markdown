# C语言注意

## 一、i++与++i

注意这两个式子的值，有些情况下或造成很大影响。如示例[s_srch2](E:\vsC语言\C语言学习\s_srch\s_srch.sln)中改变了原有的字符串的字符值。

```c
a=1;
a=i++;
/*即a=1，i=2*/

a=1;
a=++1;
/*a=2, i=2*/
i--;--i;/*同理*/
```

------

## 二、assert.h

此函数库只包含assert()函数，此函数名为断言函数。详细解释可看：[assert.h头文件详解 (biancheng.net)](http://c.biancheng.net/ref/assert_h/)

```c
assert(i==1);
/*i==1条件成立则继续执行程序，相反则中断程序并输出错误*/
```

------

## 三、指针与指针计算

指针必须被初始化，未初始化的指针即野指针，是极其危险的。

NULL指针，可以被被赋给一个指针，表示这个指针不指向任何位置。

如果不知道一个指针的类型可以将其定义为 void *p;

这种情况只能出现在同一数组中不同元素中，如果是不同数组中他们之间计算结果就是未定义的，即两个不相关的指针执行关系运算，其结果是未定义的。

*但是大多数编译器是不会检查指针表达式的结果是否取于合法的边界内。

指针的计算只有加减乘除(+ - * /)和关系操作符(< > >= <= !)。

```c
for(vp=&values[N_VALUES];vp>&values[0];)
	*--vp=0;
	
for(vp=&values[N_VALUES-1];vp>=&values[0];vp--)
	*vp=0;
```

上面两个例子相比，虽然*--vp的可读性较差，但是他在运算结束后不会越界，更加安全。

第二个例子问题在于，多一次运算，使vp的值在第一个元素被清除后，仍运算一次，这时vp>=&values[0]的值是未定义的，因为vp越界了。标准不允许这样比较！

------

## 四、逗号计算

```c
sum = ((a = 4 * 5, a + 2), a + 6);
```

输出结果是最后一个逗号后的结果，但是前面的式子仍会计算。

```c
ch = (ch >= 'A' && ch <= 'Z' ? ch + 32 : ch);
```

其效果相当于if else语句。

------

## 五、case

```c
switch(maonth)
case 1:... break;
...
default :break;
```

格式错误是不可以运行的！但是不会报错。

------

## 六、求值区别

```c
int i[10];
int *p = &i[0];
int offset;

p += offset; (a)
p += 3;      (b)
```

区别：即使offset跟后面一个表达式的字面值的值相等，但它比计算第一个表达式要消耗更多的时间，因为对整型大小的偏移量必须在运行时才知道，这是因为变量可能包括更多的值，而编译器不能在变量赋值之前知道它的值，另一方面，在编译的时候字面值3可以被缩放到一个整数中,结果在运行是添加到p，换句话说，第二个表达式可以简单的用12加上p，在四位机器上，不需要运行时的再相加。

------

## 七、递归

递归可以实现顺序输出整数的每位，利用`i+’0`‘可以实现整数向字符的转变。

虽然标准中未说明递归是运行在堆栈中，但是堆栈很适合运行递归，所以很多编译器也利用堆栈来运行递归。

递归很强大，但并不是所有时候都适用递归。如下：

```c
/*递归计算阶乘*/

long
factorial(int n);
{
	if(n<=0)
		return 1;
    else
        return n*factorial(n-1);
}


/*迭代计算阶乘*/

long
factorial(int n)
{
	int result=1;
    
    while(n>1)
    {
		result*=n;
        n-=1;
    }
    return result;
}
```

第一种是阶乘计算，第二种是迭代计算（迭代计算是尾部递归，但是不是递归，而是用的循环）。递归函数调用时将涉及一些运行时的开销——参数必须压到堆栈、为局部变量分配空间、寄存器的值必须保存等。

在计算斐波那契数列中：递归方法开销十分巨大，进行了大量的冗杂计算，效率低，但是其可读性在我看来还是高于迭代计算的 ，但其实迭代计算的可读性也并没有很差。利用很小的可读性优势换取降低大量效率的做法是不值当的，所以在利用递归之前应充分考率这么做是否会大幅度降低效率。

```c
/*递归计算斐波那契数*/

long
fibonacci(int n)
{
	if(n<2)
        return 1;
    else
        return fibonacci(n-1)+fibonacci(n-2);
}


/*迭代计算斐波那契数*/

long
fibonacci(int n)
{
	long result;
    long previous_result;
    long next_older_result;
    
    result=previous_result=1;
    while(n>2)
    {
		n-=1;
        previous_result=result;
        next_old_result=previous_result;
        result=prvious_result+next_old_result;
    }
    return result;
}
```

这个字符串参数必须包含一个或多个数字，函数应该把这些数字字符转换为整数并返回这个整数，如果字符串参数包含了任何非数字字符，函数就返回零。请不必担心算数溢出。提示：这个技巧很简单，你每发现一个数字，把当前值乘以10,并把这个值和新数字所代表的值相加。

```c
int ascii_to_integer(char *string)
{
    int result = 0;
    char *p = string;
    while(*p >= '0' && *p <= '9'){
        result *= 10;
        result += *p - '0';
        p++;
    }

    if(*p != '\0')
        result = 0;
    return result;
}
/*引发异常但不知道原因，可能是野指针，也可能是越位*/
```

利用*p-‘0’可以将字符型换为整型，这点可以去掌握。

书上例题六：[written_amount](E:\vsC语言\C语言学习\written_amount\written_amount.sln)

------

## 八、数组

### 1）数组和指针相比，指针比数组的下标计算更加有效率。

例：

```c
int array[10],a;
for(a=0;a<10;a+=1)
    array[a]=0;
```

下标计算中，在a+=1中需要先将a与整型的长度相乘，而a的数值是变化的，需要每次都要计算一次，从而影响效率。

```c
int array[10],*ap;
for(ap=array;ap<array+10;ap++)
    *ap=0;
```

指针计算中，在ap++中这里是1与整型的长度相乘，然后再与指针ap相加，而1是不变的，所以它只需要计算一次，从而提高效率。

例：

```c
a=get_value();
array[a]=0;

a=get_value();
*(array+a)=0;
```

这两例在效率上是没有区别的。

你编写程序的方法不但影响程序的运行时的效率，而且影响他的可读性。不要为了效率上细微的差别而牺牲可读性。

```c
void
strcpy(char *buffer, char const *string)
{
	while((*buffer++=*string++)!='\0')
        ;
}
```

### 2）eg：为什么第二个参数要书写为const？

1.这样是一个良好的书写习惯。使他人看到函数原型就明白，string不会被修改。

2.编译器可以捕捉到任何修改string数据的意外错误。

3.这类声明允许向函数传递const参数。、

```c
int strlen(char *string);
int strlen(char string[]);
```

声明相等，但是第一种表示更加准确。实参数组实际上是个指针。

```c
char message[]={'H','e','l','l','o',0};
char message[]="Hello";//字符数组
char *message="Hello";//字符串常量
```

```c
int matrix[6][10];
int* mp；
...
mp = &matrix[3][8];
printf("%d\n", *mp);
printf("%d\n", *++mp);
printf("%d\n", *++mp);
```

虽然这种写法通常不会出现什么问题，但它是非法的，所以应尽量避免。

```c
int   vector[10],*vp=vector;
int   matrix[3][10],*mp=matrix;
```

第一个声明是合法的，因为把vp声明为一个指向整型的指针，而数组也是指向整型的指针；

但是第二个声明是非法的，因为两者类型不同，mp是指向整型的指针，而matrix是指向整型数组的指针。

```c
int    (*p)[10]=matrix;
```

它使p指向matrix的第一行，p是一个指向10个整型元素的数组的指针。



如果需要一个指针指向数组的单个整型元素，即：

```c
int     *pi=&matrix[0][0];
int     *pi=matrix[0];
```

警告：

```c
int      (*p)[]=matrix;
```

是不允许的，它的值将根据空数组的长度调整（与0相乘），结果可能脱离预想。有的编译器可以发现错误但是有的不行。

作为函数参数的二维数组的函数原型：

```c
void func2(int (*mat)[10]);
void func2(int mat[][10]);
```

注意：

```c
void func2(int **mat);
```

在这里，mat是指向整型指针的指针，与指向整型数组的指针是不同的。

注意：

多维数组初始化：注意花括号和空格的使用；虽然不是必须但是可以使阅读变得简单,清晰结构和确定位置。

eg：

```c
int      two_dim[3][5]={
         {00,01,02,03,04},
         {10,11,12,13,14},
         {20,21,22,23,24}
};
```

```c
int      *api[10];
```

下标引用的优先级高于间接访问，所以是个数组，每个元素是指向整型的指针；

**下面的声明试图按照从1开始的下标访问数组data，它能行吗？

```c
int actual_data[20];
int *data = actual_data - 1;
```

不行，非法，当actual_data[0]时，*data=actual_data[0],非法。然而，在大多数机器上它是可以运行的！

下面的声明取自某个源文件：

```c
int a[10];
int *b = a;
```

但在另一个源文件中，却发现了这样的代码：

```c
extern int *a;
extern int b[];
int x,y;
...
x = a[3];
y = b[3];
```

请解释一下，当两条赋值语句执行时会发生什么？（假定整型和指针的长度都是4个字节）

answer：在第一个赋值语句中，编译器认为a是一个指针变量，所以它提取存储在那里的指针值，并加上12（3个整型长度），然后对这个结果执行间接访问操作。但a实际上是整型数组的起始位置，所以作为“指针”获得的这个值实际上是数组的第一个整型元素，它与12相加，其结果解释为一个地址，然后对它执行间接访问，作为结果，它将提取一些任意内存位置的内容，或者由于某种地址错误而导致程序失败。

第二个赋值中，编译器认为b是个数组名。所以它把12（3的调整结果）加到b的存储地址，然后间接访问操作从那里获得值，事实上b是一个指针变量，所以从内存中提取的后面三个字实际上是从另外的任意变量中取得的，这个问题说明了指针和数组虽然存在关联，但绝不会是相同的。

美国联邦政府使用下面这些规则计算1995年每个公民的个人收入所得税：

| If Your Taxable income is over | But not Over | Your Tax is    | of the Amout Over |
| ------------------------------ | ------------ | -------------- | ----------------- |
| $0                             | $23350       | 15%            | $0                |
| 23350                          | 56550        | 3502.50+28%    | 23350             |
| 56550                          | 117950       | 12798.50+31%   | 56550             |
| 117950                         | 256500       | 31832.50+36%   | 117950            |
| 256500                         | -            | 81710.50+39.6% | 256500            |

编写程序输出最后税收tax。

利用查表法。[single_tax.c](E:\vsC语言\C语言学习\single_tax\single_tax.sln)

编写一个名叫identity_matrix的函数，它接受任意大小整型矩阵为参数，并返回一个布尔值，提示该矩阵是否为单位矩阵。

```c
#define FALSE 0
#define TURE  1

int identify_matrix(int *matrix,int n)
{
	int row;
    int column;
    for(row=0;row<n;row++)
    {
		for(column=0;column<n;column++)
        {
			if(*matrix++!=(row==column))
                return FALSE;
        }
    }
    return TURE
}
```



------

## 九、typeof

typeof不是c语言本身的关健词或运算符，他是GCC的扩展。

typeof()中可以是任何有类型的东西，变量就是其本身类型，函数是它返回值的类型。一般用于声明变量。通常用于宏定义，示例：

```c
int fun(int a);
typeof(fun)*fptr;	//int (*fptr)(int);

typeof(int *)a,b;	//int *a,*b;
typeof(int)*a.b;	//int *a,b;
```

typeof()其中的变量是不能包含存储类说明符的，如static，extern这类都是不行的。

## 十、typedef

typedef关键字来定义数据类型名称，来代替系统默认的基本类型名称、数组类型名称、指针类型名称与用户自定义的结构型名称、共用型名称、枚举型名称等。

### 1）为基础数据类型定义新的类型名

```c
typedef unsigned int COUNt;
typedef int RAEL;
typedef long double RAEL;
typedef double RAEL;
typedef float RAEL;
typedef char RAEL;
```

便于跨平台移植程序，只需要修改typedef的定义即可。

### 2）为自定义数据类型（结构体、共用体和枚举类型）定义简洁的名称

```c
typedef struct tagPoint
{
    double x;
    double y;
    double z;
} Point;
```

定义一个结构体tagPoint，并为他起个别名：Point；

```c
Point oPoint1={100,100,1};
Point oPoint2;
```

声明这样一个结构体：

```c
typedef struct tagNode
{
    char *pItem;
    pNode pNext;
} *pNode;
```

但是编译器会报错，因为在结构体为建立完成时编译器并不认识pNode，于是问题出现了。

为解决问题我们可以：

```c
typedef struct tagNode
{
    char *pItem;
    struct tagNode *pNext;
} *pNode;
```

或者：

```c
typedef struct tagNode *pNode;
struct tagNode
{
    char *pItem;
    pNode pNext;
} ;
```

这种方式虽然编译器支持，但是我们应该尽量避免，我们用typedef定义一个并未完全声明的类型tagNode。建议规范：

```c
struct tagNode
{
	char *pItem;
    struct tagNode *pNext;
};
typedef struct tagNode *pNode;
```

### 3）为数组指针定义简洁的名称

```c
typedef int INT_ARRAY_100[100];
INT_ARRAY_100 arr;

typedef char *PCHAR;
PCHAR pa;

//PFun是我们创建的一个类型别名
typedef int *(*PFun)(int,char*);
//使用定义的新类型来声明对象，等价于int*(*a[5])(int,char *);
PFun a[5];
```

### 4）typedef带来的陷阱

```c
typedef char *PCHAR;
int strcmp(const PCHAR,const PCHAR);
```

在上述例子中，“const PCHAR” 是否相当于 “const char*” 呢？

不是，因为const给予了整个指针本身常量性，也就是形成了常量指针（char *const"一个指向char的常量指针"），而不是（const char *“指向常量的指针”）

利用以下代码可以实现const char *

```c
typedef const char *PCHAR;
int strcmp(PCHAR,PCHAR);
```

还需要特别注意的是，虽然 typedef 并不真正影响对象的存储特性，但在语法上它还是一个存储类的关键字，就像 auto、extern、static 和 register 等关键字一样。因此，像下面这种声明方式是不可行的：

```c
typedef static int INT_STATIC;
```

原因是不能声明多个存储类关键字，由于 typedef 已经占据了存储类关键字的位置，因此，在 typedef 声明中就不能够再使用 static 或任何其他存储类关键字了。

## 十一、stddef.h

### 1）变量类型

#### ptrdiff_t

这是有符号的整数类型，他是两个指针相减的结果。

[ptrdiff_t](E:\vsC语言\stddef.h\ptrdiff_t\ptrdiff_t.sln)

#### size _t

这是无符号整型类型，他是sizeof(计算长度大小)关键字的结果。

[size_t](E:\vsC语言\stddef.h\size_t\size_t.sln)

#### wchar_t

这是一个宽字符常量(类似中文日文等占两字节的文字)大小的整数类型。

[wchar_t](E:\vsC语言\stddef.h\wchar_t\wchar_t.sln)

### 2）库宏

#### NULL

这个宏是一个空指针常量的值。

#### offsetof(type,member-designator)

这会生成一个类型为size_t的整型常量，他是一个结构成员对于结构开头的字节偏移量(每一个成员位置相对于结构体首地址的距离的长度)。成员由member-designator给定的，结构的名称是在type中给定的。

[offsetof](E:\vsC语言\stddef.h\offsetof\offsetof.sln)

## 十二、string.h

### 1）strlen

```c
size_t   strlen(char const *string);
```

**计算string的长度，返回值为size_t型(无符号整型)！**所以会导致以下称情况：

```c
if(strlen(x)>=strlen(y))...
if(strlen(x)-strlen(y)>=0)...
```

它们看起来是相等的，但实际情况是第2条语句永远无法为真！但如果将其返回值强制转化为int，问题可以解决！

```c
/*
**计算字符串参数长度
*/
#include<stddef>

size_t
strlen(char const *string)
{
	int length;
    
    for(length=0;*string++ != '\0';)
        length+=1;
    
    return length;
}
```

### 2）不受限制的字符串函数

#### 1.strcpy

```c
char  *strcpy(char *dst,char const *src);
```

**将src字符串复制到dst！**但如果dst和src在内存中出现重叠，结果为未定义。

但是必须保证目标数组的空间足以容纳src，否则超出覆盖原先存储于数组之后的内存空间的值。

#### 2.strcat

```c
char  *strcat(char *dst,char const *src);
```

**将src连接到dst之后！**但如果dst和src在内存中出现重叠，结果为未定义

//2）和3）的返回值均为dst的副本，就是指向目标数组的指针。

#### 3.strcmp

```c
int  strcmp(char const *s1,char const *s2);
```

**进行字符串比较！如果s1小于s2，函数返回值小于0；如果二者相等，则返回值为0；如果s1大于s2，函数返回值大于0。**

### 3）长度受限的字符串函数

```c
char  *strncpy(char *dst,char const *src,size_t len);
char  *strncat(char *dst,char const *src,size_t len);
int   strncmp(char const *s1,char const *s2,size_t len);
```

和strcpy一样，strncpy最多向dst复制len个字符。如果strlen(src)小于len，则用NUL来补齐；如果大于，则只有len个字符复制到src。**注意！它的结果将不会以NUL字节结尾。**

和strcat一样，strncat最多复制len个字符到dst之后。但是strncat不会用NUL来填充补齐。**它不管留下的空间是否足够！**

strncmp最多比较len个字符！

### 4）字符串查找

#### 1.strchr和strrchr

```c
char  *strchr(char const *str,int ch);
char  *strrchr(char const *str,int ch);
```

strchr查找str中**第一次**(最左边)出现ch的位置，找到后返回一个指向该位置的指针！

strrchr查找str中**最后一次**(最右边)出现ch的位置，找到后返回一个指向该位置的指针！

#### 2.strpbrk

```c
char  *strpbrk(char const *str,char const *group);
```

strpbrk返回一个指向str中**第一次**匹配group中**任何一个字符**的字符的位置！

未找到返回一个NULL指针！

#### 3.strstr

```c
char  *strstr(char const *s1,char const *s2);
```

strstr在s1中查找s2**第一次完整**出现的位置，并返回一个指向该位置的指针；

如果没有匹配的返回NULL指针；

**如果s2为空字符串，则返回s1。**

标准库中并不存在strrstr和strrpbrk，但我们可以很容易实现：

```c
#include<string.h>

char *
my_strrstr(char const *s1,char const *s2)
{
	register char *last;
    register char *current;
    
    /*
    **把指针初始化为上一次匹配的位置
    */
    last=NULL;
    /*
    **只在第二个字符串不为空时才进行查找，如果s2为空，返回NULL
    */
    if(*s2!='\0')
    {
        /*
        **查找s2在s1中第一次出现地方
        */
        current=strstr(s1,s2);
         /*
    	**每次找到字符串时，让指针指向它的起始位置，然后查找下一次匹配位置
   		*/
    	while(current!=NULL)
    	{
			last=current;
        	current=strstr(last+1,s2);
    	}
    }
    /*返回指向找到的最后一次匹配的起始位置的指针*/
   return last;
}
```

### 5)strspn,strcspn和strtok

#### 1.strspn和strcspn

```c
size_t  strspn(char const *str,char const *group);
size_t  strcspn(char const *str,char const *group);
```

strspn函数返回一个整数值，该值指定 str 中**完全由group中的字符组成的子字符串的长度**。如果 str 以不在group中的字符开头，则该函数返回 0。

strcspn函数返回str中str中第一个字符的位置，该字符位于group中。如果str中没有字符在group中，则返回值为 str 的长度。

#### 2.strtok

```c
char  *strtok(char *str,char const *sep);
```

sep是个字符串，定义了用作分隔符的字符集合。strtok函数找到**str的下一个标记**，并将其用NUL结尾，然后返回一个指向这个标记的指针。

如果strtok第一个参数不是NULL，函数将找到字符串的第一个标记。strtok同时将保存他在字符串的位置。如果第一个参数是NULL，函数就在同一个字符串中这个被保存的位置开始像前面那样查找下一个标记。如果不存在返回NULL。

典型情况下：

```c
/*
**从一个字符数组中提取空白字符分隔的标记并把它们打印出来（每行一个）
*/
#include<stdio.h>
#include<string.h>

void
print_tokens(char *line)
{
    static char whitespace[]=" \t\f\r\v\n";
    char *token;
    
    for(token=strtok(line,whitespace);
       token!=NULL;
       token=strtok(NULL,whitespace));
    printf("Next token is %s\n",token);
}
```

### 5)strerror

```c
char  *strerror（int error_number);
```

strerror函数把其中一个错误代码作为参数并返回一个指向用于描述错误的字符串的指针。

### 6）内存操作

#### 1.memcpy

```c
void  *memcpy(void *dst,void const *src,size_t length);
```

memcpy函数从src的起始位置复制**length个字节**到dst内存的起始位置。如果src和dst的内存出现重叠，结果为未定义。

但是如果两个数组均为整型数组：参数不需要进行强制转换，因为函数原型为void*型指针，

```c
memcpy(saved_answers,answers,count *sizeof(answers[0]));
```

#### 2.memmove

```c
void  *memmove(void *dst,void const *src,size_t length);
```

memmove函数和memcpy函数行为差不多，但是memmove函数的源操作数和目标操作数可以重叠。

操作过程：把源操作数复制到一个临时位置，这个临时位置不会与源操作数和目标操作数重叠，然后再从临时位置复制到目标操作数。

#### 3.memcmp

```c
void  *memcmp(void const *dst,void const *src,size_t length);
```

memcmp函数是比较dst和src的，如果memcmp函数用于比较不是单字节的数据(整数或浮点数)，就可能给出不可预料的结果。

#### 4.memchr

```c
void  *memchr(void *dst,int ch,size_t length);
```

memchr函数是从dst中查找ch第一次出现的位置并返回一个指向该位置的指针，最多查找length个字节。

#### 5.memset

```c
void  *memset(void *a,int ch,size_t length); 
```

memset函数把从a开始的length个字节都设置成ch。

## 十三、ctype.h

### 1.字符分类

| 函数     | 如果它的参数符合下列条件就返回真                             |
| -------- | ------------------------------------------------------------ |
| iscntrl  | 任何控制字符                                                 |
| isspace  | 空白字符：空格‘ ’、换页‘\f’、换行'\f'、回车'\r'、制表符'\t'或垂直制表符'\v' |
| isdigit  | 十进制数字0-9                                                |
| isxdigit | 十六进制数字，包括所有十进制数字、小写字母a-f、大写字母A-F   |
| islower  | 小写字母                                                     |
| isupper  | 大写字母                                                     |
| isalpha  | 字母大小写都有                                               |
| isalnum  | 字母或数字                                                   |
| ispunct  | 标点符号，任何不属于数字或字母的图形字符（可打印符号）       |
| isgraph  | 任何图形符号                                                 |
| isprint  | 任何可打印字符，包括图形字符和空白字符                       |

### 2.字符转换

```c
int tolower(int ch);大写转小写
int toupper(int ch);小写转大写
```

利用这些函数可以提高程序的可移植性！

## 十三、动态内存分配

### 1.函数

```c
void *malloc(size_t size);//分配内存
void free(void *pointer);//释放内存
void *calloc(size_t num_elements,size_t element_size);//分配内存，两个参数：元素长度，每个元素大小
void realloc(void *ptr,size_t new_size);//修改内存
```

### 2.错误及解决

```c
/*
**定义一个不易发生错误的内存分配器。
*/
/*alloc.h*/
#include<stdlib.h>

#define malloc                  /*不要直接调用malloc*/
#define MALLOC(num,type)  (type *)alloc( (num)* sizeof(type) )
extern void *alloc(size_t size);
```

**程序11.1a  错误检查分配器：接口**

```c
/*
**不易发生错误的内存分配器的实现
*/
/*alloc.c*/
#include<stdio.h>
#include"alloc.h"
#undef   malloc
void *
alloc(size_t size)
{
	Void *new_mem;
	/*
	**请求所需的内存，并检查确实分配成功。
	*/
    new_mem=malloc(size);
	if(new_mem==NULL)
    	{
		printf("out of memory!\n");
   		exit(1);
    	}
	return new_mem;
}
```

**程序11.1b  错误检查分配器：实现**

```c
/*
**一个使用很少引起错误的内存分配器的程序。
*/
/*a_client.c*/
#include "alloc.h"

void 
function()
{
	int *new_memory;
    
    /*
    **获得一串整型数的空间。
    */
    new_memory=MALLOC(25,int);
}
```

**程序11.1c  使用错误检查分配器**

## 十四、实现多键位输入

```c
#define KEY_DOWN(VK_NONAME) ((GetAsyncKeyState(VK_NONAME) & 0x8000) ? 1:0)
```

搭配虚拟键值表使用，此宏定义可以实现同时输入。

## 十五、easyx颜色

```c
常量				值		 颜色
BLACK			0			黑
BLUE			0xAA0000	蓝
GREEN			0x00AA00	绿
CYAN			0xAAAA00	青
RED				0x0000AA	红
MAGENTA			0xAA00AA	紫
BROWN			0x0055AA	棕
LIGHTGRAY		0xAAAAAA	浅灰
DARKGRAY		0x555555	深灰
LIGHTBLUE		0xFF5555	亮蓝
LIGHTGREEN		0x55FF55	亮绿
LIGHTCYAN		0xFFFF55	亮青
LIGHTRED		0x5555FF	亮红
LIGHTMAGENTA	0xFF55FF	亮紫
YELLOW			0x55FFFF	黄
WHITE			0xFFFFFF	白
```

