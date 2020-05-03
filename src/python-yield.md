Python Yield



[TOC]



#原理

##栈帧



PyEval_EvalFrameEx



call_function 最终会调用

PyEval_EvalCodeEx



##send与next

send和next都是调用的同一函数gen_send_ex，区别在于是否带有参数。 `next`与`send`函数，如下

```
static PyObject * 
gen_iternext(PyGenObject *gen) 
{  
	return gen_send_ex(gen, **NULL**, 0);
}

static PyObject * 
gen_send(PyGenObject *gen, PyObject *arg)
{  
	return gen_send_ex(gen, arg, 0); 
}
```



#应用

##常规使用

yield

yield from

asyncio.coroutine

async 和 await





##generator

源码在 Objects/genobject.c

PyGenObject

PyGen_New



python生成器实现原理就是，基于一个"游离"的帧对象（PyFrameObject），调用生成器时，将该"游离"帧对象 挂载到当前帧栈上执行，生成器yield返回时，返回当前的值并从帧栈上卸载。在用户层面通过一个生成器对象，提供一批友好的接口 口，封装了内部保存PyFrameObject的事实.仅此而且,而这也是python基于单线程实现协程的基石。



https://juejin.im/post/5d427c3df265da039401e85a





PyCodeObject
PyFunctionObject

​	https://sourcegraph.com/github.com/python/cpython@3.7/-/blob/Include/funcobject.h#L41

PyFrameObject







**在启动生成器函数时只能send(None),如果试图输入其它的值都会得到错误提示信息**

```
>>> a = return_a_generator();
>>> print(a.send('name'))
 Traceback (most recent call last):
   File "<stdin>", line 1, in <module>
 TypeError: can't send non-None value to a just-started generator
```




StopIteration异常



带有yield 语句的function 只能说是 generator functoin，本质还是function。该function执行后返回的值才是真正的 generator。


```
def return_a_generator():
	while True:
		yield 'test';
```


| type operation             | python 2.7         | python 3.7          |
| -------------------------- | ------------------ | ------------------- |
| type(return_a_generator)   | <type 'function'>  | <class 'function'>  |
| type(return_a_generator()) | <type 'generator'> | <class 'generator'> |



Python 3.7 的源码中

generator functoin 执行的过程中，会最终调用到函数 `_PyEval_EvalCodeWithName`,
https://sourcegraph.com/github.com/python/cpython@3.7/-/blob/Python/ceval.c#L3669

该函数在创建了 PyFrameObject后，会检查当前function的 flag 是否有标记 `CO_GENERATOR` ，若有此标记，则会生成 PyGenObject 实例并返回给调用者。generator functoin的 代码函数体并不会被执行。

函数`_PyEval_EvalCodeWithName` 会处理多种情况，而不仅仅是 generator：  Handle generator/coroutine/asynchronous generator 

CO_GENERATOR标记是在 compile阶段被标记的
https://sourcegraph.com/github.com/python/cpython@3.7/-/blob/Python/compile.c#L5352

标记依据则是code 编译的语法解析阶段，如果有yield语句，会执行该标记 st->st_cur->ste_generator = 1;


https://sourcegraph.com/github.com/python/cpython@3.7/-/blob/Python/symtable.c#L1440


 

Reference：

https://leanpub.com/insidethepythonvirtualmachine/read#leanpub-auto-generators-behind-the-scenes



### generator expression







##协程 coroutine

https://zhuanlan.zhihu.com/p/25513336

https://juejin.im/post/5c13245ee51d455fa5451f33







##异步





#QAs

1. generator 是否可以跨线程？进程？
2. 可以跨 module么？
3. [解决]如果generator里面使用了 global variable，若外部改变该变量的值，generator的返回值会不会同时改变？
   1. 在 Python 2.7 和 Python 3.7 下，genetator使用的都是**修改后的新值**



#Reference

1. https://hackernoon.com/the-magic-behind-python-generator-functions-bc8eeea54220

