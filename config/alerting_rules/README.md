
CPU使用率
```
100 - (irate(node_cpu_seconds_total{mode="idle"}[5m]) * 100) 
```

メモリ使用率
```
100 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100)
```

ディスク使用率
```
100 - (node_filesystem_avail_bytes / node_filesystem_size_bytes * 100)
```
