```c
println("Hello, World!  你好，世界！")
```

## 什么是 Drift 脚本？

我是在 2021 年初设计的 Drift 语言。在经过两个版本的重构后，有了现在较为完善的版本。

> Drift: 漂流、无所谓的、顺其自然，无目的的做。

### 前世今生

第一版使用 C++ 进行开发，完成了继承，基本语法，虚拟机等，这一版之所以被重构，是因为我认为细节实现不太好，C++ 范式不利于后续开发。

第二版使用 Go 进行开发，更改语法为 S 语法类型（例如 Lisp），并结合命令式语言的思路进行编程，太过做作，丢弃。

### 目前实现

在 2021 年七月份，正值暑假。我利用一个星期的时间，使用 C99 编写了 Drift 的解释器。代码总共不超过 5000 行。

由于些许细节（泛型，垃圾回收）是第一次经过代码实践，后续花了非常多的时间进行优化，以及修复问题。

我们的 GitHub 团队：https://github.com/drift-lang &emsp; 官方主页：https://drift-lang.fun

Drift 语法精简，类似于 Python 的缩进语法，整个关键字只有 12 个，不包括其他编译类型，可以把内建函数和内建类型当作变量来定义。

### 解决问题

**Drift 不解决什么问题。**开始计划的是学习，能够根据我的理念来使用一门编程语言，用它做我的脚本语言。

主要工作在后端开发，我喜欢 Go 的设计，所以引入了鸭子类型（类不需要显示继承），泛型，泛型在运行期而不在编译期，之后会优化或者砍掉。

非常高兴的为你介绍我开发的一个玩具语言。

### 安装

Drift 解释器全部使用 C 语言开发，非常精简，执行文件在 200 kb 以内，不包含代码信息在 100 kb 以内。

> 需要的环境：GCC（大部分版本支持），Python 解释器。

目前需要自己构建，广泛支持的环境都可以编译成功。

```shell
git clone https://github.com/drift-lang/drift.git
cd drift
python build.py
```

即得到执行文件，Drift 的解释器。

请把它的路径放到系统的环境变量里，以便系统能够找到 drift 命令。

#### macOS

macOS 系统默认使用 clang 作为编译器，并强制的替换了 gcc 命令。如果使用 clang 不能构建成功，请安装 gcc 编译器。

```shell
brew install gcc
ln -s /usr/local/Cellar/gcc/bin/gcc gcc # 软链接到当前目录
vim build.py # 替换变量 GCC 编译器的路径为软链接
```

#### Windows

推荐使用 WSL 或虚拟 Linux 环境，不推荐使用 mingGW 构建。

请参照构建方式自行构建。

#### Linux

例举使用 Debian apt 系的包管理。

```shell
sudo apt -y install gcc
sudo apt -y install python3
```

#### 其它

只要使用 gcc 和 python 就能够构建解释器。

后续可能会提供下载解释器的页面，移除 Python 脚本来构建的方案，并且标准库已经完善支持常用系统接口。

### 你好，世界！

试着使用 vim，emacs，notepad 或任意工具编写以下代码并保存为 <code>hello.ft</code> 文件。

```c
println("hello, drift!")
```

打开任意 shell，bash，zsh 等终端工具，执行以下命令。

```shell
drift ./hello.ft
```

接着会在屏幕上打印字符 "hello, drift!" 并且退出程序。

### 在线玩

访问：https://play.drift-lang.fun

### 命令参数

Drift 还未支持 REPL 环境，目前可以直接执行 drift 命令得到提示信息。

```shell
usage: drift FILE(.ft) <option>

command:
  token       show lexical token list
  op          show bytecode
  tb          after exec, show environment mapping

version:  Drift 0.0.1 (MADE AT Jul 2021 29, 15:40:45)
license:  GNU General Public License GPL v3.0
           @ bingxio - bingxio@qq.com
```

| 参数 | 作用 |
|-|-|
|token|显示当前源代码的词法列表|
|op|显示当前源代码的字节码|
|tb|显示当前源代码的运行后环境|

### 语法

#### 类型系统

Drift 支持以下数据类型提供编码。

##### 整型

整型被编译成<code>int</code>类型。为 0 - 9 的数字。

暂未支持十六进制 0x 的解析。

##### 浮点型

浮点型为带小数的高精度类型，它被编译成<code>float</code>类型。

```c
3.5
3.
```

##### 字符串

字符串类型为使用双引号括起来的任意字符序列，它被编译成<code>string</code>类型。

```c
"abc3.5Goodluck!"
```

##### 字符型

字符类型为使用单引号括起来的单个 ascii 字符，它被编译成<code>char</code>类型。

