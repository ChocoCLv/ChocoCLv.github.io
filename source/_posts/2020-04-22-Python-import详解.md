---
title: Python import详解
comments: true
typora-root-url: ../../source/
date: 2020-04-22 15:40:32
tags:
- Python
- import
- 转载
---

> 原文地址 https://blog.csdn.net/weixin_38256474/article/details/81228492?fps=1&locationNum=2

如需转载请注明出处。  
win10+Python 3.6.3

一旦使用**多层文件架构**就很容易遇上 import 的坑！哈哈。

**一、理解一些基本概念**
--------------

**1、模块、包**  
**模块 module：**一般情况下，是一个以.py 为后缀的文件。其他可作为 module 的文件类型还有”.pyo”、”.pyc”、”.pyd”、”.so”、”.dll”，但 Python 初学者几乎用不到。  
module 可看作一个工具类，可共用或者隐藏代码细节，将相关代码放置在一个 module 以便让代码更好用、易懂，让 coder 重点放在高层逻辑上。  
module 能定义函数、类、变量，也能包含可执行的代码。module 来源有 3 种：  
①Python 内置的模块（标准库）；  
②第三方模块；  
③自定义模块。

<!--more-->

**包 package：** 为避免模块名冲突，Python 引入了按目录组织模块的方法，称之为 包（package）。包 是含有 Python 模块的文件夹。  
![](https://img-blog.csdn.net/20180727110240655?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODI1NjQ3NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
当一个文件夹下有init.py 时，意为该文件夹是一个包（package），其下的多个模块（module）构成一个整体，而这些模块（module）都可通过同一个包（package）导入其他代码中。

其中init.py 文件 **用于组织包（package），方便管理各个模块之间的引用、控制着包的导入行为。**  
该文件可以什么内容都不写，即为空文件（为空时，仅仅用 import [该包] 形式 是什么也做不了的），存在即可，相当于一个标记。  
但若想使用 from pacakge_1 import * 这种形式的写法，需在init.py 中加上：all = [‘file_a’, ‘file_b’] #package_1 下有 file_a.py 和 file_b.py，在导入时init.py 文件将被执行。  
但不建议在init.py 中写模块，以保证该文件简单。不过可在init.py 导入我们需要的模块，以便避免一个个导入、方便使用。

其中，all是一个重要的变量，用来指定此包（package）被 import * 时，哪些模块（module）会被 import 进【当前作用域中】。不在all列表中的模块不会被其他程序引用。可以重写all，如all = [‘当前所属包模块 1 名字’, ‘模块 1 名字’]，如果写了这个，则会按列表中的模块名进行导入。

在模糊导入时，形如 from package import *，* 是由__all__定义的。

精确导入，形如 from package import *、import package.class。

path也是一个常用变量，是个列表，默认情况下只有一个元素，即当前包（package）的路径。修改path可改变包（package）内的搜索路径。

当我们在导入一个包（package）时（会先加载init.py 定义的引入模块，然后再运行其他代码），实际上是导入的它的init.py 文件（导入时，该文件自动运行，助我们一下导入该包中的多个模块）。我们可以在init.py 中再导入其他的包（package）或模块 或自定义类。

**2、sys.modules、命名空间、模块内置属性**  
**2.1 sys.modules**  
官方解释：[链接](https://docs.python.org/3.6/library/sys.html?highlight=sys%20modules#sys.modules)  
**sys.modules** 是一个 将模块名称（module_name）映射到已加载的模块（modules） 的字典。可用来强制重新加载 modules。Python 一启动，它将被加载在内存中。  
当我们导入新 modules，sys.modules 将自动记录下该 module；当第二次再导入该 module 时，Python 将直接到字典中查找，加快运行速度。

它是个**字典**，故拥有字典的一切方法，如 sys.modules.keys()、sys.modules.values()、sys.modules[‘os’]。但请不要轻易替换字典、或从字典中删除某元素，将可能导致 Python 运行失败。

```python
import sys
print(sys.modules)#打印，查看该字典具体内容。
```

**2.2 命名空间**  
如同一个 dict，key 是变量名字，value 是变量的值。

*   每个函数 function 有自己的命名空间，称 local namespace，记录函数的变量。
*   每个模块 module 有自己的命名空间，称 global namespace，记录模块的变量，包括 **functions、classes、导入的 modules、module 级别的变量和常量**。
*   build-in 命名空间，它包含 build-in function 和 exceptions，可被任意模块访问。

某段 Python 代码访问 变量 x 时，Python 会所有的命名空间中查找该变量，顺序是：

1.  local namespace 即当前函数或类方法。若找到，则停止搜索；
2.  global namespace 即当前模块。若找到，则停止搜索；
3.  build-in namespace Python 会假设变量 x 是 build-in 的函数函数或变量。若变量 x 不是 build-in 的内置函数或变量，Python 将报错 NameError。
4.  对于闭包，若在 local namespace 找不到该变量，则下一个查找目标是父函数的 local namespace。

例：namespace_test.py 代码

```python
def func(a=1):
    b = 2
    print(locals())#打印当前函数（方法）的局部命名空间
    '''
    locs = locals()#只读，不可写。将报错！
    locs['c'] = 3
    print(c)
    '''
    return a+b
func()
glos = globals()
glos['d'] = 4
print(d)

print(globals())#打印当前模块namespace_test.py的全局命名空间
```

内置函数 locals()、globals() 返回一个字典。区别：前者只读、后者可写。

**命名空间** 在 from module_name import 、import module_name 中的体现：from 关键词是导入模块或包中的某个部分。

1.  from module_A import X：会将该模块的函数 / 变量导入到当前模块的命名空间中，无须用 module_A.X 访问了。
2.  import module_A：modules_A 本身被导入，但保存它原有的命名空间，故得用 module_A.X 方式访问其函数或变量。

**2.3 模块内置属性**

1.  name直接运行本模块，name值为main；import module，name值为模块名字。
2.  file当前 module 的绝对路径
3.  dict
4.  doc
5.  package
6.  path

**3、绝对导入、相对导入**  
![](https://img-blog.csdn.net/20180727120550441?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODI1NjQ3NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
**3.1 绝对导入**：所有的模块 import 都从 “根节点” 开始。根节点的位置由 sys.path 中的路径决定，项目的根目录一般自动在 sys.path 中。如果希望程序能处处执行，需手动修改 sys.path。  
例 1：c.py 中导入 B 包 / B1 子包 / b1.py 模块

```python
import sys,os
BASE_DIR = os.path.dirname(os.path.abspath(__file__))#存放c.py所在的绝对路径

sys.path.append(BASE_DIR)

from B.B1 import b1#导入B包中子包B1中的模块b1
```

例 2：b1.py 中导入 b2.py 模块

```python
from B.B1 import b2#从B包中的子包B1中导入模块b2
```

**3.2 相对导入**：只关心相对自己当前目录的模块位置就好。不能在包（package）的内部直接执行（会报错）。不管根节点在哪儿，包内的模块相对位置都是正确的。  
b1.py 代码

```python
#from . import b2 #这种导入方式会报错。
import b2#正确
b2.print_b2()
```

b2.py 代码

```python
def print_b2():
    print('b2')
```

运行 b1.py，打印：b2。

在使用相对导入时，可能遇到 ValueError: Attempted relative import beyond toplevel package  
解决方案：参考这篇文章，[链接](https://blog.csdn.net/sky453589103/article/details/78863050)。

**3.3 单独导入包（package）**：单独 import 某个包名称时，不会导入该包中所包含的所有子模块。  
c.py 导入同级目录 B 包的子包 B1 包的 b2 模块，执行 b2 模块的 print_b2() 方法：  
c.py 代码

```python
import B
B.B1.b2.print_b2()
```

运行 c.py，会报错。

**解决办法**：  
B/init.py 代码

```python
from . import B1#其中的.表示当前目录
```

B/B1/init.py 代码

```python
from . import b2
```

此时，执行 c.py，成功打印：b2。

**3.4 额外**  
①一个. py 文件调用另一个. py 文件中的类。  
如 a.py（class A）、b.py（class B），a.py 调用 b.py 中类 B 用：from b import B  
②一个. py 文件中的类 继承另一个. py 文件中的类。如 a.py（class A）、b.py（class B），a.py 中类 A 继承 b.py 类 B。

```python
from b import B
class A(B):
pass
```

**二、Python 运行机制：理解 Python 在执行 import 语句（导入内置（Python 自个的）或第三方模块（已在 sys.path 中））时，进行了啥操作？**
-----------------------------------------------------------------------------------------

step1：创建一个新的、空的 module 对象（它可能包含多个 module）；  
step2：将该 module 对象 插入 sys.modules 中；  
step3：装载 module 的代码（如果需要，需先编译）；  
step4：执行新的 module 中对应的代码。

在执行 step3 时，首先需找到 module 程序所在的位置，如导入的 module 名字为 mod_1，则解释器得找到 mod_1.py 文件，搜索顺序是：  
当前路径（或当前目录指定 sys.path）—–>PYTHONPATH—–>Python 安装设置相关的默认路径。

对于不在 sys.path 中，一定要避免用 import 导入 自定义包（package）的子模块（module），而要用 from…import… 的绝对导入 或相对导入，且包（package）的相对导入只能用 from 形式。

1、**“标准”import，顶部导入**  
![](https://img-blog.csdn.net/2018072714235219?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODI1NjQ3NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
有上述基础知识，再理解这个思维导图，就很容易了。在运用模块的变量或函数时，就能得心应手了。

**2、嵌套 import**

**2.1 顺序导入 - import**  
![](https://img-blog.csdn.net/20180727142629596?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODI1NjQ3NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

PS：**各个模块的 Local 命名空间的独立的**。即：  
**test 模块** import moduleA 后，只能访问 moduleA 模块，不能访问 moduleB 模块。虽然 moduleB 已加载到内存中，如需访问，还得明确地在 test 模块 import moduleB。实际上打印 locals()，字典中只有 moduleA，没有 moduleB。

**2.2 循环导入 / 嵌套导入 - import**  
![](https://img-blog.csdn.net/20180727142811352?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODI1NjQ3NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
**形如 from moduleB import ClassB 语句，根据 Python 内部 import 机制**，执行细分步骤：  

1. 在 sys.modules 中查找 符号 “moduleB”；  
2. 如果符号 “moduleB” 存在，则获得符号 “moduleB” 对应的 module 对象；  
   从的dict__中获得 符号 “ClassB” 对应的对象。如果 “ClassB” 不存在，则抛出异常 “ImportError: cannot import name ‘classB’”  
3. 如果符号 “moduleB” 不存在，则创建一个新的 module 对象。不过此时该新 module 对象的dict为空。然后执行 moduleB.py 文件中的语句，填充的dict。

**总结：from moduleB import ClassB 有两个过程，先 from module，后 import ClassB。**  
![](https://img-blog.csdn.net/20180727143522855?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODI1NjQ3NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当然将 moduleA.py 语句 from moduleB import ClassB 改为：import moduleB，将在第二次执行 moduleB.py 语句 from moduleA import ClassA 时报错：ImportError: cannot import name ‘classA’

**解决这种 circular import 循环导入的方法：**  
例比：安装无线网卡时，需上网下载网卡驱动；  
安装压缩软件时，从网上下载的压缩软件安装程序是被压缩的文件。  
**方法 1**—–> 延迟导入（lazy import）：把 import 语句写在方法 / 函数里，将它的作用域限制在局部。（此法可能导致性能问题）  
**方法 2**—–> 将 from x import y 改成 import x.y 形式  
**方法 3**—–> 组织代码（重构代码）：更改代码布局，可合并或分离竞争资源。  
合并—–> 都写到一个. py 文件里；  
分离–> 把需要 import 的资源提取到一个第三方. py 文件中。  
总之，将循环变成单向。

**3、包（package）import**  
在一个文件下同时有init.py 文件、和其他模块文件时，该文件夹即看作一个包（package）。包的导入 和模块导入基本一致，只是**导入包**时，**会执行这个init.py，而不是模块中的语句。**  
而且，如果**只是单纯地导入包【形如：import xxx】**，而包的init.py 中有没有明确地的其他初始化操作，则：此包下的模块 是不会被自动导入的。当然该包是会成功导入的，并将包名称放入当前. py 的 Local 命名空间中。  
![](https://img-blog.csdn.net/20180727144304644?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODI1NjQ3NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
[D：youcaihua\test\PkgDemo\mod.py] 文件  
[D：youcaihua\test\PkgDemo\pkg1\pkg1_mod.py] 文件  
[D：youcaihua\test\PkgDemo\pkg2\pkg2_mod.py] 文件，三个文件同样的代码：

```python
def getName():
    print(__name__)

if __name__ == '__main__':
    getName()
```

[D：youcaihua\test\test.py] 文件

```python
import PkgDemo.mod#1
print(locals(),'\n')
import PkgDemo.pkg1#2
print(locals(),'\n')
import PkgDemo.pkg1.pkg1_mod as m1#3
print(locals(),'\n')
import PkgDemo.pkg2.pkg2_mod#4
PkgDemo.mod.getName()#5
print('调用mod.py----', locals(), '\n')
m1.getName()#6
PkgDemo.pkg2.pkg2_mod.getName()#7
```

执行 **#1** 后，将 PkgDemo、PkgDemo.mod 加入 sys.modules 中，此时可调用 PkgDemo.mod 的任何类、或函数。当不能调用包 PkgDemo.pkg1 或 pkg2 下任何模块。但当前 test.py 文件 Local 命名空间中**只有** PkgDemo。

执行 **#2** 后，只是将 PkgDemo.pkg1 载入内存，sys.modules 会有 PkgDemo、PkgDemo.mod、PkgDemo.pkg1 三个模块。但 PkgDemo.pkg1 下的任何模块 都没有自动载入内存，所以在此时：PkgDemo.pkg1.pkg1_mod.getName() 将会出错。当前 test.py 的 Local 命名空间依然只有 PkgDemo。

执行 **#3** 后，会将 pkg1_mod 载入内存，sys.modules 会有 PkgDemo、PkgDemo.mod、PkgDemo.pkg1、PkgDemo.pkg1.pkg1_mod 四个模块，此时可执行 PkgDemo.pkg1.pkg1_mod.getName()。由于使用了 as，当前 Local 命名空间将另外添加 m1（作为 PkgDemo.pkg1.pkg1_mod 的别名）、当然还有 PkgDemo。

执行 **#4** 后，会将 PkgDemo.pkg2、PkgDemo.pkg2.pkg2_mod 载入内存，sys.modules 中会有 PkgDemo、PkgDemo.mod、PkgDemo.pkg1、PkgDemo.pkg1.pkg1_mod、PkgDemo.pkg2、PkgDemo.pkg2.pkg2_mod 六个模块，当然：当前 Local 命名空间还是只有 PkgDemo、m1。

**#5**、**#6**、**#7** 当然都可正确执行。

三、How to avoid Python circle import error？如何避免 Python 的循环导入问题？
--------------------------------------------------------------

代码布局、（架构）设计问题，解决之道是：将循环变成单向。采用分层、用时导入、相对导入（层次建议不要超过两个）

注意：在命令行执行 Python xx.py、与 IDE 中执行，结果可能不同。

如需转载请注明出处。  
参考：  
[官方规范](https://www.python.org/dev/peps/pep-0328/)