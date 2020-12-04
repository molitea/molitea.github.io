---
title: Python 线程池
tags: [Python, threadpool, futures]
---

**什么是线程池**

Web服务器、数据库服务器、文件服务器、邮件服务器等许多服务器应用都面向处理来自某些远程来源的**大量短小的任务**。构建服务器应用程序的一个过于简单的模型是：每当一个请求到达就创建一个新的服务对象，然后在新的服务对象中为请求服务。但当有大量请求并发访问时，服务器不断的创建和销毁对象的开销很大。所以提高服务器效率的一个手段就是尽可能减少创建和销毁对象的次数，特别是一些很耗资源的对象创建和销毁，这样就引入了“池”的概念，“池”的概念使得人们可以定制一定量的资源，然后对这些资源进行复用，而不是频繁的创建和销毁。

线程池是预先创建线程的一种技术。线程池在还没有任务到来之前，创建一定数量的线程，放入空闲队列中。这些线程都是处于睡眠状态，即均为启动，不消耗CPU，而只是占用较小的内存空间。当请求到来之后，缓冲池给这次请求分配一个空闲线程，把请求传入此线程中运行，进行处理。当预先创建的线程都处于运行状态，即预制线程不够，线程池可以自由创建一定数量的新线程，用于处理更多的请求。当系统比较闲的时候，也可以通过移除一部分一直处于停用状态的线程。

**线程池的注意事项**

虽然线程池是构建多线程应用程序的强大机制，但使用它并不是没有风险的。在使用线程池时需注意线程池大小与性能的关系，注意并发风险、死锁、资源不足和线程泄漏等问题。

**1）线程池大小**

多线程应用并非线程越多越好，需要根据系统运行的软硬件环境以及应用本身的特点决定线程池的大小。一般来说，如果代码结构合理的话，线程数目与CPU 数量相适合即可。如果线程运行时可能出现阻塞现象，可相应增加池的大小；如有必要可采用自适应算法来动态调整线程池的大小，以提高CPU 的有效利用率和系统的整体性能。

**2）并发错误**

多线程应用要特别注意并发错误，要从逻辑上保证程序的正确性，注意避免死锁现象的发生。

**3）线程泄漏**

这是线程池应用中一个严重的问题，当任务执行完毕而线程没能返回池中就会发生线程泄漏现象。

**线程池的设计**

一个典型的线程池，应该包括如下几个部分：

1、线程池管理器（ThreadPool），用于启动、停用，管理线程池

2、工作线程（WorkThread），线程池中的线程

3、请求接口（WorkRequest），创建请求对象，以供工作线程调度任务的执行

4、请求队列（RequestQueue），用于存放和提取请求

5、结果队列（ResultQueue），用于存储请求执行后返回的结果

线程池管理器，通过添加请求的方法（putRequest）向请求队列（RequestQueue）添加请求，这些请求事先需要实现请求接口，即传递工作函数、参数、结果处理函数、以及异常处理函数。

接着，初始化一定数量的工作线程，这些线程通过轮询的方式不断查看请求队列（RequestQueue），只要有请求存在，则会提取出请求，进行执行。

然后，线程池管理器调用方法（poll）查看结果队列（resultQueue）是否有值，如果有值，则取出，调用结果处理函数执行。

通过以上讲述，不难发现，这个系统的核心资源在于请求队列和结果队列，工作线程通过轮询requestQueue获得任务，主线程通过查看结果队列，获得执行结果。

**如何实现线程池**？

这里，我分别介绍两种实现方式：

**第一种：threadpool（老配方）**

使用threadpool模块，这是个python的第三方模块，支持python2和python3，具体使用方式如下：

```python
from threadpool import *
import random
import time
import datetime

# 自己在ThreadPool类中添加的方法
# def workersize(self):
#         return len(self.workers)

# def stop(self):
#     '''join 所有的thread,确保所有的线程都执行完毕'''
#     self.dismissWorkers(self.workersize(), True)
#     self.joinAllDismissedWorkers()

def do_work(data):
    time.sleep(random.randint(1,3))
    res = str(datetime.datetime.now()) + "" + str(data)
    return res

def print_result(request, result):
    print "---Result from request %s : %r" % (request.requestID, result)


if __name__ == '__main__':
    main = ThreadPool(3)
     
    reqs = makeRequests(do_work, [i for i in range(20)], print_result)
    for i, req in enumerate(reqs):
        main.putRequest(req)
        print "(%d): work request #%s added." % (i, req.requestID)

    # for i in range(20):
    #     req = WorkRequest(do_work, args=[i], callback=print_result)
    #     # req = WorkRequest(do_work, args=(i,), callback=print_result)        # ok too
    #     main.putRequest(req)
    #     print "(%d): work request #%s added." % (i, req.requestID)
     
    print '-' * 20, main.workersize(), '-' * 20
     
    counter = 0
    while True:
        try:
            time.sleep(0.5)
            main.poll()
            if(counter == 5):
                print "Add 3 more workers threads"
                main.createWorkers(3)
                print '-' * 20, main.workersize(), '-' * 20
            if(counter == 10):
                print "dismiss 2 workers threads"
                main.dismissWorkers(2)
                print '-' * 20, main.workersize(), '-' * 20
            counter += 1
        except NoResultsPending:
            print "no pending results"
            break
     
    main.stop()
    print "Stop & End."
```

threadpool是一个比较老的模块了，现在虽然还有一些人在用，但已经不再是主流了，关于python多线程，现在已经开始步入未来（futures模块）了。

**第二种：futures（新配方）**

使用concurrent.futures模块，这个模块是python3中自带的模块，在python2.7以上的版本需要安装（pip install futures）才能使用。具体使用如下：

