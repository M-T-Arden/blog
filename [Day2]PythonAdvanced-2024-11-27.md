# [PythonAdvanced] Learning Notes - Day 2

## Overview

**Date**: 2024-11-27                                     **Time Spent**: 6 hours                    

**Topics**: Decorators                                  **Difficulty**: ⭐⭐⭐ (1-5 ⭐)

## What I Learned Today

1. [**Generator & Iterator 迭代器和生成器**]

   - **Main points**: 
     
     When we use a loop to loop over something it is called iteration. An iterator is an object that enables a programmer to traverse a container, particularly lists. However, an iterator performs traversal and gives access to data elements in a container, but does not perform iteration. 
     
     Generators are iterators, and they do not store all the values in memory, they generate the values on the fly. 

     當我們使用循環來循環某些內容時，稱為迭代。它是進程本身的名稱。迭代器是一個對象，它使程式設計師能夠遍歷容器，特別是列表。迭代器用于遍历集合中的元素，但它本身并不存储元素，而是提供了一种按顺序访问集合元素的方式。因此，说迭代器更像是一个访问元素的接口。
     
     生成器（generator）是一种特殊的迭代器，它使用函数来产生序列中的元素。生成器函数使用 `yield` 关键字来暂停函数执行并产生一个值，然后在下一次迭代时从上次暂停的位置继续执行。生成器的一个关键特性是它们可以“惰性求值”（lazy evaluation），即只在需要时才生成值。
     
   - **Generator Example code**
     
     ```python
     # Generators are best for calculating large sets of results (particularly calculations involving loops themselves) where you don’t want to allocate the memory for all results at the same time. 
     # generator version
     def fibon(n):
         a = b = 1
         for i in range(n):
             yield a
             a, b = b, a + b
     for x in fibon(1000000):
         print(x)
     ```
     
   - **Iterator Example code**
     
     ```python
     int_var = 1779
     iter(int_var)
     # Output: Traceback (most recent call last):
     #   File "<stdin>", line 1, in <module>
     # TypeError: 'int' object is not iterable
     # This is because int is not iterable
     
     my_string = "Yasoob"
     my_iter = iter(my_string)
     print(next(my_iter))
     # Output: 'Y'
     ```

2. [**Classes 类**]

   - **Main points**: Class variables are for data shared between different instances of a class. 

     类变量用于在类的不同实例之间共享的数据。

   - **Classes Example code**

     ```python
     class Cal(object): #不可变函数使用过程中不会出现污染
         # pi is a class variable
         pi = 3.142
     
         def __init__(self, radius):
             # self.radius is an instance variable
             self.radius = radius
     
         def area(self):
             return self.pi * (self.radius ** 2)
     
     a = Cal(32)
     a.area()
     # Output: 3217.408
     a.pi
     # Output: 3.142
     a.pi = 43
     a.pi
     # Output: 43
     ```

   - `__init__` is a class initializer. Whenever an instance of a class is created its `__init__` method is called. 是类初始值设定项。每当创建类的实例时，都会调用其 '__init__' 方法。

     ```python
     class GetTest(object):
         def __init__(self, name):
             print('Greetings!! {0}'.format(name))
         def another_method(self):
             print('I am another method which is not'
                   ' automatically called')
     
     a = GetTest('yasoob')
     # Output: Greetings!! yasoob
     
     # Try creating an instance without the name arguments
     b = GetTest()
     Traceback (most recent call last):
       File "<stdin>", line 1, in <module>
     TypeError: __init__() takes exactly 2 arguments (1 given)
     ```

   - `__getitem__`Implementing **get item** in a class allows its instances to use the [] (indexer) operator. 在类中实现 **get item** 允许其实例使用 []（索引器）运算符。

     ```python
     class GetTest(object):
         def __init__(self):
             self.info = {
                 'name':'Yasoob',
                 'country':'Pakistan',
                 'number':12345812
             }
     
         def __getitem__(self,i):
             return self.info[i]
     
     foo = GetTest()
     
     foo['name']
     # Output: 'Yasoob'
     
     foo['number']
     # Output: 12345812
     ```

3. [**Meta classes 元类**]

   - **Main points**: A class defines the behavior of an object, while a meta class defines the behavior of a class. With meta classes, we can change the way classes are created, automatically add properties or methods, implement class registration, enforce API consistency, and more.

     类定义了对象的行为，而元类则定义了类的行为。通过元类，我们可以改变类的创建方式，自动添加属性或方法，实现类的注册，强制API一致性等。

   - **Meta classes Example code**

     ```python
     class MyMeta(type):
         def __new__(cls, name, bases, dct):
             x = super().__new__(cls, name, bases, dct)
             x.attr = 100
             return x
     
     class MyClass(metaclass=MyMeta):
         pass
     
     print(MyClass.attr)  # 输出: 100
     ```

