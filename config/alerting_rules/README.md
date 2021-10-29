
CPU使用率
```
100 - (avg by (instance) (irate(node_cpu{mode="idle", instance=~"^%s:.+"}[5m])) * 100)
```

メモリ使用率
```
100 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100)
```

ディスク使用率
```
100 - (node_filesystem_avail_bytes / node_filesystem_size_bytes * 100)
```
