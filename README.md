# ***MeiLang编译器 alpha01***



## meic.exe : 用于将高级语法编译为nasm汇编语法 （a.mei  ->  a.asm）

## nasm.exe : 用于将生成的汇编语法汇编为对象文件 （a.asm  ->  a.o）(nasm官网)[https://www.nasm.us]

## lld-link.exe : 属于llvm工具链，用于win平台的链接工具，输入对象文件，输出可执行文件（a.o  ->  a.exe）(LLVM官网)[https://llvm.org]


##### HelloWorld示例：

```cpp

class Main{ /*和Java一样，主类名需和文件名一致，但不强制public*/
    func main(){
        var str1:char = "HelloWorld!",ENDL,0; /*用逗号分割就是字符数组，需要自行写0结束符*/
        call CLIB.printf(str1);
        return 0; /*返回0，不用写返回类型，会自己推断*/
    }
}

```
注意！暂不支持函数值直接传递，只能通过变量传递

##### 注释：
```cpp

\*仅支持这种的多行注释*\

```

##### 数据类型：
支持char i16 i32 i64 后续我会补上浮点数的缺口的
复合类型 用`,`分割的就是数组，char类型本身会生成字符数组

##### 关键点！
·MeiLang的输出是nasm汇编语言，所以只能在x86_64运行！而且我现在只做了一个Windows版本·

·主类的名字必须和文件名相同，否则会让编译器找不到入口·
·暂不支持字符串直接传递给函数，可以传递字符串变量给函数·

·var 定义的变量是全局变量，预计下一个版本我会加入局部变量·
·如果把一个整形变量直接传递给函数，一般情况会被认作是这个变量的指针，可以使用中括号包起来，比如call CLIB.printf(str1,[num1]);

·在终端编译时，不支持直接传入文件名字，如果就是当前目录，需要写./，比如： `meic ./Main.mei`  而且， `.\`也是不支持的·
·最后就是我之前说powershell用不了，经过我实测，可以用·

##### 额外 
·目录里的 Main.mei 是一个示例代码，你可以试着跑一下 .\meic.exe ./Main.mei


##### 2025.7.22 更新
1.新增macro关键字去，用于宏函数定义，前提必须定义在主函数之前，默认全局宏函数

2.新增final类型，虽然是变量的新类型，实际上是用来定义常量的。 优点是，final常量支持四则运算和小括号，会在编译时求值 相当于C++的 constexpr int

3.新增asmcode_data 关键字，与asmcode关键字不同，它不是用来嵌入汇编代码的，而是在头部来标注数据的，比如可以用它来声明一个外部函数，初始化一块内存空间等等

演示新功能（Main.mei）：
```cpp
class Main{
    macro print 0-*{ 
        /* 宏函数定义演示，其中0-*表示的是传参的数量，像0-*表示0到无数个，0-3就表示0到3个，也可以直接写固定值     传参依次是 %1，%2,%3... */
        call CLIB.printf(%1,%2,%3,%4);
    }
    func main(){
        var format:char = "Number: %d %d %d",ENDL; /*现在的char数组结尾会自动补上空字符*/
        var num1:final = 1;
        var num2:final = (4 / 2)*3-(4+5); /*final常量支持四则运算*/
        var num3:i32   = 29; /*普通的int，不支持四则运算，传给函数时需要[]包裹取值*/
        call print!(format,num1,num2,[num3]); /*宏函数调用要在函数名后面加一个! （感叹号）*/
        return 0;
    }
}
```

输出：
```
Number: 1 -3 29
```
