## cpu飙高的记录

### 查看端口上的进程 pid 
```
netstat -tulnp | grep :8080
lsof -i :8080
```

### 使用 top -H 命令查看线程信息 
```
top -H -p <PID>
```

### dump 堆栈信息
```
jstack -l <PID> > thread_dump.txt
```

### 将线程 tid 转为十六进制
```
printf "%x\n" <TID>
```

### 获取线程堆栈信息
```
jstack <PID> | grep -A 30 <hex-TID>
```