```c
'p' '@' '😀'
```

##### 布尔型

布尔型为内建类型，是在运行时里定义的类型，用户可以直接使用。它被编译成<code>bool</code>类型。

```
true  # 真
false # 假
```

##### 数组

数组为任意类型的序列，它被编译成<code>[]T</code>类型。

```
[1, 2, 3]
```

##### 元组

元组为任意类型的不可变序列，它被编译成<code>()T</code>类型。

```
(1, 2, 3)
```

##### 字典

字典为固定键值的键值对类型序列，它被编译成<code>{}< T1, T2 ></code>类型。

```
{a: 2, b: 4, c: 7}
{"name": "bingxio", "age": 20}
```

##### 函数类型

Drift 可以支持函数作为参数，和函数作为值返回。

```
def () show -> |int, int| -> int
    ret def (a, b int) _ -> int
        ret a + b

println(show())       # function _
println(show()(3, 5)) # 8

def fn |int, int| -> int = show()
println(type(fn)) # function

def (fn || -> string) show
    print(fn())

def () a -> string
    ret "hello, "

def () b -> string
    ret "world!"

show(a)
show(b)
```

##### 字面量

其他定义的任何类名，接口名，模块，为用户字面量类型。

##### 空类型

空类型使用<code>nil</code>定义，为零值。

##### 任意类型

任意类型使用 any 定义，为任意类型，编译器不做类型检查。

```
def x any = nil
x = 123
x = true
x = 4.5
```

#### 语句系统

我不太明白 BNF、EBNF 等的语义表达，所以直接使用例子。

都非常简单。

##### 注释

注释使用<code>#</code>符号开始，到换行符结束。

语法分析会忽略这一行代码。

##### 保留字

Drift 使用了 12 个保留字，特定使用它们来定义语义。

|def|ret|for|aop|if|ef|nf|new|out|go|use|nil|
|-|-|-|-|-|-|-|-|-|-|-|-|


##### 运算符

Drift 支持以下运算符。

|+|-|*|/|%|->|<-|.|,|:|=|;|>|<|>=|<=|&|\||!|!=|==|{|}|(|)|[|]|_|::|\\|
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|

##### 变量

变量使用<code>def</code>关键字定义。

```
def x int = 20
def y string = "hello, world!"
println(x, y)
```

##### 条件

逻辑条件可通过<code>if</code>，<code>ef</code>，<code>nf</code>等语句来使用。

使用单个<code>if</code>表达式，多个<code>ef</code>表达式，单个<code>nf</code>表达式。为如果，或者如果，都不是。

```
def a int = 20
def b int = 30

if a > b
    println("CALL A")
ef a == 10
    println("CALL B)
ef a == 20
    println("CALL C")
  if 1 + 1 == 2
      println("CALL D")
nf
    println("ERROR")
```

##### 循环

循环为固定执行某个语句块。使用<code>aop</code>，<code>for</code>语句定义。

```
def i int = 0
aop i < 100
    if i % 2 == 0
        println("i =", i)
    i = i + 1

for def i int = 0; i < 10; i = i + 1
    if i % 2 == 0
        println("i =", i)
```

循环控制使用<code>go</code>，<code>out</code>语句定义。

<code>go</code>为满足条件前进，<code>out</code>为满足条件退出，都支持不带条件退出或前进。

```
# 输出 0，1，2，3，4
for def i int = 0;
      i < 10; i = i + 1
    if i == 5
        out ->
    println(i)

# 输出 0，1，2，3，4，6，7，8，9
for def i int = 0;
    i < 10; i = i + 1
  if i == 5
    go ->
  println(i)

# 输出 0，1，2，3，4，6，7，8，9
for def i int = 0;
      i < 10; i = i + 1
    go i == 5
    println(i)

# 输出 0，1，2，3，4
for def i int = 0;
      i < 10; i = i + 1
    out i == 5
    println(i)
```

##### 迭代

使用 for 关键字迭代一个数组，循环控制同上。

```
def () test -> []int
    def list []int = []

    for def i int = 0; i < 20; i = i + 1
        append(list, i * 2)
    ret list

for x <- test()
    if x % 8 == 0
        println("mod(8): ", x)
        go ->
    println(x)
```

##### 函数

使用<code>def</code>保留字定义函数。

函数为某个固定代码块，可以接收参数，返回值，并在代码块内进行任意处理。

```
# 不带参数
def () show
    println("CALL")
show()

# 带参数
def (a, b int, c string) show
    println(a, b, c)
show(1, 2, "OK")

# 带参数，带返回
def (a int, b int) add -> int
    ret a + b
def x int = add(3, 8)
println(x) # 11

# 多参数，位置只能为最后参数
def (a int, b <-string) show
    println("A =", a, "B =", b)
show(
    23, "a", "hello?", "who!", "good")
```

