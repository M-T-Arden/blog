# [PythonAdvanced] Learning Notes - Day 1

## Overview

**Date**: 2024-11-26                                     **Time Spent**: 6 hours                    

**Topics**: Decorators                                  **Difficulty**: ⭐⭐⭐☆☆ (1-5 stars)

## What I Learned Today

1. [***args** **&** ****kwargs**]
   
   - **Main points**: (使用中`*`是必须的，名称可以根据个人习惯更换)
     
     *args is used to send a non-keyworded variable length argument list to the function. `*args`用来传递没有keyword的值。
     
     **kwargs allows you to pass keyworded variable length of arguments to a function. `**kwargs`是用来传递有keyword的值。
   
   - ***args Example code**
     
     ```python
     def test_var_args(f_arg, *argv):
      print("first normal arg:", f_arg)
      for arg in argv:
      print("another arg through *argv:", arg)
     
     >>> test_var_args('yasoob', 'python', 'eggs', 'test')
     first normal arg: yasoob
     another arg through *argv: python
     another arg through *argv: eggs
     another arg through *argv: test
     ```
   
   - ****kwargs Example code**
     
     ```python
     def greet_me(**kwargs):
         for key, value in kwargs.items():#遍历字符串
             print("{0} = {1}".format(key, value))#以key=value的格式化形式输出
     
     >>> greet_me(name="yasoob") //key=name, value=yasoob
     name = yasoob
     ```

2. [Decorators]
   
   - **Main points**: By definition, a decorator is a function that takes another function and extends the behavior of the latter function without explicitly modifying it.
   
   - **Example code**
     
     ```python
     def decorator(func):
      def wrapper():
          print("Something is happening before the function is called.")
          func()
          print("Something is happening after the function is called.")
      return wrapper 
     def say_whee():
      print("Whee!") 
     say_whee = decorator(say_whee)
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
     
     ```python
     def do_twice(func):
         def wrapper_do_twice(*args, **kwargs):
             func(*args, **kwargs)
             func(*args, **kwargs)
             return func(*args, **kwargs)
         return wrapper_do_twice
     
     @do_twice
     def return_greeting(name):
         print("Creating greeting")
         return f"Hi {name}"
     
     >>> return_greeting("Adam")
     Creating greeting
     Creating greeting
     'Hi Adam'
     ```
   
   - **Example code**
     
     ```python
     import functools
     def do_twice(func):
         @functools.wraps(func) 
         def wrapper_do_twice(*args, **kwargs):
             func(*args, **kwargs)
             return func(*args, **kwargs)
         return wrapper_do_twice
     @do_twice
     def say_whee():
         print("Whee!")
     
     >>> say_whee
     <function say_whee at 0x7ff79a60f2f0>
     
     >>> say_whee.__name__
     'say_whee'
     
     >>> help(say_whee)
     Help on function say_whee in module whee:
     ```
   
   - **Example code**  
     
     ```python
     import functools
     
     def decorator(func):
         @functools.wraps(func)
         def wrapper_decorator(*args, **kwargs):
             # Do something before
             value = func(*args, **kwargs)
             # Do something after
             return value
         return wrapper_decorator
     ```
   
   - **Timer Example Code**
     
     ```python
     import functools
     import time
     
     def timer(func):
         """Print the runtime of the decorated function"""
         @functools.wraps(func)
         def wrapper_timer(*args, **kwargs):
             start_time = time.perf_counter()
             value = func(*args, **kwargs)
             end_time = time.perf_counter()
             run_time = end_time - start_time
             print(f"Finished {func.__name__}() in {run_time:.4f} secs")
             return value
         return wrapper_timer
     
     @timer
     def waste_some_time(num_times):
         for _ in range(num_times):
              sum([number**2 for number in range(10_000)])
     
     >>> waste_some_time(1)
     Finished waste_some_time() in 0.0010 secs
     
     >>> waste_some_time(999)
     Finished waste_some_time() in 0.3260 secs
     ```

## Code Practice

learning-path/python-advanced/Day1-Decorators.py包含全部的Code Practice以及小作业timer装饰器。

遇见的挑战难及解决方法和笔记点见注释。

The challenges encountered, solutions, and key points for note-taking are indicated in the comments.

## Resources Used

- [[Python Tips](https://book.pythontips.com/en/latest/args_and_kwargs.html)]: A book of many contents like generators, decorators, mutation and so on.
- [[Real Python](https://realpython.com/primer-on-python-decorators/)]: Primer on Python Decorators.

## Tomorrow's Plan

- [ ] 上下文管理器（with语句）

- [ ] 面向对象进阶（元类、属性描述符）

- [ ] 迭代器和生成器

## Personal Thoughts

Today, my learning progress was slower than I thought. Although I had completed some small projects with the help of AI tools, I realized that I lacked a solid foundation of certain concepts I had been using. When I learned about these knowledge gaps, I discovered how much more there is to learn. As I studied, I gained a deeper insight into the tools and coding logic. The journey has been challenging, but ultimately rewarding. I hope to continue making progress tomorrow and complete my plan in time.