```python
from  concurrent.futures import ThreadPoolExecutor
import time

def task(name, seconds):
    print '%s sleep %s seconds start...' % (name, seconds)
    time.sleep(seconds)
    print '%s sleep %s seconds done' % (name, seconds)
    return '%s task done' % name

with ThreadPoolExecutor(max_workers=5) as t:
    task1 = t.submit(task, 'xxxx', 1)
    task2 = t.submit(task, 'zzzz', 2)
    task3 = t.submit(task, 'yyyy', 2)
    task4 = t.submit(task, 'mmmm', 3)
    print(task1.done())
    print(task2.done())
    print(task3.done())
    print(task4.done())
    time.sleep(2)
    print(task1.done())
    print(task2.done())
    print(task3.done())
    print(task4.done())

    print(task1.result())
    print(task2.result())
    print(task3.result())
    print(task4.result())
```

- 使用 with 语句 ，通过 ThreadPoolExecutor 构造实例，同时传入 max_workers 参数来设置线程池中最多能同时运行的线程数目。
- 使用 submit 函数来提交线程需要执行的任务到线程池中，并返回该任务的句柄（类似于文件、画图），注意 submit() 不是阻塞的，而是立即返回。
- 通过使用 done() 方法判断该任务是否结束。上面的例子可以看出，提交任务后立即判断任务状态，显示四个任务都未完成。在延时2.5后，task1 和 task2 执行完毕，task3 仍在执行中。
- 使用 result() 方法可以获取任务的返回值 **【注意result 是阻塞的会阻塞主线程】**

concurrent.futures中的常用方法：

**wait**

```python
wait(fs, timeout=None, return_when=ALL_COMPLETED)
```

fs: 表示需要执行的序列；
timeout: 等待的最大时间，如果超过这个时间即使线程未执行完成也将返回；
return_when：表示wait返回结果的条件，默认为 ALL_COMPLETED 全部执行完成再返回；可指定FIRST_COMPLETED 当第一个执行完就退出阻塞。

```python
from  concurrent.futures import ThreadPoolExecutor,wait,FIRST_COMPLETED, ALL_COMPLETED
import time

task_args_list = [('zhangsan', 1),('lishi',2), ('wangwu', 3)]
task_list = []


def task(name, seconds):
    print('% sleep %s seconds start...' % (name, seconds))
    time.sleep(seconds)
    print('% sleep %s seconds done' % (name, seconds))
    return '%s task done' % name

with ThreadPoolExecutor(max_workers=5) as t:
    [task_list.append(t.submit(task, *arg)) for  arg in task_args_list]
    wait(task_list, return_when=FIRST_COMPLETED)
    print('all_task_submit_complete! and First task complete!')
    print(wait(task_list,timeout=1.5))
    
# 运行结果
zhangsanleep 1 seconds start...
lishileep 2 seconds start...
wangwuleep 3 seconds start...
zhangsanleep 1 seconds done
all_task_submit_complete! and First task complete!
lishileep 2 seconds done
DoneAndNotDoneFutures(done=set([<Future at 0x33b8708 state=finished returned str>, <Future at 0x33b8d08 state=finished returned str>]), not_done=set([<Future at 0x33be288 state=running>]))
wangwuleep 3 seconds done
```

**as_completed**

上面虽提供了done()方法判断任务是否结束的方法，但是不能在主线程中一直判断。最好的方法是当某个任务结束了，就给主线程返回结果，而不是一直判断每个任务是否结束。

concurrent.futures 中 的 as_completed() 就是这样一个方法，当子线程中的任务执行完后，直接用 result() 获取返回结果。

```python
from  concurrent.futures import ThreadPoolExecutor, as_completed
import time

task_args_list = [('zhangsan', 1),('lishi',3), ('wangwu', 2)]
task_list = []

def task(name, seconds):
    print('%s sleep %s seconds start...' % (name, seconds))
    time.sleep(seconds)
    return '%s sleep %s seconds done' % (name, seconds)

with ThreadPoolExecutor(max_workers=5) as t:
    [task_list.append(t.submit(task, *arg)) for arg in task_args_list]
    for future in as_completed(task_list):
        print(future.result())

    print('All Task Done!!!!!!!!!')
    
# 运行结果
zhangsan sleep 1 seconds start...
lishi sleep 3 seconds start...
wangwu sleep 2 seconds start...
zhangsan sleep 1 seconds done
wangwu sleep 2 seconds done
lishi sleep 3 seconds done
All Task Done!!!!!!!!!
```

**map**

```python
map(fn, *iterables, timeout=None)
```

fn： 第一个参数 fn 是需要线程执行的函数；
iterables：第二个参数接受一个可迭代对象；
timeout： 第三个参数 timeout 跟 wait() 的 timeout 一样，但由于 map 是返回线程执行的结果，如果 timeout小于线程执行时间会抛异常 TimeoutError。

```python
def spider(page):
    time.sleep(page)
    return page

start = time.time()
executor = ThreadPoolExecutor(max_workers=4)

i = 1
for result in executor.map(spider, [2, 3, 1, 4]):
    print("task{}:{}".format(i, result))
    i += 1

#  运行结果
task1:2
task2:3
task3:1
task4:4
```

使用 map 方法，无需提前使用 submit 方法，map 方法与 python 高阶函数 map 的含义相同，都是将序列中的每个元素都执行同一个函数。上面的代码对列表中的每个元素都执行 spider() 函数，并分配各线程池。

可以看到map执行结果与上面的 as_completed() 方法的结果不同，输出顺序和列表的顺序相同，就算 1s 的任务先执行完成，也会先打印前面提交的任务返回的结果。