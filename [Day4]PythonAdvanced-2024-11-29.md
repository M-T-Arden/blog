# [PythonAdvanced] Learning Notes - Day 4

## Overview

**Date**: 2024-11-29                                     **Time Spent**: 6 hours                    

**Topics**: Decorators                                  **Difficulty**: ⭐⭐⭐ (1-5 ⭐)

## Today's Plan

- [x] FastAPI框架入门

- [x] 修改MTtranslator为异步处理

## What I Learned Today

1. [**Fast API**]

   - **Main points**: 
   
     **FastAPI**是一个用于构建API的现代、快速（高性能）的Web框架。**FastAPI** 使用定义 API 的 **OpenAPI** 标准将你的所有 API 转换成「模式」。架构组成如下：
   
     - **Starlette**：负责Web端（请求路由、并发），需要一个ASGI（Asynchronous Server Gateway Interface，是Python的Web服务器和应用程序服务器之间的接口规范）。它允许开发者使用异步编程模型来处理HTTP请求和响应，以提高服务器的性能和可扩展性。
     - **Pydantic**：负责传入数据校验部分，具体运用到的地方就是类似于参数校验。
   
   - **Example Code**
   
     ```python
     #每当 FastAPI 接收一个使用 GET 方法访问 URL「/」的请求时这个函数会被调用。
     from fastapi import FastAPI
     
     app = FastAPI()
     
     @app.get("/") 
     async def root():
         return {"message": "Hello World"}
     
     ```
   
     ***在cmd/teriminal等终端以uvicorn运行，例**`uvicorn main:app --reload`
   
1. [**Concurrent 并发**]

   - **Main points**: 
     
     它被称为"异步"的原因是因为计算机/程序不必与慢任务"同步"，去等待任务完成的确切时刻，而在此期间不做任何事情直到能够获取任务结果才继续工作。作为一个"异步"系统，一旦完成，任务就可以排队等待一段时间（几微秒），等待计算机程序完成它要做的任何事情，然后回来获取结果并继续处理它们。`async` 关键字用于声明一个异步函数。这样的函数会返回一个 `Promise` 对象。`await` 关键字用于等待一个 `Promise` 完成，并且它会暂停当前 `async` 函数的执行，直到 `Promise` 被解决（resolve）或拒绝（reject）。await可以用来等待多个异步函数进行完成，提高效率。[**Parallel 并行**] 在等待期间无法进行其他操作，只有等待直到完成。
     
   - **Example code**
     
     ```python
     import asyncio
     
     async def fetch_data1():
         await asyncio.sleep(2)  # 模拟网络请求延迟2秒
         print('Data 1 fetched')
     
     async def fetch_data2():
         await asyncio.sleep(1)  # 模拟网络请求延迟1秒
         print('Data 2 fetched')
     
     async def main():
         print('Start fetching...')
         
         # 使用 asyncio.gather 同时运行 fetch_data1 和 fetch_data2
         await asyncio.gather(fetch_data1(), fetch_data2())
         
         # 等待两个异步函数都完成后，再执行后续操作
         print('Both data fetched')
     
     # 运行异步主函数
     asyncio.run(main())
     ```

## Code Practice

```
import asyncio
from fastapi import FastAPI

app=FastAPI()
@app.get('/') #告诉 FastAPI 在它下方的函数负责处理如下访问请求：
#请求路径为 / (根目录) ，使用 get 操作
async def root():
    return {'message':'Hello World'}
#终端命令：uvicorn main:app --reload。
#其中，main是Python文件的名称（不带.py后缀），app是FastAPI实例的名称。
# --reload选项使得代码更改后自动重新加载服务器，非常适合开发时使用。
"""
POST：创建数据。
GET：读取数据。
PUT：更新数据。
DELETE：删除数据。
"""
"""
在FastAPI框架中，不需要（也不应该）自己调用asyncio.run()来运行异步视图函数。
FastAPI和Uvicorn会为您处理异步执行。
"""
#asyncio.run(root())
```

遇见的挑战难及解决方法和笔记点见注释。

The challenges encountered, solutions, and key points for note-taking are indicated in the comments.

## Resources Used

- [FastAPI官方文档](https://fastapi.tiangolo.com/) 
- [asyncio官方文档](https://docs.python.org/3/library/asyncio.html)

## Tomorrow's Plan

- [ ] 常用设计模式（单例、工厂、观察者）
- [ ] 单元测试（pytest）
- [ ] 代码质量工具（pylint, black）