##### 类

使用<code>def</code>保留字定义类。

类为某个固定的代码块，接收实例成员，代码块内包含成员，成员方法。

```
def User
    def name string = nil
    def age int = nil

    def tip = "USER"

    def () login -> {}<string, any>
        def m {}<string, any> = {
            "name": .name,
            "age" : .age
        }
        ret m
def u User = new User{name: "bingxio", age: 20}
println(u, u.tip, u.login())
```

使用<code>new</code>保留字实例某个类的成员。

使用<code>.</code>运算符可在类成员里获取外部成员。

##### 接口

Drift 使用<code>def</code>定义接口，使用鸭子类型（不需要显示继承）。

```
def Person
    \string\ say    # 接口方法
    \\ eat -> bool  # 接口方法

def Me
    def age int = nil

    def (word string) say
        println("say:", word)
  
    def () eat -> bool
        ret .age == nil | .age < 6

# 实例化给接口
def p Person = new Me{age: 20}
println(p.age, p.say("Hello"), p.eat())

# 使用参数传递
def (p Person) show
    p.say("What?")

show(new Me{})
```

##### 泛型

泛型区别于<code>any</code>类型的特征是，编译器会做类型检查，并在编译器生成代码，实现运行零开销。

Drift 的泛型支持类成员，接口成员，和函数参数。

```
def Foo<T> # 接口可以不必定义其类型
    \T, T\ show -> string

def Bar<T int | float>
    def x T = nil

    # 此处规范为接口的方法
    def <T any>(a, b T) show -> string
        ret type(a) + " " + type(b)

def a Bar = new Bar{x: 20}
def b Bar = new Bar{x: 8.3}

println(type(a.x), type(b.x)) # int float
println(
    a.show('p', 34), # char int
    b.show(1.2, "?") # float string 
) 
```

##### 异常

Drift 支持抛出异常和定义异常。

使用类似管道的方式定义和接收异常的数据。下例，定义了名称为 e 的异常块，在类成员方法 show 中被抛出，且传入了一个字符串数据给异常块，此时，它将被输出。抛出异常后不执行后续代码，show 方法的返回值是 nil。

```
e ->
    println("exception:", e)

def Foo
    def (a, b int) show -> int
        if a == 3
            e <- "hello, world!"
        ret a * b

def f Foo = new Foo{}
println(f, f.show(3, 5))
```

##### 模块

任何一个<code>.ft</code>代码文件为一个模块。

使用<code>use</code>保留字引入某个模块，使用<code>\\</code>符号作为路径分割，使用<code><-</code>运算符引入所有成员，使用<code>::</code>运算符引入模块成员。

```
#a.ft
#~/demo/inner/a.ft
def () show
    println("CALL INNER")

#b.ft
#~/demo/b.ft
use inner\a

println(a, a::show)
a::show()

#c.ft
#~/demo/c.ft
use <-inner\a

println(show)
show()
```

##### 内建变量

目前内建的变量有 2 个。

|名称|值|
|-|-|
|true|1|
|false|0|

##### 内建函数

目前内建的函数有 11  个。

|名称|定义|作用|
|-|-|-|
|println|println(obj <-any)          |输出多个任意对象并最后换行|
|print  |print(obj <-any)            |输出多个任意对象最后不换行|
|putline|putline(obj <-any)          |输出多个任意对象，每输出换行|
|put    |put(obj any)                |输出一个任意对象，不换行|
|len    |len(obj any) -> int         |返回对象长度|
|type   |type(obj any) -> string     |返回对象类型字符串|
|sleep  |sleep(m int)                |程序暂停毫秒|
|rand   |rand(start, end int) -> int |返回指定范围的随机数|
|append |append(arr []any, n any)    |数组追加|
|remove |remove(arr []any, i int)    |移除数组指定位置的元素|
|input  |input() -> string           |从系统获取输入字符串|

##### 与 C 语言交互

Python 的底层支持为 Modules 文件夹，定义其标准库，代码行数 30 万行左右。

Drift 参照 Lua 的方式不去自己实现所以标准库，用户或者 PR 实现。请使用 C 语言接入 Drift 的头文件，进行对接编程，实现函数，类，模块给 Drift 使用。

