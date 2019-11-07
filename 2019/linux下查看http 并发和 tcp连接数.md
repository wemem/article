### linux查看httpd进程数

```bash
ps -ef | grep httpd | wc -l
```

### 查看Apache的并发请求数及其TCP连接状态
```bash
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
```
（这条语句是从新浪互动社区事业部技术总监王老大那儿获得的，非常不错）

返回结果示例：

LAST_ACK 5
SYN_RECV 30
ESTABLISHED 1597
FIN_WAIT1 51
FIN_WAIT2 504
TIME_WAIT 1057

其中的SYN_RECV表示正在等待处理的请求数；ESTABLISHED表示正常数据传输状态；TIME_WAIT表示处理完毕，等待超时结束的请求数。





### linux并发连接数查看


#### 1、查看Web服务器（Nginx Apache）的并发请求数及其TCP连接状态：

```bash
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
```

```bash
netstat -n|grep  ^tcp|awk '{print $NF}'|sort -nr|uniq -c
```
或者：

```bash
netstat -n | awk '/^tcp/ {++state[$NF]} END {for(key in state) print key,"t",state[key]}'
```

返回结果一般如下：

``` 
LAST_ACK 5 （正在等待处理的请求数）
SYN_RECV 30
ESTABLISHED 1597 （正常数据传输状态）
FIN_WAIT1 51
FIN_WAIT2 504
TIME_WAIT 1057 （处理完毕，等待超时结束的请求数）
```

其他参数说明：

``` 
CLOSED：无连接是活动的或正在进行
LISTEN：服务器在等待进入呼叫
SYN_RECV：一个连接请求已经到达，等待确认
SYN_SENT：应用已经开始，打开一个连接
ESTABLISHED：正常数据传输状态
FIN_WAIT1：应用说它已经完成
FIN_WAIT2：另一边已同意释放
ITMED_WAIT：等待所有分组死掉
CLOSING：两边同时尝试关闭
TIME_WAIT：另一边已初始化一个释放
LAST_ACK：等待所有分组死掉
``` 
#### 2、查看Nginx运行进程数

```bash
ps -ef | grep nginx | wc -l
```
返回的数字就是nginx的运行进程数，如果是apache则执行

```bash
ps -ef | grep httpd | wc -l
``` 
#### 3、查看Web服务器进程连接数：

```bash
netstat -antp | grep 80 | grep ESTABLISHED -c
``` 
#### 4、查看MySQL进程连接数：
```bash
ps -axef | grep mysqld -c
```