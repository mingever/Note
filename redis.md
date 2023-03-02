## docker搭建redis

1. 拉取

``` bash
docker pull redis
```

2. 安装

``` bash
docker run --restart=always \
-d -p 6379:6379 \
--name redis \
-v /root/redis/conf/redis.conf:/etc/redis/redis.conf \
-v /root/redis/data:/data \
redis:6.0.16 \
redis-server /etc/redis/redis.conf \
--requirepass 169736

#说明：
#–restart=always                     总是开机启动
#redis-server /etc/redis/redis.conf  以配置文件启动redis，加载容器内的conf文件
#–appendonly yes                     开启aof持久化，默认是rdb
#–requirepass 169736                 设置密码(向外开放，一定要设置密码，否则会有挖矿木马)
#直接创建的redis容器里是没有配置文件的，该配置文件是需要在创建容器时映射进来的，需要从官网下载。和nginx容器内自带配置文件不一样
#redis.conf中，daemoinze要设置为no，禁止后台启动，否咋会与docker的 -d发送冲突
#-v /root/redis-demo/conf:/etc/redis 也可以把配置文件复制到文件夹里，然后直接挂载文件夹。推荐挂载文件夹，挂载文件的话，因为inode映射，需要为宿主文件赋予777权限才不需要重启，chmod 777 redis.conf。
```

