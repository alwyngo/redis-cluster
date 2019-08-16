# redis-cluster

redis 集群模式搭建

### 配置RedissonConfig
```java
@Configuration
public class RedissonConfig {

    @Bean
    RedissonClient redissonClient() {
        Config config = new Config();
        config.useSingleServer().setAddress("redis://127.0.0.1:6379").setDatabase(0);
        RedissonClient redissonClient = Redisson.create(config);
        return redissonClient;
    }

    @Bean("redissonClusterClient")
    RedissonClient redissonClusterClient() {
        Config config = new Config();
        config.useClusterServers().addNodeAddress("redis://127.0.0.1:7000", "redis://127.0.0.1:7001", "redis://127.0.0.1:7002", "redis://127.0.0.1:7003", "redis://127.0.0.1:7004", "redis://127.0.0.1:7005");
        RedissonClient client = Redisson.create(config);

        return client;
    }
}
```

### Controller调用测试
```java
@RequestMapping("getKey/{key}")
public RList<Object> getKey(@PathVariable("key") String key) {
    RList<Object> list = redissonClusterClient.getList(key);
    // 获取集群节点
    Collection<ClusterNode> nodes = redissonClusterClient.getClusterNodesGroup().getNodes();
    return list;
}
```