```c
//lib.c
#include <stdio.h>

#include "src/vm.h" /* 引入虚拟机头文件 */

/* 用户数据 */
typedef struct { char *name; int age; } user;

/* foo 函数，推入实参的数量到栈 */
void foo(keg *arg) { push_stack(new_num(arg->item)); }

/* add 函数，接收 2 个参数，并退入它们的和到栈 */
void add(keg *arg) {
    int b = check_num(arg, 0);
    int a = check_num(arg, 1);
    push_stack(new_num(a + b));
}

/* new_user 函数，推入一个用户数据 */
void new_user(keg *arg) {
    user *u = malloc(sizeof(user)); /* 声明一块内存 */
    u->name = "bingxio";
    u->age = 20;
    push_stack(new_userdata(u)); /* 推入用户对象 */
}

/* get_name 函数，接收一个用户参数，推入它的 name 参数 */
void get_name(keg *arg) {
    user *u = check_userdata(arg, 0); /* 检查首个参数为用户参数 */
    push_stack(new_string(u->name));
}

/* get_age 函数，接收一个用户参数，推入它的 age 参数 */
void get_age(keg *arg) {
    user *u = check_userdata(arg, 0);
    push_stack(new_num(u->age));
}

/* 模块定义 */
reg_mod *bar() {
    reg_mod *m = new_mod("bar");  /* 使用 new_code 初始化模块结构，并给入名称 */
    emit_member(m, "x", C_VAR);         /* 设置成员，名称为函数名称 */
    emit_member(m, "show", C_METHOD);   /* 设置成员方法，名称为函数名称 */
    return m;
}

/* 模块成员，推入一个字符串 */
void x() { push_stack(new_string("i am x~")); }

/* 模块方法，输出参数数量 */
void show(keg *arg) { printf("show~ args: %d\n", arg->item); }

/* 注册的函数列表 */
static const char *user_func[] = {
    "foo",
    "add",
    "new_user",
    "get_name",
    "get_age",
    NULL,
};

/* 注册的模块列表 */
static const char *user_mods[] = {"bar", NULL};

/* 初始化函数，虚拟机最先调用 */
void init() {
    reg_c_func(user_func); /* 注册函数 */
    reg_c_mod(user_mods);  /* 注册模块 */
    reg_name("cn", new_char('p')); /* 注册名称 */
}
```

Drift 使用动态库的方式接入，编译动态库。

```bash
gcc -fPIC -shared lib.c -o lib.so
```

虚拟机初始化时，加载同目录下所有<code>.so</code>后缀的动态库文件，并执行内部的<code>init()</code>函数。

- reg_c_func(); 注册函数，它接收字符串列表，对应为函数名称。
- reg_c_mod();  注册模块，它接收字符串列表，对应为模块名称。
- reg_name();   注册名称，首个参数为名称，第二个为数据对象。



- 函数的定义必须接收一个<code>keg *</code>的指针，它是虚拟机给入的实参列表。
- 模块的定义必须返回一个<code>reg_mod *</code>的指针，它是模块结构。

每个动态库文件加载后，会初始化一个名称、函数、或模块。自由使用 C 语言定义。

测试代码。

```
println("OK", foo)
def i int = foo(1, 2, 3)
println("i =", i)
def sum int = add(3, 6)
println("SUM =", sum)

println(bar, bar::x, bar::show)
bar::show([1, 2, 3], 666)

def x any = new_user()
println(x, get_name(x), get_age(x))

println(cn)
```

https://github.com/drift-lang/drift/blob/main/src/vm.h#L69

https://github.com/drift-lang/drift/blob/main/src/object.h#L153

#### 编译系统

最早的博文定义了基础 Drift 语法：https://bingxio.fun/article/2021-05-24.html

##### 词法分析

词法使用经典结构定义。

- 类型
- 字面量
- 行号

https://github.com/drift-lang/drift/blob/main/src/lexer.c

https://github.com/drift-lang/drift/blob/main/src/token.h

##### 语法分析

语法分析使用 TDOP Pratt 算法。

参阅我的博文：https://bingxio.fun/article/2021-07-19.html

论文：https://tdop.github.io/

**Drift 不生成 AST (Abstract syntax tree)。**

##### 字节码

Drift 的字节码为一个枚举序列，单个字节码的类型为 1 byte，对应 uint8_t 类型。

https://github.com/drift-lang/drift/blob/main/src/opcode.h

##### 虚拟机

Drift 使用从头遍历字节码进行求值。

其中某些算法（编译对应行号，条件跳转，类调用，泛型实现）等，请参阅我的博客。https://bingxio.fun

##### 垃圾回收

Drift 使用对象引用计数（RC）的方式回收对象内存。

博文：https://bingxio.fun/article/2021-08-11.html

#### PR

欢迎在 Github 仓库提交 Issue 和 Pull request！！