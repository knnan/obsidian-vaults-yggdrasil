j# Mastering Python’s Asyncio: A Practical Guide | by Moraneus | Medium

![Moraneus](https://miro.medium.com/v2/resize:fill:88:88/1*ajks1Jc86sA6rxAw3BuA2w.jpeg)
[Moraneus](https://medium.com/@moraneus)

When you dive into Python’s world, one gem that truly shines for handling modern web and network tasks is `asyncio`. This toolkit is Python's answer to writing clean, efficient, and scalable code for concurrent I/O operations. It might sound a bit intimidating at first, with its event loops, coroutines, and futures. But once you get the hang of it, you'll wonder how you ever lived without it. So, let's break it down, step by step, with examples and a peek at how things look on the other side of the async fence.

Understanding Asyncio
---------------------

Before jumping into examples, it’s crucial to grasp the core concepts of `asyncio`:

*   **_Event Loop:_** The central execution device provided by `asyncio`. It manages and distributes the execution of different tasks. It's responsible for handling events and scheduling asynchronous routines.
*   **_Coroutines:_** Asynchronous functions declared with `async def`. These functions can be paused and resumed at await points, allowing I/O operations to run in the background.
*   **_Futures:_** Objects that represent the result of work that has not yet been completed. They are returned from tasks scheduled by the event loop.
*   **_Tasks:_** Scheduled coroutines that are wrapped into a Future object by the event loop, allowing their execution.

Getting Started with Asyncio
----------------------------

First off, `asyncio` is all about writing code that can do multiple things at once, without actually doing them at the same time. It’s like having a chef in a kitchen who starts cooking a stew, and knows it’ll take time to simmer, so they begin prepping a salad instead of just standing around. This is the essence of async programming — keep moving efficiently without unnecessary waiting.

The `await` Reserve Keyword
---------------------------

The `await` keyword in Python is an essential part of asynchronous programming, introduced in Python 3.5. It is used to pause the execution of an `async` function until an awaitable object (like coroutines, Tasks, Futures, or I/O) completes, allowing other tasks to run in the meantime. This key feature enables efficient handling of I/O-bound and high-level structured network code.

Understanding `await`
---------------------

*   **_Context:_** `await` can only be used inside `async` functions. Attempting to use it outside such a context results in a syntax error.
*   **_Purpose:_** Its primary purpose is to yield control back to the event loop, suspending the execution of the enclosing coroutine until the awaited object is resolved. This non-blocking behavior is what makes asynchronous programming efficient, especially for I/O-bound tasks.
*   **_Awaitables:_** The objects that can be used with `await` must be awaitable. The most common awaitables are coroutines declared with `async def`, but others include asyncio Tasks, Futures, or any object with an `__await__()` method.

Examples
--------

“ Hello, Async World!”
----------------------

Imagine you’re tasked with printing “Hello, World!” after a 2-second pause. The **synchronous** approach is straightforward:

```
import time
def say_hello():
    time.sleep(2)
    print("Hello, Async World? (not yet)")
say_hello()
```


It does the job, but everything comes to a halt while waiting for those 2 seconds.

Now, let’s switch gears to `asyncio`, showing the asynchronous way:

```python
import asyncio
async def say_hello_async():
    await asyncio.sleep(2)
    print("Hello, Async World!")
asyncio.run(say_hello_async())
```


With `asyncio`, while we wait, the event loop can do other tasks, like checking emails or playing a tune, making our code non-blocking and more efficient:
``
```python
import asyncio
async def say_hello_async():
    await asyncio.sleep(2)  # Simulates waiting for 2 seconds
    print("Hello, Async World!")
async def do_something_else():
    print("Starting another task...")
    await asyncio.sleep(1)  # Simulates doing something else for 1 second
    print("Finished another task!")
async def main():
    # Schedule both tasks to run concurrently
    await asyncio.gather(
        say_hello_async(),
        do_something_else(),
    )
asyncio.run(main())
```


In this modified version, the `main()` function uses `asyncio.gather()` to run `say_hello_async()` and `do_something_else()` concurrently. This means that while the program is waiting for the `say_hello_async()` function to complete its 2-second sleep, it starts and potentially completes the `do_something_else()` function, effectively doing another task during the wait time.

Fetching Web Pages (Concurrent I/O Tasks)
-----------------------------------------

Fetching web pages is a classic example to demonstrate the power of async programming. Let’s compare fetching URLs synchronously vs. asynchronously.

Synchronous HTTP requests is mostly made by the `requests` library, fetching two web pages in a row looks something like this:

```python
import requests
import time
start_time = time.time()
def fetch(url):
    return requests.get(url).text
page1 = fetch('http://example.com')
page2 = fetch('http://example.org')
print(f"Done in {time.time() - start_time} seconds")
# Output: Done in 0.6225857734680176 seconds
```


This code is as simple as it gets, but it waits idly for each request to complete before moving to the next.

Let’s amp up the efficiency with `aiohttp` and `asyncio`which can be used for asynchronous HTTP requests:

```
import aiohttp
import asyncio
import time
async def fetch_async(url, session):
    async with session.get(url) as response:
        return await response.text()
async def main():
    async with aiohttp.ClientSession() as session:
        page1 = asyncio.create_task(fetch_async('http://example.com', session))
        page2 = asyncio.create_task(fetch_async('http://example.org', session))
        await asyncio.gather(page1, page2)
start_time = time.time()
asyncio.run(main())
print(f"Done in {time.time() - start_time} seconds")
# Output: Done in 0.2990539073944092 seconds
```


This async version doesn’t wait around. While one page is being fetched, it starts on the next, drastically cutting down total wait time.

Reading Files (Concurrent I/O Tasks)
------------------------------------

Let’s explore a different use case for concurrent execution with `asyncio`, moving away from web requests. This time, we'll focus on reading multiple files asynchronously. This can be particularly useful when dealing with large files or I/O-bound tasks that do not involve network communication.

In a synchronous setup, reading multiple files one after the other can significantly increase execution time, especially with large files:

```python
# Synchronously reading multiple files
def read_file_sync(filepath):
    with open(filepath, 'r') as file:
        return file.read()
def read_all_sync(filepaths):
    return [read_file_sync(filepath) for filepath in filepaths]
filepaths = ['file1.txt', 'file2.txt']
data = read_all_sync(filepaths)
print(data)
```


For the asynchronous version, we’ll use `aiofiles`, a library that provides support for asynchronous file operations. If you haven't installed `aiofiles` yet, you can do so using pip:

```bash
pip install aiofiles
```


With `aiofiles`, we can perform file I/O operations without blocking the event loop, allowing us to read multiple files concurrently.

```python
import asyncio
import aiofiles
# Asynchronously reading a single file
async def read_file_async(filepath):
    async with aiofiles.open(filepath, 'r') as file:
        return await file.read()
async def read_all_async(filepaths):
    tasks = [read_file_async(filepath) for filepath in filepaths]
    return await asyncio.gather(*tasks)
# Running the async function
async def main():
    filepaths = ['file1.txt', 'file2.txt']
    data = await read_all_async(filepaths)
    print(data)
asyncio.run(main())
```


The asynchronous version, by leveraging `aiofiles` and `asyncio.gather`, allows for concurrent reading of multiple files. This approach significantly reduces the total execution time compared to the synchronous version, which reads each file one after the other. By performing I/O operations concurrently, we can improve the efficiency of programs that need to handle multiple file operations.

Mixing Async and Sync: A Hybrid Approach
----------------------------------------

Sometimes, you can’t escape synchronous functions but still want to enjoy the async ride. Here’s how you can mix them:

```python
import asyncio
import time
def sync_task():
    print("Starting a slow sync task...")
    time.sleep(5)  # Simulating a long task
    print("Finished the slow task.")
async def async_wrapper():
    loop = asyncio.get_running_loop()
    await loop.run_in_executor(None, sync_task)
async def main():
    await asyncio.gather(
        async_wrapper(),
        # Imagine other async tasks here
    )
asyncio.run(main())
```


The provided code snippet demonstrates how to integrate synchronous functions within an asynchronous environment using Python’s `asyncio` library.

**Explain The Code:**

1.  **The Asynchronous Wrapper (**`**async_wrapper**`**function):**

*   This async function demonstrates how to run the synchronous `sync_task` in a way that does not block the event loop. It achieves this by utilizing `loop.run_in_executor(None, sync_task)`.
*   `loop.run_in_executor(None, sync_task)` schedules `sync_task` to run in a separate thread or process, depending on the executor used. The default executor (`None` specified as the first argument) runs tasks in a thread pool.
*   `await` is used to wait for the completion of `sync_task` without blocking the event loop, allowing other asynchronous operations to progress in the meantime.

**2\. Executing Asynchronously (**`**main**` **function):**

*   The `main` async function showcases how to run both synchronous and asynchronous tasks together without blocking.
*   `asyncio.gather` is used to schedule concurrent execution of the `async_wrapper` and potentially other asynchronous tasks. By using `gather`, you ensure that the event loop can manage multiple tasks, running them concurrently where possible.

**3\. Starting the Event Loop (**`**asyncio.run(main())**`**):**

*   Finally, `asyncio.run(main())` is called to run the `main` coroutine, which effectively starts the event loop and executes the tasks scheduled within `main`.

**Why Is This Approach Needed?**

*   **_Integration of Legacy Code:_** In real-world applications, you often encounter legacy code that is synchronous in nature. Rewriting large codebases for async compatibility is not always feasible. This approach allows you to integrate such code into your async applications seamlessly.
*   **_Working with Blocking I/O:_** Some operations, especially those involving blocking I/O, don’t have asynchronous equivalents, or you might be working with third-party libraries that only offer synchronous functions. This technique allows those operations to be offloaded to a thread, freeing the event loop to handle other async tasks.
*   **_CPU-bound Tasks:_** Although CPU-bound tasks are usually better handled by multiprocessing due to Python’s Global Interpreter Lock (GIL), you might sometimes choose to run them in threads for simplicity or because the computational overhead is not excessively high. Using `run_in_executor` allows these tasks to coexist with I/O-bound asynchronous tasks.

The Future() Object
-------------------

In Python’s asynchronous programming model, a `Future` is a low-level awaitable object that represents an eventual result of an asynchronous operation. When you create a Future, you're essentially declaring a placeholder for a result that will be available at some point in the future. Futures are a crucial part of the `asyncio` library, allowing for fine-grained control over asynchronous operations.

Understanding Futures
---------------------

*   **_Role:_** Futures are used to bridge low-level asynchronous operations with high-level asyncio applications. They provide a way to manage the state of an asynchronous operation: pending, finished (with a result), or failed (with an exception).
*   **_Usage_**: Typically, you don’t need to create Futures yourself when using high-level `asyncio` functions and constructs (like Tasks, which are a subclass of Future). However, understanding Futures is essential for interfacing with lower-level async APIs or when building complex asynchronous systems.

Working with Futures
--------------------

A Future object has several key methods and properties:

*   `set_result(result)`: Sets the result of the Future. This will mark it as done and notify all awaiting coroutines.
*   `set_exception(exception)`: Sets an exception as the result of the Future. This also marks it as done but will raise the exception when awaited.
*   `add_done_callback(callback)`: Adds a callback function to be called when the Future is done (either completed with a result or an exception).
*   `result()`: Returns the result of the Future. If the Future is not done, it will raise an `InvalidStateError`. If the Future is completed with an exception, this method will re-raise the exception.
*   `done()`: Returns `True` if the Future is done. A Future is considered done if it has a result or an exception.

```python
import asyncio
# A function to simulate an asynchronous operation using a Future
async def async_operation(future, data):
    await asyncio.sleep(1)  # Simulate some async work with a delay
        # Set the result or exception based on the input data
    if data == "success":
        future.set_result("Operation succeeded")
    else:
        future.set_exception(RuntimeError("Operation failed"))
# A callback function to be called when the Future is done
def future_callback(future):
    try:
        print("Callback:", future.result())  # Attempt to print the result
    except Exception as exc:
        print("Callback:", exc)  # Print the exception if there was one
async def main():
    # Create a Future object
    future = asyncio.Future()
        # Add a callback to the Future
    future.add_done_callback(future_callback)
        # Start the asynchronous operation and pass the Future
    await async_operation(future, "success")  # Try changing "success" to anything else to simulate a failure
        # Check if the Future is done and print its result
    if future.done():
        try:
            print("Main:", future.result())
        except Exception as exc:
            print("Main:", exc)
# Run the main coroutine
asyncio.run(main())
```


How It Works
------------

*   `async_operation` is an async function simulating an asynchronous task that takes a `Future` object and some `data` as arguments. It waits for 1 second to mimic some async work. Based on the `data` value, it either sets a result on the `Future` using `set_result` or raises an exception using `set_exception`.
*   `future_callback` is a callback function that prints the result of the Future once it's done. It checks if the operation succeeded or failed by calling `future.result()`, which either returns the result or re-raises the exception set in the Future.
*   In the `main` coroutine, a Future object is created, and `future_callback` is added as its callback using `add_done_callback`. The `async_operation` is then awaited with the Future and sample data ("success" or any other value to simulate failure).
*   After `async_operation` completed, `main` check if the Future is done using `done()`. It then attempts to print the result directly, handling any potential exceptions.

This example succinctly demonstrates the basic mechanisms of managing asynchronous operations with Futures in Python’s asyncio, including setting results, handling exceptions, using callbacks, and retrieving operation outcomes.

Conclusion
----------

Adopting `asyncio` in Python applications can significantly improve the performance and scalability of I/O-bound and network-driven programs. By understanding and applying the concepts of event loops, coroutines, futures, and tasks, developers can write efficient, non-blocking code that can handle thousands of simultaneous connections with ease. The examples provided in this article, are few, but yet, showcase the versatility of `asyncio` and demonstrate how it can be used to achieve concurrency in Python applications, offering a clear advantage over traditional synchronous code for certain types of tasks.

Your Support Means a Lot! 🙌
----------------------------

If you enjoyed this article and found it valuable, please consider giving it a **_clap_** to show your support. Feel free to explore my other articles, where I cover a wide range of topics related to Python programming and others. By following me, you’ll stay updated on my latest content and insights. I look forward to sharing more knowledge and connecting with you through future articles. Until then, keep coding, keep learning, and most importantly, enjoy the journey!

**Happy programming!**

References
----------