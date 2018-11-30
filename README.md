# lock-redis
在高并发生产环境实际应用过的redis锁


调用方式

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
    
    
    重要参数说明
    
    /**
     * Detailed constructor.
     * lockKey  锁的key
     * timeoutMsecs  在这个时间内重试
     * expireMsecs   超过这个时间锁自动释放
     */
    public RedisLock(RedisTemplate redisTemplate, String lockKey, int timeoutMsecs, int expireMsecs) {
        this(redisTemplate, lockKey, timeoutMsecs);
        this.expireMsecs = expireMsecs;
    }

    
