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
