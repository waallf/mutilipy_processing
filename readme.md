[参考](https://blog.51cto.com/10836356/2176371)
### 先创建线程1，再创建线程2，再去启动线程1，2,。并和主线程同时运行。若子线程先于主线程完毕，则子线程先关闭后主线程运行完毕关闭，若主线程先于子线程运行完毕，则主线程需要等待所有的子线程运行完毕后再关闭。 代码如下。  
```
import threading
import time
def music(name):
    print('%s begin listen music%s'%(name,time.ctime()))
    time.sleep(3)
    print('%s stop listen music%s' % (name, time.ctime()))
def game(name):
    print('%s begin play game%s'%(name,time.ctime()))
    time.sleep(3)
    print('%s stop play game%s' % (name,time.ctime()))
if __name__ == '__main__':
    # threadl = []
    # t1 = threading.Thread(target=music,args=('zhang',))
    # t2 = threading.Thread(target=game,args=('zhang',))
    # t1.start()
    # t2.start()
    music('zhang')
    game('zhang')
    print('Ending now %s'%time.ctime())
```
### 主线程等待某子线程结束后才执行(join函数)  
```
import threading
import time
def music(name):
    print('%s begin listen music%s'%(name,time.ctime()))
    time.sleep(5)
    print('%s stop listen music%s' % (name, time.ctime()))
def game(name):
    print('%s begin play game%s'%(name,time.ctime()))
    time.sleep(3)
    print('%s stop play game%s' % (name,time.ctime()))
if __name__ == '__main__':
    threadl = []    #线程列表，用例存放线程
    #产生线程的实例
    t1 = threading.Thread(target=music,args=('zhang',)) #target是要执行的函数名（不是函数），args是函数对应的参数，以元组的形式；
    t2 = threading.Thread(target=game,args=('zhang',))
    threadl.append(t1)
    threadl.append(t2)
    #循环列表，依次执行各个子线程
    for x in threadl:
        x.start()
    #将最后一个子线程阻塞主线程，只有当该子线程完成后主线程才能往下执行
    x.join()
    print('Ending now %s'%time.ctime())
```  
### 线程守护  
前面不管是否用到了join()函数，主线程最后要等到所有的子线程结束且自己也执行完才能关闭。使用线程守护，可以不管某个线程的使用情况，只要其他子线程结束且主线程执行完毕，主线程都会关闭。注意:setDaemon方法必须在start之前且要带一个必填的布尔型参数.代码如下   
```
import threading
import time
def music(name):
    print('%s begin listen music%s'%(name,time.ctime()))
    time.sleep(2)
    print('%s stop listen music%s' % (name, time.ctime()))
def game(name):
    print('%s begin play game%s'%(name,time.ctime()))
    time.sleep(5)
    print('%s stop play game%s' % (name,time.ctime()))
if __name__ == '__main__':
    threadl = []    #线程列表，用例存放线程
    #产生线程的实例
    t1 = threading.Thread(target=music,args=('zhang',)) #target是要执行的函数名（不是函数），args是函数对应的参数，以元组的形式；
    t2 = threading.Thread(target=game,args=('zhang',))
    threadl.append(t1)
    threadl.append(t2)
    #循环列表，依次执行各个子线程
    t2.setDaemon(True) #t2线程守护
    for x in threadl:
        x.start()
    #将子线程t1阻塞主线程，只有当该子线程完成后主线程才能往下执行
    print('Ending now %s'%time.ctime())
```
## GIL(global interpreter lock),无论多少个CPU，开启多少个线程，每次只能执行一个线程。  
有的任务使用多线程以后反而时间会加强，因为我们把任务分为I/O密集型和计算密集型，而多线程在切分中又分为I/O切换和时间切换。如果任务是I/O密集型，若不采用多线程，在进行I/O操作时，CPU会有大量的闲置状态。如果CPU一直在进行工作，如果进行多线程切换时，可能会造成时间和资源的浪费。  
# 新建少量进程，可以如下：　　
```
import multiprocessing
import time

def func(msg):
    for i in xrange(3):
        print msg
        time.sleep(1)

if __name__ == "__main__":
    p = multiprocessing.Process(target=func, args=("hello", ))
    p.start()
    p.join()
    print "Sub-process done."
```  
# 使用进程池　　
```
import multiprocessing
import time

def func(msg):
    for i in xrange(3):
        print msg
        time.sleep(1)

if __name__ == "__main__":
    pool = multiprocessing.Pool(processes=4)
    for i in xrange(10):
        msg = "hello %d" %(i)
        pool.apply_async(func, (msg, ))
    pool.close()
    pool.join()
    print "Sub-process(es) done."
```
# 使用pool，并需要关注结果　　
```
import multiprocessing
import time

def func(msg):
    for i in xrange(3):
        print msg
        time.sleep(1)
    return "done " + msg
if __name__ == "__main__":
    pool = multiprocessing.Pool(processes=4)
    result = []
    for i in xrange(10):
        msg = "hello %d" %(i)
        result.append(pool.apply_async(func, (msg, )))
    pool.close()
    pool.join()
    for res in result:
        print res.get()
    print "Sub-process(es) done."
```   

