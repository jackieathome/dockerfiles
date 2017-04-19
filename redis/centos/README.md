
## 参考资料

* [dockerfile/redis](https://github.com/dockerfile/redis)
* [sentinel.conf](http://download.redis.io/redis-stable/sentinel.conf)
* [redis.conf](http://download.redis.io/redis-stable/redis.conf)

## 使用样例

```shell
# 构建镜像
docker build . -t redis_centos
# 运行镜像
docker run -d -p 6379:6379 -v <data-dir>:/data --name redis redis_centos
```