[docker挂载文件和文件夹相关问题 ](https://segmentfault.com/a/1190000015684472#:~:text=)



## redis.conf

**网络**

``` bash
bind 127.0.0.1 # 绑定的本机的ip，远程连接需要注释
protected-mode yes # 保护模式 不是本地需要改为no
port 6379 # 端口设置
```

protected-mode

1. 作用：禁止公网访问redis，加强redis安全
2. 启动条件：保护模式已打开==&&==未指定bind==&&==未指定密码

如

``` bash
protected-mode yes // 打开保护模式
#bind 127.0.0.1 //不绑定任何网络接口
#requirepass xiaoyi //不设置密码
```

**通用 GENERAL**

``` bash
daemonize yes # 以守护进程的方式运行，默认是 no，我们需要自己开启为yes！docker需要为no，否则会与-d冲突
pidfile /var/run/redis_6379.pid # 如果以后台的方式运行，我们就需要指定一个 pid 文件！
loglevel notice
logfile "" # 日志的文件位置名
databases 16 # 数据库的数量，默认是 16 个数据库
always-show-logo yes # 是否总是显示LOGO
```

**快照**

持久化， 在规定的时间内，执行了多少次操作，则会持久化到文件 .rdb. aof

redis 是内存数据库，如果没有持久化，那么数据断电及失

``` bash
# 如果900s内，如果至少有一个1 key进行了修改，我们及进行持久化操作
save 900 1
# 如果300s内，如果至少10 key进行了修改，我们及进行持久化操作
save 300 10
# 如果60s内，如果至少10000 key进行了修改，我们及进行持久化操作
save 60 10000
# 我们之后学习持久化，会自己定义这个测试！
stop-writes-on-bgsave-error yes # 持久化如果出错，是否还需要继续工作！
rdbcompression yes # 是否压缩 rdb 文件，需要消耗一些cpu资源！
rdbchecksum yes # 保存rdb文件的时候，进行错误的检查校验！
dir ./ # rdb 文件保存的目录！
```

**REPLICATION 复制**

``` bash
#主从复制
masterauth <master-password> #主机密码
replica-read-only yes #从服务器只读
```

**SECURITY 安全**

``` bash
requirepass #密码
#也可以直接在命令行config set requirepass "123456" 来设置密码
```

**限制 CLIENTS**

``` bash
maxclients 10000 # 设置能连接上redis的最大客户端的数量
maxmemory <bytes> # redis 配置最大的内存容量
maxmemory-policy noeviction # 内存到达上限之后的处理策略
1、volatile-lru：只对设置了过期时的key进行LRU（默认值）
2、allkeys-lru ： 删除lru算法的key
3、volatile-random：随机删除即将过期key
4、allkeys-random：随机删除
5、volatile-ttl ： 删除即将过期的
6、noeviction ： 永不过期，返回错误
```

**APPEND ONLY 模式 **

``` bash
#aof配置
appendonly no # 默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分所有的情况下，rdb完全够用！
appendfilename "appendonly.aof" # 持久化的文件的名字
# appendfsync always # 每次修改都会 sync。消耗性能
appendfsync everysec # 每秒执行一次 sync，可能会丢失这1s的数据！
# appendfsync no # 不执行 sync，这个时候操作系统自己同步数据，速度最快！
```



## 主从复制

### 简介

在实际项目里，一般不会简单地只在一台服务器上部署Redis服务器，因为单台Redis服务器不能满足高并发的压力，另外如果该服务器或Redis服务器失效，整个系统就可能崩溃。

在主从复制的集群里，主节点一般是一个，从节点一般是两个或多个，写入主节点的数据会被复制到从节点上，这样一旦主节点出现故障，应用系统就能切换到从节点去读写数据，提升系统的可用性。再采用主从复制模式里默认的读写分离机制，就能提升系统的缓存读写性能。

### 主从复制模式概述

在实际应用中，如果有响应的设置，在向一台Redis服务器里写数据后，这个数据可以复制到另一台(或多台)Redis服务器，这里数据源服务器叫主服务器(Master Server),复制数据目的地所在的服务器叫做从服务器(Slave Server)。

好处：

- 把写操作集中到主服务器上，把读操作集中到从服务器上，以提升读写性能
- 由于出现了备份，因此能提升数据库的安全性，比如当主Redis服务器失效后，能很快切换到从服务器上读数据

注意：

- 一个主服务器可以带一个或多个从服务器，从服务器可以再带从服务器，但在复制数据时只能把主服务器的数据复制到从服务器上，反之不能
- 一台服务器只能跟随一台主服务器，不能出现一丛多主的模式
- 在Redis2.8以后的版本里，采用异步的复制模式，即进行主从复制时不会影响主服务器上的读写数据操作

### 命令方式搭建集群

在配置主从关系时，需要从节点上使用`slaveof`命令

项目中主从主配置应该在配置文件中配置，这样的话是永久的，==用命令形式的话，是暂时的==，重启会恢复。

> 注：==配置带有密码认证的哨兵集群时，需要将主从节点的密码都设置成一样的==

#### 创建一台主服务器

``` bash
docker run --restart=always \
-d -p 6379:6379 \
--name redis01 \
-v /root/redis-cluster/conf/redis01.conf:/etc/redis/redis.conf \
-v /root/redis-cluster/data01:/data \
redis:6.0.16 \
redis-server /etc/redis/redis.conf \
--requirepass 169736
```

#### 创建两台从服务器

``` bash
docker run --restart=always \
-d -p 6380:6379 \
--name redis02 \
-v /root/redis-cluster/conf/redis02.conf:/etc/redis/redis.conf \
-v /root/redis-cluster/data02:/data \
redis:6.0.16 \
redis-server /etc/redis/redis.conf \
--requirepass 169736
```

``` bash
docker run --restart=always \
-d -p 6381:6379 \
--name redis03 \
-v /root/redis-cluster/conf/redis03.conf:/etc/redis/redis.conf \
-v /root/redis-cluster/data03:/data \
redis:6.0.16 \
redis-server /etc/redis/redis.conf \
--requirepass 169736
```

#### 配置主从复制

**1）查看redis01容器信息**

通过`docker inspect redis01`命令查看redis-master容器的信息，在其中能通过==IPAddress==项看到该容器的IP地址，这里是172.17.0.2。在真实项目里，Redis服务器所在的IP地址是固定的，而通过Docker容器启动的Redis服务器的IP地址是动态的，所以这里要用上述命来获取IP地址。

**2）指定从节点的主机**

在redis-slave1和redis-slave2容器的命令窗口里运行如下的slaveof命令，指定当前Redis服务器为从服务器。
该命令的格式是`slaveof IP端口号`，这里指向172.17.0.2:6379所在的主服务器。

``` bash
slaveof 172.17.0.2 6379
```

==如果主服务器设置了密码的话，需要更改从机的配置文件==：

在从redis服务器上的配置文件redis.conf中找到：masterauth \<master-password>这一行，然后在这一行的下面写上`masterauth 169736`(主redis服务器密码)。这时候我们再分别重启主redis服务器和从redis服务器。

如果要设置密码的话，==主从节点的密码都要设置成一样的==，==并且主机也要添加masterauth==，不然主机宕机无法自动切换成从节点。

**3）在主机中通过info replication查看主从配置信息**

``` bash
# Replication
role:master #角色为主机
connected_slaves:2 #有两台从机
slave0:ip=172.17.0.4,port=6379,state=online,offset=126,lag=0 #从机的信息
slave1:ip=172.17.0.3,port=6379,state=online,offset=126,lag=0
master_replid:8ae56d8e025804ff07d45f185b36810a22629d39
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:126
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:126
```

### 通过配置文件搭建集群

1. 创建docker时需要通过 -v来挂载`/etc/redis/redis.conf`配置文件，并且通过`redis-server /etc/redis/redis.conf `来通过配置文件启动redis
2. 从机配置文件里需要加上`slaveof 172.17.0.2 6379`



## 哨兵模式

### 概述

基于主从复制模式的集群在发生故障时可能会出现数据丢失等情况，因为当主服务器发生故障后，需要手动进行数据恢复动作，并要重新设置主从关系，比较麻烦。

可以在主从复制的基础上引入“哨兵(sentinel)”机制，一方面用哨兵远程监控主从服务器是否可用，另一方面当主服务器发生故障时通过哨兵机制可以实现“故障自动恢复”效果。

一般来说，哨兵机制会和主从复制模式整合使用，在基于哨兵的模式里会在一台或多台服务器上引入哨兵进程，这些节点也叫哨兵节点。

哨兵节点一般不存储数据，它的作用是监控主从模式里的主服务器节点。当哨兵节点监控的主服务器发生故障时，哨兵节点会主导“故障自动恢复”流程，具体来讲就是会在该主服务器下属的从服务器里选出一个新的主服务器，并完成响应的数据和配置更改等动作。

也就是说，如果采用这种模式，可以让故障自动修复，从而提升系统的可用性。在项目里，一般会配置多个主从模式集群，所以会引入多个哨兵节点。如下图所示

<img src="https://oss.mingever.com/note/redis/redis-sentinel.png" style="zoom: 80%;" />

### 搭建哨兵模式集群

#### 搭建主二复制集群

先搭建一个一主二从的redis主从集群

其中redis01为主机，redis02和redis03为两个从机

#### 建立第一个哨兵节点

**1）在/root/redis-cluster/sentinel下新建sentinel01.conf配置文件**

``` bash
port 16379
sentinel monitor master 172.17.0.2 6379 2
sentinel auth-pass master 169736
dir /
logfile "sentinel01.log"

#port 16379   哨兵节点的工作端口为16379
#sentinel monitor master 172.17.0.5 6382 2  指定监控对象，其中master是哨兵节点为监控服务器指定的名字，172.17.0.2和6379分别是redis01这台主服务器的IP地址和端口号，最后的2表示至少需要2台哨兵节点认可才能认定该主服务器失效。
#sentinel auth-pass master 169736 表示服务器的密码，注意要放到sentinel monitor master ip port 后面,因为名称master要先定义
#dir 日志文件目录
#logfile 日志文件名称
```

**2）新建redis-sentinel01容器**

``` bash
docker run -itd --privileged=true \
-p 16379:16379 \
--name redis-sentinel01 \
-v /root/redis-cluster/sentinel:/etc/sentinel \
redis:6.0.16 \
redis-sentinel /etc/sentinel/sentinel01.conf

#通过-v挂在了/root/redis-cluster/sentinel目录，同时使用-p指定了Docker容器16379端口映射宿主机16369端口。
#用redis-sentinel命令启动了哨兵节点，启动时加载了sentinel01.conf文件。
```

**3）查看哨兵节点信息**

通过`docker exec -it redis-sentinel01 /bin/bash`命令进入Docker容器里的命令行，然后用`redis-cli -h 127.0.0.1 -p 16379`命令进入Redis客户端，在Redis客户端里，用`info sentinel`命令查看哨兵节点的信息

``` bash
# Sentinel
sentinel_masters:1
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
sentinel_simulate_failure_flags:0
master0:name=master,status=ok,address=172.17.0.2:6379,slaves=2,sentinels=1

#从最后一行可以看到，该哨兵节点监控的主服务器状态(status)是ok,slaves数量为2，即该主服务器有两个从服务器，这和之前的配置情况是一致的。
```

#### 建立第二个哨兵节点

与建立第一个节点步骤基本一致

``` bash
port 16380
sentinel monitor master 172.17.0.2 6379 2
sentinel auth-pass master 169736
dir /
logfile "sentinel02.log"

#改一下端口名，和日志名
```

``` bash
docker run -itd --privileged=true \
-p 16380:16380 \
--name redis-sentinel02 \
-v /root/redis-cluster/sentinel:/etc/sentinel \
redis:6.0.16 \
redis-sentinel /etc/sentinel/sentinel02.conf
```

进入容器，查看信息

``` bash
# Sentinel
sentinel_masters:1
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
sentinel_simulate_failure_flags:0
master0:name=master,status=ok,address=172.17.0.2:6379,slaves=2,sentinels=2

#一主两从，两哨兵
```

### 哨兵的常用配置

**1）指定下线时间的阈值**

`sentinel down-after-millseconds master 60000`

其中，master表示该哨兵节点监控的服务器名，需要和sentinel monitor配置项里指定的服务器名保持一致，而60000表示时间，单位是毫秒。也就是说，如果在69秒里该哨兵节点没有收到master服务器的正确响应，就会认为该服务器已经下线失效。

**2）设置故障恢复的时效**

`sentinel failover-timeout master 180000`

该时效参数的单位是毫秒，这里的含义是，在进行故障恢复时，如果在180秒里还没有完成主从服务器的切换动作，就会认为本次恢复动作失败。



## SpringBoot整合redis

### lettuce

在SpringBoot2.x 之后，原来使用的jedis 被替换为了 lettuce

- jedis : 采用的直连，多个线程操作的话，是不安全的，如果想要避免不安全的，要使用 jedis pool 连接池！ 更像BIO模式
- lettuce : 采用netty，实例可以再多个线程中进行共享，不存在线程不安全的情况！可以减少线程数据 了，更像NIO模式

### RedisTemplete

> SpringBoot所有的配置类，都有一个自动配置类 RedisAutoConfiguration
> 自动配置类都会绑定一个properties配置文件  RedisPropertied
>
> @ConditionalOnMissingBean：它是==修饰bean==的一个注解，作用是判断当前需要注入Spring容器中的bean的实现类是否已经含有，有的话不注入，没有就注入。因此，我们可以自定义一个redisTemplate来替换掉默认的

源码：

``` java
@Bean
@ConditionalOnMissingBean(name = "redisTemplate") // 我们可以自己定义一个redisTemplate来替换这个默认的！
    public RedisTemplate<Object, Object>redisTemplate(RedisConnectionFactory redisConnectionFactory)throws UnknownHostException {
        // 默认的 RedisTemplate 没有过多的设置，redis 对象都是需要序列化！
        // 两个泛型都是 Object, Object 的类型，我们后使用需要强制转换 <String, Object>
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
}
@Bean
@ConditionalOnMissingBean // 由于 String 是redis中最常使用的类型，所以说单独提出来了一个bean！
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory)throws UnknownHostException {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
```

自定义redisTemplate

``` java
@Configuration
public class RedisConfig {
    //自定义RedisTemplate
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        // 我们为了自己开发方便，一般直接使用 <String,Object>
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);
        // Json序列化配置
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new
                Jackson2JsonRedisSerializer<>(Object.class);
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
        // String 的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        // key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        // hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        // value序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // hash的value序列化方式采用jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
    }
}
```

### RedisUtils

``` java
@Component
public final class RedisUtil {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    // =============================common============================

    /**
     * 指定缓存失效时间
     *
     * @param key  键
     * @param time 时间(秒)
     */
    public boolean expire(String key, long time) {
        try {
            if (time > 0) {
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据key 获取过期时间
     *
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    public long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }


    /**
     * 判断key是否存在
     *
     * @param key 键
     * @return true 存在 false不存在
     */
    public boolean hasKey(String key) {
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除缓存
     *
     * @param key 可以传一个值 或多个
     */
    @SuppressWarnings("unchecked")
    public void del(String... key) {
        if (key != null && key.length > 0) {
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {
                redisTemplate.delete((Collection<String>) CollectionUtils.arrayToList(key));
            }
        }
    }


    // ============================String=============================

    /**
     * 普通缓存获取
     *
     * @param key 键
     * @return 值
     */
    public Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     *
     * @param key   键
     * @param value 值
     * @return true成功 false失败
     */

    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 普通缓存放入并设置时间
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */

    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 递增
     *
     * @param key   键
     * @param delta 要增加几(大于0)
     */
    public long incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }


    /**
     * 递减
     *
     * @param key   键
     * @param delta 要减少几(小于0)
     */
    public long decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }


    // ================================Map=================================

    /**
     * HashGet
     *
     * @param key  键 不能为null
     * @param item 项 不能为null
     */
    public Object hget(String key, String item) {
        return redisTemplate.opsForHash().get(key, item);
    }

    /**
     * 获取hashKey对应的所有键值
     *
     * @param key 键
     * @return 对应的多个键值
     */
    public Map<Object, Object> hmget(String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * HashSet
     *
     * @param key 键
     * @param map 对应多个键值
     */
    public boolean hmset(String key, Map<String, Object> map) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * HashSet 并设置时间
     *
     * @param key  键
     * @param map  对应多个键值
     * @param time 时间(秒)
     * @return true成功 false失败
     */
    public boolean hmset(String key, Map<String, Object> map, long time) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @param time  时间(秒) 注意:如果已存在的hash表有时间,这里将会替换原有的时间
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value, long time) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除hash表中的值
     *
     * @param key  键 不能为null
     * @param item 项 可以使多个 不能为null
     */
    public void hdel(String key, Object... item) {
        redisTemplate.opsForHash().delete(key, item);
    }


    /**
     * 判断hash表中是否有该项的值
     *
     * @param key  键 不能为null
     * @param item 项 不能为null
     * @return true 存在 false不存在
     */
    public boolean hHasKey(String key, String item) {
        return redisTemplate.opsForHash().hasKey(key, item);
    }


    /**
     * hash递增 如果不存在,就会创建一个 并把新增后的值返回
     *
     * @param key  键
     * @param item 项
     * @param by   要增加几(大于0)
     */
    public double hincr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, by);
    }


    /**
     * hash递减
     *
     * @param key  键
     * @param item 项
     * @param by   要减少记(小于0)
     */
    public double hdecr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, -by);
    }


    // ============================set=============================

    /**
     * 根据key获取Set中的所有值
     *
     * @param key 键
     */
    public Set<Object> sGet(String key) {
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 根据value从一个set中查询,是否存在
     *
     * @param key   键
     * @param value 值
     * @return true 存在 false不存在
     */
    public boolean sHasKey(String key, Object value) {
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将数据放入set缓存
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSet(String key, Object... values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 将set数据放入缓存
     *
     * @param key    键
     * @param time   时间(秒)
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSetAndTime(String key, long time, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().add(key, values);
            if (time > 0)
                expire(key, time);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 获取set缓存的长度
     *
     * @param key 键
     */
    public long sGetSetSize(String key) {
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 移除值为value的
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 移除的个数
     */

    public long setRemove(String key, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().remove(key, values);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // ===============================list=================================

    /**
     * 获取list缓存的内容
     *
     * @param key   键
     * @param start 开始
     * @param end   结束 0 到 -1代表所有值
     */
    public List<Object> lGet(String key, long start, long end) {
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 获取list缓存的长度
     *
     * @param key 键
     */
    public long lGetListSize(String key) {
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 通过索引 获取list中的值
     *
     * @param key   键
     * @param index 索引 index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
     */
    public Object lGetIndex(String key, long index) {
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     */
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     */
    public boolean lSet(String key, Object value, long time) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0)
                expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     * @return
     */
    public boolean lSet(String key, List<Object> value, long time) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0)
                expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 根据索引修改list中的某条数据
     *
     * @param key   键
     * @param index 索引
     * @param value 值
     * @return
     */

    public boolean lUpdateIndex(String key, long index, Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 移除N个值为value
     *
     * @param key   键
     * @param count 移除多少个
     * @param value 值
     * @return 移除的个数
     */

    public long lRemove(String key, long count, Object value) {
        try {
            Long remove = redisTemplate.opsForList().remove(key, count, value);
            return remove;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }

    }
}
```

### 搭建公网redis集群

1. 从节点redis.conf里的`slaveof` 后面要写公网ip和端口
2. 伪集群的话，要更改redis.conf里的端口，并从docker暴露，如`-p 6380:6380`，`-p 6381:6381`
3. redis-server默认端口是6379，其他端口需要-p来指定
4. `Protected-mode`需要关闭
5. sentinel.conf里的`sentinel monitor` 后也要写公网ip和端口
6. 主机从机的密码要设置成一样的，并且主机也要添加`masterauth` 密码，不然主机宕机无法自动切换成从节点

### applicaton.yaml

``` yaml
spring:
    redis:
      host: 123.57.93.167
      port: 6379
      password: 169736
      sentinel:
        master: master
        nodes:
          - 123.57.93.167:16379
          - 123.57.93.167:16380
              
#即使sentinel.conf里sentinel auth-pass设置里redis主节点密码，springboot配置文件里也要加上password,否则连接失败
#nodes数组里是哨兵的ip及端口，而不是redis的
#redis的host和port可加可不加，因为Redis 连接自动配置的优先顺序是：Redis Sentinel（哨兵） > Redis Cluster（集群） > Standalone（单机），哨兵模式优先级是最高的
```

### 测试

``` java
@SpringBootTest
class RedisDemoApplicationTests {

    @Resource
    private RedisTemplate redisTemplate;    //会注入自定义的RedisTemplate

    @Resource
    private RedisUtil redisUtil;

    @Test
    void contextLoads() {
        // RedisTemplate 操作不同的数据类型，api和我们的指令是一样的
        // opsForValue: 操作字符串，类似String
        // opsForList: 操作List，类似list
        // opsForSet  opsForHash  opsForZSet

        // 除了基本的操作，我们常用的方法都可以直接通过RedisTemplate来操作，比如事务，和基本的CRUD

        // RedisConnection 获取redis连接对象(很少去用)
        /*
        RedisConnection redisConnection = redisTemplate.getConnectionFactory().getConnection();
        redisConnection.flushDb();
        redisConnection.flushAll();
        */

        redisTemplate.opsForValue().set("mykey","ming");
        System.out.println(redisTemplate.opsForValue().get("mykey"));
    }

    @Test
    public void test() throws JsonProcessingException {
        User user = new User("草莓",3);
        //真实的开发一般都使用json来传递对象
        //String jsonUser = new ObjectMapper().writeValueAsString(user);
        redisTemplate.opsForValue().set("user",user);
        System.out.println(redisTemplate.opsForValue().get("user"));

    }

    @Test
    public void testUtil(){
        redisUtil.set("key3","ming");
        System.out.println(redisUtil.get("key3"));
    }

}
```

