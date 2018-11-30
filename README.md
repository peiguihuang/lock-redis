# lock-redis
在高并发生产环境实际应用过的redis锁，针对指定资源加锁。一般应用场景如：
(1)、线程锁：主要用来给方法、代码块加锁。当某个方法或代码使用锁，在同一时刻仅有一个线程执行该方法或该代码段。线程锁只在同一JVM中有效果，因为线程锁的实现在根本上是依靠线程之间共享内存实现的，比如synchronized是共享对象头，显示锁Lock是共享某个变量（state）。

(2)、进程锁：为了控制同一操作系统中多个进程访问某个共享资源，因为进程具有独立性，各个进程无法访问其他进程的资源，因此无法通过synchronized等线程锁实现进程锁。

(3)、分布式锁：当多个进程不在同一个系统中，用分布式锁控制多个进程对资源的访问。



# 调用方式


@RequestMapping(value = "/lock", method = RequestMethod.GET)
    public Object lock(String id) {
    
        ResultMap rMap = new ResultMap();
        RedisLock lock = new RedisLock(redis, "infun-lock:"+id, 11000, 200000);
        try {
            if(lock.lock()) {
                logger.info("代码成功运行！！！！！");
                Thread.sleep(10000);
                rMap.success();
                return rMap;
            }else {
                rMap.failure();
                rMap.errmsg("资源被占用！！！！！");
                logger.info("资源被占用！！！！！");
                return rMap;
            }
        } catch (Exception e) {
            rMap.failure();
            rMap.errmsg(e.getMessage());
        }finally {
            lock.unlock();
        }
        rMap.success();
        return rMap;
        
    }
    
    
# 重要参数说明
/**
* Detailed constructor.
* lockKey  锁的key
* timeoutMsecs  在这个时间内重试
* expireMsecs   超过这个时间锁自动释放
*/

public RedisLock(RedisTemplate redisTemplate, String lockKey, int timeoutMsecs, int expireMsecs) {

    this(redisTemplate, lockKey, timeoutMsecs);
    
}

    
