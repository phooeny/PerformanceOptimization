Descriptor

[TOC]

#原理

在python中，如果一个新式类定义了\_\_get\_\_, \_\_set\_\_,\_\_delete\_\_方法中的一个或者多个，那么称之为descriptor。descriptor通常用来改变默认的属性访问（attribute lookup)。注意 ，descriptor的实例一定是类的属性（class attribute)。

这三个特殊的函数签名是这样的：

　　object.\_\_get\_\_(self, instance, owner)：return value
　　object.\_\_set\_\_(self, instance, value)：return None
　　object.\_\_delete\_\_(self, instance)： return None



descriptor有分为data descriptor与non-data descriptor:

如果一个descriptor只实现了\_\_get\_\_方法，我们称之为non-data descriptor

如果同时实现了\_\_get\_\_ 和  \_\_set\_\_我们称之为data descriptor。



##Attribute lookup

###实例属性查找

​    （1）如果“attr”是出现在Clz或其基类的\_\_dict\_\_中， 且attr是data descriptor， 那么调用其\_\_get\_\_方法, 否则

​    （2）如果“attr”出现在obj的\_\_dict\_\_中， 那么直接返回 obj.\_\_dict\_\_['attr']， 否则

​    （3）如果“attr”出现在Clz或其基类的\_\_dict\_\_中

​        （3.1）如果attr是non-data descriptor，那么调用其\_\_get\_\_方法， 否则

​        （3.2）返回 \_\_dict\_\_['attr']

​    （4）如果Clz有\_\_getattr\_\_方法，调用\_\_getattr\_\_方法，否则

​    （5）抛出AttributeError 

###类属性查找

　　（2.1）如果clz.\_\_dict\_\_['attr']是一个descriptor（不管是data descriptor还是non-data descriptor），都调用其\_\_get\_\_方法

　　（2.2）否则返回clz.\_\_dict\_\_['attr']


###属性赋值

　　（1）如果Clz定义了\_\_setattr\_\_方法，那么调用该方法，否则

　　（2）如果“attr”是出现在Clz或其基类的\_\_dict\_\_中， 且attr是data descriptor， 那么调用其\_\_set\_\_方法, 否则

　　（3）等价调用obj.\_\_dict\_\_['attr'] = var 



#应用

##function & method

```
class Widget(object):
	def func(self):pass;

w = Widget()
```



| op                      | python 2.7                                                   | python 3.7                                                   |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| w.\_\_dict\_\_          | {}                                                           | {}                                                           |
| Widget.\_\_dict\_\_     | dict_proxy({'\_\_dict\_\_': <attribute '\_\_dict\_\_' of 'Widget' objects>, '\_\_module\_\_': '\_\_main\_\_', '\_\_weakref\_\_': <attribute '\_\_weakref\_\_' of 'Widget' objects>, '\_\_doc\_\_': None, 'func': <function func at 0x1022ef398>}) | mappingproxy({'\_\_module_\_\_': '\_\_main_\_\_', 'func': <function Widget.func at 0x10fc9f400>, '\_\_dict\_\_': <attribute '\_\_dict\_\_' of 'Widget' objects>, '\_\_weakref\_\_': <attribute '\_\_weakref\_\_' of 'Widget' objects>, '\_\_doc\_\_': None}) |
| w.func                  | <bound method Widget.func of <\_\_main\_\_.Widget object at 0x1022d8210>> | <bound method Widget.func of <\_\_main\_\_.Widget object at 0x10fcaaeb8>> |
| Widget.\_\_dict\_\_['func'] | <function func at 0x1022ef398>                               | <function Widget.func at 0x10fc9f400>                        |
| Widget.func             | <unbound method Widget.func>                                 | <function Widget.func at 0x10fc9f400>                        |



python 2.7  和 3.7中，不管是 method 还是 function，都默认包含 \_\_get\_\_  方法，而并没有 \_\_set\_\_

##methodmethod & staticmethod



##property

property()就是一个data descriptor实现, https://docs.python.org/2/howto/descriptor.html#descriptor-protocol 



## super



##examples


```
import functools, time
class cached_property(object):
    """ A property that is only computed once per instance and then replaces
        itself with an ordinary attribute. Deleting the attribute resets the
        property. """

    def __init__(self, func):
        functools.update_wrapper(self, func)
        self.func = func
    
    def __get__(self, obj, cls):
        if obj is None: return self
        value = obj.__dict__[self.func.__name__] = self.func(obj) #此处将结果写入obj.__dict__
        return value

class TestClz(object):
    @cached_property
    def complex_calc(self):
        print('very complex_calc');
        return sum(range(100))

if __name__=='__main__':
    t = TestClz()
    print('>>> first call');
    print(t.complex_calc);
    print('>>> second call');
    print(t.complex_calc);
```

Python 2.7 和 Python 3.7 的输出结果相同
```
>>> first call
very complex_calc
4950
>>> second call
4950
```



#reference

1.  https://docs.python.org/3/howto/descriptor.html 
2. https://www.cnblogs.com/xybaby/p/6266686.html
3. https://www.cnblogs.com/xybaby/p/6270551.html 
4. https://pyzh.readthedocs.io/en/latest/Descriptor-HOW-TO-Guide.html 