4. [**Descriptor 属性描述符**]

   - **Main points**: A descriptor is a class that can be considered as long as one or more of the methods in `__get__`, `__set__`, `__delete__` are internally defined. If a descriptor defines both the `__set__` and `__get__` methods (or only the `__set__` method), it is called a data descriptor. Data descriptors take precedence over instance attributes. If a descriptor only defines a `__get__` method (or doesn't define a `__set__`method), it is called a non-data descriptor.

     描述符是一个类，只要内部定义了`__get__`、`__set__`、`__delete__`中的一个或多个方法，就可以被视为描述符。如果描述符同时定义了`__set__`和`__get__`方法（或只定义了`__set__`方法），则被称为数据描述符。数据描述符的优先级高于实例属性。如果描述符只定义了`__get__`方法（或未定义`__set__`方法），则被称为非数据描述符。

   - **Descriptor Example code**

     ```python
     class Descriptor:
         def __set_name__(self, owner, name):
             self.name = f"__{name}__"
     
         def __get__(self, instance, owner):
             return getattr(instance, self.name, None)
     
         def __set__(self, instance, value):
             setattr(instance, self.name, value)
     
         def __delete__(self, instance):
             raise AttributeError("delete is disabled")
     
     class X:
         data = Descriptor()
     
     x = X()
     x.data = 100
     print(x.data)  # 输出: 100
     print(x.__dict__)  # {'__data__': 100}
     ```

   - **Example code**

     ```python
     def do_twice(func):
         def wrapper_do_twice(*args, **kwargs):
             func(*args, **kwargs)
             func(*args, **kwargs)
         return wrapper_do_twice
     
     @do_twice # greet = do_twice(greet)
     def greet(name):
         print(f"Hello {name}")
     >>> greet(name = "Mia")
     ```

   - **Example code**

5. [**Context Managers 上下文管理器**]

   - **Main points**: Context managers allow you to allocate and release resources precisely when you want to. The most widely used example of context managers is the `with` statement. 

     上下文管理器可讓您在需要時精確地指派和釋放資源。上下文管理器最廣泛使用的範例是`with`語句。

     *Example*: Open the file, write some data to it and then close it.

     举例：打開文件，向其中寫入一些數據，然後關閉它

     ```python
     with open('some_file', 'w') as opened_file:
         opened_file.write('Hola!')
         
     Silimar as following code:
         
     file = open('some_file', 'w')
     try:
         file.write('Hola!')
     finally:
         file.close()
     ```

   - **File Example code**
     
     ```python
     #by defining __enter__ and __exit__ methods we can use our new class in a with statement.
     class File(object):
         def __init__(self, file_name, method):
             self.file_obj = open(file_name, method)
         def __enter__(self):
             return self.file_obj
         def __exit__(self, type, value, traceback):
             self.file_obj.close()
     
     with File('demo.txt', 'w') as opened_file:
         opened_file.write('Hola!')
     ```
     
   - Instead of a class, we can implement a Context Manager using a generator function. 
     
     *Example code*
     
     ```python
     from contextlib import contextmanager
     
     @contextmanager
     def open_file(name):
         f = open(name, 'w')
         try:
             yield f
         finally:
             f.close()
     with open_file("test.txt") as f:
         f.write("Hi")
     ```

## Code Practice

learning-path/python-advanced/包含全部的Code Practice。

遇见的挑战难及解决方法和笔记点见注释。

The challenges encountered, solutions, and key points for note-taking are indicated in the comments.

## Resources Used

- [[Python Tips](https://book.pythontips.com/en/latest/args_and_kwargs.html)]: A book of many contents like generators, decorators, mutation and so on.
- [[Real Python](https://realpython.com/primer-on-python-decorators/)]: Primer on Python Decorators.

## Tomorrow's Plan

- [ ] asyncio基础
- [ ] 异步上下文管理器
- [ ] 异步迭代器
- [ ] FastAPI框架入门

## Today's Plan

- [x] 上下文管理器（with语句）

- [x] 面向对象进阶（元类、属性描述符）

- [x] 迭代器和生成器

## Personal Thoughts

Today, my learning progress was faster than yesterday. Interestingly, I found that the knowledge is crossed because yesterday I used the classes although I don't have an insight of it. This made me think the tools are just tools, you may not need to know how it was invented before you used them. Of course, it would be better if you got a clear understanding of the tools. I am glad I am on my way.
