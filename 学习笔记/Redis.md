## Redis数据库

### 1 redis的安装与介绍

1.1 创建批处理文件命名为start.bat,用文本编辑器编辑 redis-server redis.windows.donf,然后保存退出

1.2 双击批处理文件,则redis安装成功

1.3 安装RedisDesktopManager图形管理工具,连接redis服务器,配置端口6379,默认无需密码,自带16个逻辑库,如需增加,在配置文件里面修改即可

------

### 2 redis配置参数

2.1 修改配置文件

```yaml
#端口6379
port 6379

# 允许任意IP访问
bind 0.0.0.0

# 300s无连接关闭
timeout 300

# loglevel notice无需修改

# 将日志输出到日志文件
logfile "logs.log"

# 日志不要在控制台打印,输出到日志文件
syslog-enabled no
#------------------------------------
databases =逻辑库数量,默认为16
save =RDB文件同步的频率
rdbcompression =同步RDB文件是否采用压缩,默认为yes
dbfilename =镜像文件名称,默认为dump.rdb
dir =rdb文件的目录,默认redis目录
requirepass =访问密码,默认无需密码

maxclients =最大连接数,默认无限制
maxmemory =占用内存的最大值,默认无限制
appendonly =开启AOF备份
appendfsync =AOF同步的频率,分为 no|everysec|always
```

------

### 3 redis的数据操作

type key  查看可以的类型

3.1 string

​	增加 set      mset      setex key 时间 value

​	读取  get     mget

​	追加  append  key value

3.2 hash

​	单个属性 hset person name "张三"

​	多个属性 hmset perosn age 18  gender true

​	查看  (hvals  hkeys  hget   hmget   hdel) person /属性

3.3 list

​	左推lpush key value  lrange key 0 -1

​	右推rpush key value  lrange key 0 -1

​	 取出就删除 lpop key左删除 rpop key右删除

​	插入   linsert key before|after value newvalue 

​	根据索引修改值    lset key index 新值(index下标从0开始)

​	删除   lrem key count( "值")  + value 

​		 	count>0 从头删  count<0从尾删 count=0符合条件都删除	  

3.4 set

​	特点 无序,string,不重复,没有修改

​	查看  smembers key

​	增加 sadd 集合名 + value值

​	判断元素是否在集合中  sismember  集合名+元素值

3.5 zset

​	特点 有序集合,有顺序权重

​	增加 zadd key 权重 value   

​	查看 	zrange key 0 -1 查看所有元素

​			zrangebyscore key 权重1() 权重2()

​			zscore key value 根据内容获取权重(获取一个)

​	删除指定元素 zrem key value

​				zremrangebyscore key 权重范围

​	如果不存在则添加

​		zincrby key 权重  value

3.6 键key的命令

​	查看 keys on*  以on开头的key

​		keys * 查看所有的key

​	判断某个key是否存在   exists key

​	删除  del key

​	给key设置过期时间   exprie key 时间

3.7 清空数据库

​	flushdb  清除当前数据库的所有key

​	flushall    清除所有数据库的key

------

### 4 python与redis的交互

4.1 安装 pip install redis

```yaml
import redis

#1 链接数据库 key--value
# StrictRedis基类   多用Redis子类
client = redis.Redis(
	host = '127.0.0.1',port = 6379
)
#2 设置key
key = 'pyone'

#3  string 增
result = client.set(key,'one')
#4 删
result =client.delete(key)
#5 改
relust = client.set(key,'two')
#6 查
result = client.get(key)
```

------

### 5 主从服务器



