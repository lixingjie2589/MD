# CMD

Window 中杀死指定端口 cmd 命令行 taskkill

```bash
1.查看指定端口的占用情况
netstat -aon|findstr "8080"
2 .直接强制杀死指定端口
taskkill /pid 4136 -t -f

查看PID对应的进程
tasklist|findstr "127704"

taskkill /f /t /im java.exe
```

激活Windows

```cmd
# 查看过期时间
slmgr.vbs -xpr

1
slmgr /ipk 激活码
2
slmgr /skms kms.xspace.in
3
slmgr /ato
```

