Python Decorator

[TOC]


##多个装饰器
https://zhuanlan.zhihu.com/p/45458873 

如果存在多个装饰器,可以把装饰器想象成洋葱，由近及远对函数进行层层包裹，执行的时候就是拿一把刀从一侧开始切，直到切到另一侧结束



##带参数的 装饰器

在正常装饰器的外层再包裹一层函数。jiangnz 个人理解： 其实就是优先调用 call 方法，返回的结果再跟@语法糖结合

```
def auth(permission):
    def _auth(func):
        def wrapper(*args, **kwargs):
            print(f"验证权限[{permission}]...")
            func(*args, **kwargs)
            print("执行完毕...")

        return wrapper
    
    return _auth


@auth("add")
def add(a, b):
    """
    求和运算
    """
    print(a + b)


add(1, 2) 
```


##functools.wraps

https://zhuanlan.zhihu.com/p/45535784

经过装饰器之后的函数还是原来的函数吗？原来的函数肯定还存在的，只不过真正调用的是装饰后生成的新函数。
为了消除装饰器对原函数的影响，我们需要伪装成原函数，拥有原函数的属性，看起来就像是同一个人一样。



##可调用对象

装饰器并不一定就是 闭包，只需要是 `可调用对象`且返回结果是 `可调用对象`

实例一： @语法糖调用function， 而function返回的结果是 可调用instance
```
class cls_func():
    def __init__(self, func):
        self.func = func;

    def __call__(self):
        print('before test');
        self.func('124');
        print('after test');
        return '1222222';

class wrapper():

    def __call__(self, func):
        return cls_func(func);

def wrapper_function(func):
    return cls_func(func);

@wrapper_function
def testd(taskid):
    print ("testd runing".format(taskid));
    return "task summer success eg", []

print(testd())
```

实例二： @语法糖调用 可调用instance， 可调用instance的返回结果是 method

```
class wrapper():

    def test(self):
        print('before test');
        self.func('124');
        print('after test');
        return '1222222';
    
    def __call__(self, func):
        self.func = func;
        return self.test;

wrapper_instance = wrapper();

@wrapper_instance
def testd(taskid):
    print ("testd runing".format(taskid));
    return "task summer success eg", []

print(testd())
```

实例三： @语法糖调用 可调用instance， 可调用instance的返回结果是 另一个可调用instance
```
class cls_func():
    def __init__(self, func):
        self.func = func;

    def __call__(self):
        print('before test');
        self.func('124');
        print('after test');
        return '1222222';

class wrapper():

    def __call__(self, func):
        return cls_func(func);

wrapper_instance = wrapper();

@wrapper_instance
def testd(taskid):
    print ("testd runing".format(taskid));
    return "task summer success eg", []

print(testd())
```





http://y.tsutsumi.io/dry-principles-through-python-decorators.html

