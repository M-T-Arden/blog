# [PythonAdvanced] Learning Notes - Day 2

## Overview

**Date**: 2024-11-28                                     **Time Spent**: 6 hours                    

**Topics**: Decorators                                  **Difficulty**: ⭐⭐⭐ (1-5 ⭐)

## Today's Plan

- [x] asyncio基础

- [x] 异步上下文管理器
- [x] 异步迭代器
- [ ] FastAPI框架入门

## What I Learned Today

1. [**asyncio**]

   - **Main points**: 
     
     asyncio is a library to write **concurrent** code using the **async/await** syntax. asyncio is often a perfect fit for IO-bound and high-level **structured** network code. asyncio provides a set of **high-level** APIs to:
     
     - [run Python coroutines](https://docs.python.org/3.13/library/asyncio-task.html#coroutine) concurrently and have full control over their execution;
     - perform [network IO and IPC](https://docs.python.org/3.13/library/asyncio-stream.html#asyncio-streams);
     - control [subprocesses](https://docs.python.org/3.13/library/asyncio-subprocess.html#asyncio-subprocess);
     - distribute tasks via [queues](https://docs.python.org/3.13/library/asyncio-queue.html#asyncio-queues);
     - [synchronize](https://docs.python.org/3.13/library/asyncio-sync.html#asyncio-sync) concurrent code;
     
     Additionally, there are **low-level** APIs for *library and framework developers* to:
     
     - create and manage [event loops](https://docs.python.org/3.13/library/asyncio-eventloop.html#asyncio-event-loop), which provide asynchronous APIs for [networking](https://docs.python.org/3.13/library/asyncio-eventloop.html#loop-create-server), running [subprocesses](https://docs.python.org/3.13/library/asyncio-eventloop.html#loop-subprocess-exec), handling [OS signals](https://docs.python.org/3.13/library/asyncio-eventloop.html#loop-add-signal-handler), etc;
     - implement efficient protocols using [transports](https://docs.python.org/3.13/library/asyncio-protocol.html#asyncio-transports-protocols);
     - [bridge](https://docs.python.org/3.13/library/asyncio-future.html#asyncio-futures) callback-based libraries and code with async/await syntax.
     
     asyncio 是一个使用**async/await**语法编写**并发**代码的库。asyncio 通常非常适合 IO 绑定和高级 **结构化**网络代码。asyncio 提供一组 **高层级** API 用于:
     
     - 并发地 [运行 Python 协程](https://docs.python.org/zh-cn/3.13/library/asyncio-task.html#coroutine) 并对其执行过程实现完全控制;
     - 执行 [网络 IO 和 IPC](https://docs.python.org/zh-cn/3.13/library/asyncio-stream.html#asyncio-streams);
     - 控制 [子进程](https://docs.python.org/zh-cn/3.13/library/asyncio-subprocess.html#asyncio-subprocess);
     - 通过 [队列](https://docs.python.org/zh-cn/3.13/library/asyncio-queue.html#asyncio-queues) 实现分布式任务;
     - [同步](https://docs.python.org/zh-cn/3.13/library/asyncio-sync.html#asyncio-sync) 并发代码;
     
     此外，还有一些 **低层级** API 以支持 *库和框架的开发者* 实现:
     
     - 创建和管理 [事件循环](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#asyncio-event-loop)，它提供用于 [连接网络](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#loop-create-server), 运行 [子进程](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#loop-subprocess-exec), 处理 [OS 信号](https://docs.python.org/zh-cn/3.13/library/asyncio-eventloop.html#loop-add-signal-handler) 等功能的异步 API；
     - 使用 [transports](https://docs.python.org/zh-cn/3.13/library/asyncio-protocol.html#asyncio-transports-protocols) 实现高效率协议;
     - 通过 async/await 语法 [桥接](https://docs.python.org/zh-cn/3.13/library/asyncio-future.html#asyncio-futures) 基于回调的库和代码。
     
   - **Example code**
     
     ```python
     import asyncio
     
     async def main():
         print('Hello ...')
         await asyncio.sleep(1)
         print('... World!')
     
     asyncio.run(main())
     ```
     
   - **Runners Example code**

     asyncio.**run**(*coro*, ***, *debug=None*)  

     Execute the [coroutine](https://docs.python.org/3.11/glossary.html#term-coroutine) *coro* and return the result. This function runs the passed coroutine, taking care of managing the asyncio event loop, *finalizing asynchronous generators*, and closing the threadpool. If *debug* is `True`, the event loop will be run in debug mode. `False` disables debug mode explicitly. `None` is used to respect the global [Debug Mode](https://docs.python.org/3.11/library/asyncio-dev.html#asyncio-debug-mode) settings.

     #执行[协程](https://docs.python.org/3.11/glossary.html#term-coroutine)*coro*并返回结果。该函数运行传递的协程，负责管理 asyncio 事件循环、*完成异步生成器*并关闭线程池。如果 *debug* 为 `True`，事件循环将运行于调试模式。 `False` 将显式地禁用调试模式。 使用 `None` 将沿用全局 [Debug 模式](https://docs.python.org/zh-cn/3.13/library/asyncio-dev.html#asyncio-debug-mode) 设置。

     ```python
     async def main():
         await asyncio.sleep(1)
         print('hello')
     
     asyncio.run(main())
     ```

   - **Runners Context Example code**

     *class* asyncio.**Runner**(***, *debug=None*, *loop_factory=None*) 

     A context manager that simplifies *multiple* async function calls in the same context. *loop_factory* could be used for overriding the loop creation. It is the responsibility of the *loop_factory* to set the created loop as the current one. By default [`asyncio.new_event_loop()`](https://docs.python.org/3.11/library/asyncio-eventloop.html#asyncio.new_event_loop) is used and set as current event loop with [`asyncio.set_event_loop()`](https://docs.python.org/3.11/library/asyncio-eventloop.html#asyncio.set_event_loop) if *loop_factory* is `None`.

     对在相同上下文中 *多个* 异步函数调用进行简化的上下文管理器。*loop_factory* 可被用来重载循环的创建。 *loop_factory* 要负责将所创建的循环设置为当前事件循环。 在默认情况下如果 *loop_factory* 为 `None` 则会使用 [`asyncio.new_event_loop()`](https://docs.python.org/zh-cn/3.11/library/asyncio-eventloop.html#asyncio.new_event_loop) 并通过 [`asyncio.set_event_loop()`](https://docs.python.org/zh-cn/3.11/library/asyncio-eventloop.html#asyncio.set_event_loop) 将其设置为当前事件循环。

     ```python
     #run(),close(),get_loop()
     async def main():
         await asyncio.sleep(1)
         print('hello')
     
     with asyncio.Runner() as runner:
         runner.run(main())
     ```

   - **Coroutines Example code**

     [Coroutines](https://docs.python.org/3.11/glossary.html#term-coroutine) declared with the async/await syntax is the preferred way of writing asyncio applications.

     通过 async/await 语法来声明 [协程](https://docs.python.org/zh-cn/3.11/glossary.html#term-coroutine) 是编写 asyncio 应用的推荐方式。

     ```python
     import asyncio
     import time
     
     async def say_after(delay, what):
         await asyncio.sleep(delay)
         print(what)
     
     async def main():
         print(f"started at {time.strftime('%X')}")
     
         await say_after(1, 'hello')
         await say_after(2, 'world')
     
         print(f"finished at {time.strftime('%X')}")
     
     >>>asyncio.run(main())
     started at 17:13:52
     hello
     world
     finished at 17:13:55
     ```

     ```python
     async def main():
         task1 = asyncio.create_task(
             say_after(1, 'hello'))
     
         task2 = asyncio.create_task(
             say_after(2, 'world'))
     
         print(f"started at {time.strftime('%X')}")
     
         # Wait until both tasks are completed (should take
         # around 2 seconds.)
         await task1
         await task2
     
         print(f"finished at {time.strftime('%X')}")
     >>>asyncio.run(main())
     started at 17:14:32
     hello
     world
     finished at 17:14:34
     ```

     ```python
     async def main():
         async with asyncio.TaskGroup() as tg:
             task1 = tg.create_task(
                 say_after(1, 'hello'))
     
             task2 = tg.create_task(
                 say_after(2, 'world'))
     
             print(f"started at {time.strftime('%X')}")
     
         # The await is implicit when the context manager exits.
     
         print(f"finished at {time.strftime('%X')}")
     >>>asyncio.run(main())
     started at 17:14:32
     hello
     world
     finished at 17:14:34
     ```

   - 迭代器 (Iterator):
     迭代器是一个对象，它实现了 next() 方法，该方法会返回一个包含两个属性的对象：value 和 done。
     value 表示当前迭代到的值。done 是一个布尔值，表示迭代是否完成（true 表示完成，false 表示未完成）。
     异步迭代器 (Async Iterator):
     异步迭代器是迭代器的异步版本。异步迭代器对象的 anext() 方法返回一个awaitable对象，该对象解析后为 value和done，value是迭代的当前值， done 是布尔值。

   - **AsyncIterator Example code**
     
     ```python
     import asyncio
     
     class AsyncNumberGenerator:
         def __init__(self, max_value):
             self.max_value = max_value
             self.current = 0
     
         def __aiter__(self):
             return self
     
         async def __anext__(self):
             if self.current <= self.max_value:
                 await asyncio.sleep(1) # 模拟异步操作（例如，等待I/O操作）
                 value = self.current
                 self.current += 1
                 return value
             else:
                 raise StopAsyncIteration
     
     # 使用异步迭代器
     async def main():
         async for number in AsyncNumberGenerator(5):
             print(number)  # 每秒打印一个从0到5的数字
     
     # 运行异步主函数
     asyncio.run(main())
     ```

## Code Practice

learning-path/python-advanced/包含全部的Code Practice。

遇见的挑战难及解决方法和笔记点见注释。

The challenges encountered, solutions, and key points for note-taking are indicated in the comments.

## Resources Used

- [FastAPI官方文档](https://fastapi.tiangolo.com/) 
- [asyncio官方文档](https://docs.python.org/3/library/asyncio.html)

## Tomorrow's Plan

- [ ] FastAPI框架入门
- [ ] 异步处理调整MTtranslator

## Personal Thoughts

I felt stressed during today's learning because the official documentation was too complex to understand. More specifically, the documentation was mostly in English, and I struggled with some uncommon expressions and technical concepts. Fortunately, I could turn to AI tools for examples and further explanations, which helped a lot. Another day of learning—thanks to scientific progress!
