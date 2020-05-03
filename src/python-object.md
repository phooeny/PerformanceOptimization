Object in Python

[TOC]

python中一切皆对象，包括常量，函数，类和实例，甚至包括 code 和 model 也都是 对象。



object, type, class, instance

#原理

##世界的起源： object 与 type

cpython中的定义

鸡生蛋与蛋生鸡





##metaclass 初探

| python 2                                                     | python 3                                        |
| ------------------------------------------------------------ | ----------------------------------------------- |
| class Example(): <br>    \_\_metaclass\_\_ =  meta_cls;<br>    pass; | class Example(metaclass=meta_cls):<br>    psss; |



##对象实例化过程

Python的对象实例化过程只是 调用可调用对象的一种特例

http://blog.xiayf.cn/2012/04/26/python-object-creation-sequence/

https://blog.tonyseek.com/post/notes-about-python-instantiation/



##metaclass 深入

Python 2

Python 3



只需要是可调用对象



#应用举例

##abc module



| component | python version |      |
| --------- | -------------- | ---- |
|           |                |      |
|           |                |      |
|           |                |      |
|           |                |      |



|----|----|

| | ABC|

collections 模块已经提供的 metaclass

python 2

funcobject.h

PyFunctionObject

PyCodeObject

​	常量表 co_consts

​	符号表 co_names

​	字节码序列 co_code





MAKE_FUNCTION 指令

CALL_FUCNTION



##函数

| Python 2.7                                                   | Python 3.7                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| type(func_name): <type 'function'><br/>func_name.\_\_class\_\_ : <type 'function'><br/>func_name.\_\_base\_\_ : AttributeError: 'function' object has no attribute '\_\_base\_\_'<br>isinstance(a, object) : True <br>issubclass(a, object) : TypeError: issubclass() arg 1 must be a class<br>a.\_\_zjn\_\_ = 'test' | type(func_name): <class 'function'><br>func_name.\_\_class\_\_ : <class 'function'><br>func_name.\_\_base\_\_ : AttributeError: 'function' object has no attribute '\_\_base\_\_'<br>isinstance(a, object) : True <br/>issubclass(a, object) : TypeError: issubclass() arg 1 must be a class<br>a.\_\_zjn\_\_ = 'test' |

func_closure

func_code



method 与 function区别



从语法上讲，def func()是函数生命语句；而从虚拟机角度看，它其实是函数对象的创建语句



`PyEval_EvalCode->PyEval_EvalCode->_PyEval_EvalCodeWithName->_PyEval_EvalFrameEx`

module - `run_mod -> PyEval_EvalCode->PyEval_EvalCodeEx->_PyEval_EvalCodeWithName->PyEval_EvalFrameEx`.



# 对象扩展



##可调用对象



## 可迭代对象



#QAs

1. how to determine if a class in python is a metaclass
   1. 任何 callable 的object都可以作为 metaclass，包括 function。
2. how to find the metaclass of a class
   1. python 2中,  cls.\_\_metaclass\_\_ 
   2. python3中，彻底放弃了 \_\_metaclass\_\_ 标记方法，使用 cls.\_\_metaclass\_\_  会报异常
   3. 通用方法: type(cls), cls.\_\_class\_\_ 



## class理解

class 关键词声明的变量，也有可能本质上是一个function。实例如下

| class declararion                                    | type(class_name)   |
| ---------------------------------------------------- | ------------------ |
| class C(metaclass=lambda *a: lambda *a: None): pass; | <class 'function'> |
| class C(metaclass=lambda *a: None): pass;            | <class 'NoneType'> |



jiangnz理解：python 的代码都会被编译为字节码序列，python自带的关键字也是如此。字节码的指令集合很有限(255个，实际只用了一百多个)，只是指定了特定的执行序列。

   





可调用对象

https://www.jianshu.com/p/cab0aeca227f



描述符 descriptor

https://blog.tonyseek.com/post/notes-about-python-descriptor/ 



type关键字的理解

jiangnz：并不是一个函数，而是内置 object

| type       | python 2.7    | python 3.7     |
| ---------- | ------------- | -------------- |
| type(type) | <type 'type'> | <class 'type'> |





function 与 method

PyFunctionObject 与 PyMethodObject



Bound method 与 Unbound method





isinstance(module name, object) : True



| type                         | python 2.7        | python 3.7         |
| ---------------------------- | ----------------- | ------------------ |
| type(module_name ) |<type 'module'> | <class 'module'> |
| type(func_name)              | <type 'function'> | <class 'function'> |
| type(func_name.\_\_code\_\_) | <type 'code'>     | <class 'code'>     |



第一类对象



并非所有的关键字都可以执行 type( name) 操作



duck typing

https://www.liaoxuefeng.com/wiki/1016959663602400/1017497232674368 









##字节码

https://zhuanlan.zhihu.com/p/39259061

https://python3-cookbook.readthedocs.io/zh_CN/latest/c09/p25_disassembling_python_byte_code.html

https://juejin.im/entry/5b23d2b0f265da595a5e33e5

https://fanchao01.github.io/blog/2016/11/13/python-pycode_and_frame/



Python 2.7 与 Python 3.7 字节码序列的指令集并不相同，比如声明class 的字节码序列

python先将代码编译为字节码序列，然后在虚拟机中动态地依次解释执行字节码。编译好的字节码存储在硬盘中，以.pyc 或 .pyd 为扩展名。这些字节码其实就是 PyCodeObject对象的持久化。



import opcode

for op in range(len(opcode.opname)):

​	print('0x%.2X(%.3d): %s' % (op, op, opcode.opname[op]));



include/opcode.h

https://github.com/python/cpython/blob/master/Include/opcode.h







python -m dis a.py



第一行为代码行号；
第二行为偏移地址；
第三行为字节码指令；
第四行为指令参数；
第五行为参数解释。



`PyEval_EvalFrameEx`函数的功能为执行字节码并返回结果

Python 3.7: https://github.com/python/cpython/blob/3.7/Python/ceval.c#L544 

Python 2.7: https://github.com/python/cpython/blob/2.7/Python/ceval.c#L689 

函数 PyEval_EvalFrameEx 这两版python中的差别很大。Python 2.7 的 PyEval_EvalFrameEx函数中有对 opcode进行分发的 switch语句。但是Python 3.7中，opcode进行分发的 switch语句不在 PyEval_EvalFrameEx中，而在 _PyEval_EvalFrameDefault 中



Py_LOCAL_INLINE(PyObject \*) _Py_HOT_FUNCTION  call_function(PyObject \*\*\*pp_stack, Py_ssize_t oparg, PyObject \*kwnames) 





##super

super是个类。`super 和父类没有实质性的关联，而是跟MRO直接相关`。获取 instance 的 MRO 列表，查找 cls 在当前 MRO 列表中的 index, 并返回它的下一个类，即 mro[index + 1]。

https://mozillazg.com/2016/12/python-super-is-not-as-simple-as-you-thought.html

http://funhacks.net/explore-python/Class/super.html

| type        | python 2.7    | python 3.7     |
| ----------- | ------------- | -------------- |
| type(super) | <type 'type'> | <class 'type'> |



class A(object):pass;

class B(object):pass;

class C( A if name.lower() =='a' else B): pass;



a = A if name.lower() =='a' else B

class C( a): pass;







#Reference

CPython： https://github.com/python/cpython



