# httpsqs
张宴httpsqs说明
[文章作者：张宴 本文版本：v1.7.1 最后修改：2011.11.04 转载请注明原文链接：http://blog.zyan.cc/httpsqs/]

　　HTTPSQS（HTTP Simple Queue Service）是一款基于 HTTP GET/POST 协议的轻量级开源简单消息队列服务，使用 Tokyo Cabinet 的 B+Tree Key/Value 数据库来做数据的持久化存储。

　　项目网址：http://code.google.com/p/httpsqs/
　　使用文档：http://blog.zyan.cc/httpsqs/
　　使用环境：Linux（同时支持32位、64位操作系统，推荐使用64位操作系统）
　　软件作者：张宴

　　队列（Queue）又称先进先出表（First In First Out），即先进入队列的元素，先从队列中取出。加入元素的一头叫“队头”，取出元素的一头叫“队尾”。利用消息队列可以很好地异步处理数据传送和存储，当你频繁地向数据库中插入数据、频繁地向搜索引擎提交数据，就可采取消息队列来异步插入。另外，还可以将较慢的处理逻辑、有并发数量限制的处理逻辑，通过消息队列放在后台处理，例如FLV视频转换、发送手机短信、发送电子邮件等。

　　HTTPSQS 具有以下特征：

　　● 非常简单，基于 HTTP GET/POST 协议。PHP、Java、Perl、Shell、Python、Ruby等支持HTTP协议的编程语言均可调用。
　　● 非常快速，入队列、出队列速度超过10000次/秒。
　　● 高并发，支持上万的并发连接，C10K不成问题。
　　● 支持多队列。
　　● 单个队列支持的最大队列数量高达10亿条。
　　● 低内存消耗，海量数据存储，存储几十GB的数据只需不到100MB的物理内存缓冲区。
　　● 可以在不停止服务的情况下便捷地修改单个队列的最大队列数量。
　　● 可以实时查看队列状态（入队列位置、出队列位置、未读队列数量、最大队列数量）。
　　● 可以查看指定队列ID（队列点）的内容，包括未出、已出的队列内容。
　　● 查看队列内容时，支持多字符集编码。
　　● 源代码不超过800行，适合二次开发。

1、HTTPSQS 1.7 压力测试：

　　采用Apache ab命令进行压力测试，开启10个线程，放入10万条文本数据（每条512字节）到队列中:
　　使用HTTP Keep-Alive时：23018 requests/sec
　　关闭HTTP Keep-Alive时：11840 requests/sec

　　采用Apache ab命令进行压力测试，开启10个线程，从队列中取出10万条文本数据（每条512字节）:
　　使用HTTP Keep-Alive时：25982 requests/sec
　　关闭HTTP Keep-Alive时：13294 requests/sec

　　详细测试内容：http://code.google.com/p/httpsqs/wiki/BenchmarkTest

　　生产环境应用：在金山游戏官网中，新闻、论坛帖子、客服公告、SNS社区等发生的增、删、改操作，文本内容实时写入HTTPSQS队列，全站搜索引擎增量索引准实时（1分钟内）更新的数据源取自HTTPSQS。HTTPSQS 2009年12月18日上线至今，运行稳定，既有来自Web服务器的入队列操作，也有来自命令行脚本的批量入、出队列操作。

2、HTTPSQS 的生产环境应用：

　　●金山通行证（https://my.xoyo.com）
　　队列应用类型：手机短信上行、手机短信下发、邮件下发
　　队列应用要求：稳定性高，存储数据量大
　　队列部署结构：一主、一备两台 HTTPSQS 热备模式

　　●金山用户行为分析系统（http://kbi.xoyo.com）
　　队列应用类型：用户鼠标点击、访问URL原始数据采集
　　队列应用要求：并发性能高，存储数据量大
　　队列部署结构：多台 HTTPSQS 应用层哈希分布式模式

　　●金山网络游戏运营平台 KingEyes
　　队列应用类型：用户操作日志记录

　　●金山逍遥网站内搜索
　　队列应用类型：索引准实时更新。在金山游戏官网中，新闻、论坛帖子、客服公告、SNS社区等发生的增、删、改操作，文本内容实时写入HTTPSQS队列，全站搜索引擎增量索引准实时（1分钟内）更新的数据源取自HTTPSQS。

　　●金山逍遥网全站通用评论系统
　　队列应用类型：评论发表

　　●金山《剑侠情缘》电视连续剧四大角色人物选秀活动（http://zt.xoyo.com/haixuan/）
　　队列应用类型：用户上传的照片异步裁剪、缩放处理

　　●新浪邮箱（http://mail.sina.com.cn）
　　队列应用类型：用户登陆日志记录

3、HTTPSQS 编译安装：

ulimit -SHn 65535

wget http://httpsqs.googlecode.com/files/libevent-2.0.12-stable.tar.gz
tar zxvf libevent-2.0.12-stable.tar.gz
cd libevent-2.0.12-stable/
./configure --prefix=/usr/local/libevent-2.0.12-stable/
make
make install
cd ../

wget http://httpsqs.googlecode.com/files/tokyocabinet-1.4.47.tar.gz
tar zxvf tokyocabinet-1.4.47.tar.gz
cd tokyocabinet-1.4.47/
./configure --prefix=/usr/local/tokyocabinet-1.4.47/
#注：在32位Linux操作系统上编译Tokyo cabinet，请使用./configure --enable-off64代替./configure，可以使数据库文件突破2GB的限制。
#./configure --enable-off64 --prefix=/usr/local/tokyocabinet-1.4.47/
make
make install
cd ../

wget http://httpsqs.googlecode.com/files/httpsqs-1.7.tar.gz
tar zxvf httpsqs-1.7.tar.gz
cd httpsqs-1.7/
make
make install
cd ../

4、HTTPSQS 服务器使用文档：
[root@xoyo ~]# httpsqs -h
-l <ip_addr> 监听的IP地址，默认值为 0.0.0.0 
-p <num> 监听的TCP端口（默认值：1218）
-x <path> 数据库目录，目录不存在会自动创建（例如：/opt/httpsqs/data）
-t <second> HTTP请求的超时时间（默认值：3）
-s <second> 同步内存缓冲区内容到磁盘的间隔秒数（默认值：5）
-c <num> 内存中缓存的最大非叶子节点数（默认值：1024）
-m <size> 数据库内存缓存大小，单位：MB（默认值：100）
-i <file> 保存进程PID到文件中（默认值：/tmp/httpsqs.pid）
-a <auth> 访问HTTPSQS的验证密码（例如：mypass123）
-d 以守护进程运行
-h 显示这个帮助

示例：
ulimit -SHn 65535 
httpsqs -d -p 1218 -x /data0/queue


　　请使用命令“killall httpsqs”、“pkill httpsqs”和“kill `cat /tmp/httpsqs.pid`”来停止httpsqs。

　　注意：请不要使用命令“pkill -9 httpsqs”和“kill -9  httpsqs的进程ID”来结束httpsqs，否则，内存中尚未保存到磁盘的数据将会丢失。


5、HTTPSQS 客户端使用文档：

　　(1)、入队列（将文本消息放入队列）：

　　HTTP GET 协议（以curl命令为例）：
curl "http://host:port/?name=your_queue_name&opt=put&data=经过URL编码的文本消息&auth=mypass123"


　　HTTP POST 协议（以curl命令为例）：
curl -d "经过URL编码的文本消息" "http://host:port/?name=your_queue_name&opt=put&auth=mypass123"

如果入队列成功，返回：
HTTPSQS_PUT_OK


　　如果入队列失败，返回：
HTTPSQS_PUT_ERROR


　　如果队列已满，返回：
HTTPSQS_PUT_END


　　从HTTPSQS 1.2版本开始，在返回给客户端的HTTP Header头中增加了一行“Pos: xxx”，输出当前队列的读取位置点，例如：
HTTP/1.1 200 OK
Content-Type: text/plain
Keep-Alive: 120
Pos: 19
Date: Thu, 18 Mar 2010 04:57:08 GMT
Content-Length: 14

HTTPSQS_PUT_OK



　　(2)、出队列（从队列中取出文本消息）：

　　HTTP GET 协议（以curl命令为例）：
curl "http://host:port/?charset=utf-8&name=your_queue_name&opt=get&auth=mypass123"


curl "http://host:port/?charset=gb2312&name=your_queue_name&opt=get&auth=mypass123"
返回消息队列的内容给客户端。

　　如果没有未取出的消息队列，则返回：
HTTPSQS_GET_END


　　从HTTPSQS 1.2版本开始，在返回给客户端的HTTP Header头中增加了一行“Pos: xxx”，输出当前队列的读取位置点，例如：
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8
Keep-Alive: 120
Pos: 7
Date: Thu, 18 Mar 2010 04:56:01 GMT
Content-Length: 18

消息队列内容


　　参数charset说明（例如：/?charset=utf-8）：
　　指定HTTP输出Header头的字符编码，即：
　　Content-Type: text/plain; charset=utf-8 

　　任何在IANA注册的字符编码均可使用，但是，并不是所有的浏览器都能解析全部的字符编码。对于中文，常用的字符编码有：utf-8、gb2312、gbk、gb18030、big5等。


　　(3)、查看队列状态（普通方式，便于浏览器查看）：

　　HTTP GET 协议（以curl命令为例）：

curl "http://host:port/?name=your_queue_name&opt=status&auth=mypass123"


　　返回（示例）：

HTTP Simple Queue Service v1.7
------------------------------
Queue Name: xoyo
Maximum number of queues: 1000000
Put position of queue (1st lap): 45
Get position of queue (1st lap): 6
Number of unread queue: 39


　　如果“队列写入点值”大于“最大队列数量值”，将重置“队列写入点”为1，即又从1开始存储新的队列内容，覆盖原来队列位置点的内容：

HTTP Simple Queue Service v1.7
------------------------------
Queue Name: xoyo
Maximum number of queues: 1000000
Put position of queue (2st lap): 4562
Get position of queue (1st lap): 900045
Number of unread queue: 104517
(4)、查看队列状态（JSON方式，便于程序处理返回内容）：

　　从HTTPSQS 1.3版本开始支持此功能。

　　HTTP GET 协议（以curl命令为例）：

curl "http://host:port/?name=your_queue_name&opt=status_json&auth=mypass123"


　　返回（示例）：

{"name":"xoyo","maxqueue":1000000,"putpos":45,"putlap":1,"getpos":6,"getlap":1,"unread":39}


　　如果“队列写入点值”大于“最大队列数量值”，将重置“队列写入点”为1，即又从1开始存储新的队列内容，覆盖原来队列位置点的内容：

{"name":"xoyo","maxqueue":1000000,"putpos":4562,"putlap":2,"getpos":900045,"getlap":1,"unread":104517}



　　(5)、查看指定队列位置点的内容：

　　跟一般的队列系统不同的是，HTTPSQS 可以查看指定队列ID（队列点）的内容，包括未出、已出的队列内容。可以方便地观测进入队列的内容是否正确。

　　另外，假设有一个发送手机短信的队列，由客户端守护进程从队列中取出信息，并调用“短信网关接口”发送短信。但是，如果某段时间“短信网关接口”有故障，而这段时间队列位置点300~900的信息已经出队列，但是发送短信失败，我们还可以在位置点300~900被覆盖前，查看到这些位置点的内容，作相应的处理。

　　HTTP GET 协议（以curl命令为例）：

curl "http://host:port/?charset=utf-8&name=your_queue_name&opt=view&pos=5&auth=mypass123"


curl "http://host:port/?charset=gb2312&name=your_queue_name&opt=view&pos=19&auth=mypass123"


　　pos >=1 并且 <= 1000000000

　　返回指定队列位置点的内容。


　　(6)、重置指定队列：

　　HTTP GET 协议（以curl命令为例）：

curl "http://host:port/?name=your_queue_name&opt=reset&auth=mypass123"


　　如果重置成功，返回：

HTTPSQS_RESET_OK


　　如果重置失败，返回：

HTTPSQS_RESET_ERROR



　　(7)、更改指定队列的最大队列数量：

　　默认的最大队列长度（100万条）：1000000

　　HTTP GET 协议（以curl命令为例）：

curl "http://host:port/?name=your_queue_name&opt=maxqueue&num=1000000000&auth=mypass123"


　　num >=10 并且 <= 1000000000

　　如果更改最大队列数量成功，则返回：

HTTPSQS_MAXQUEUE_OK


　　更改的最大队列数量必须大于当前的“队列写入点”。另外，当“队列写入点”小于“队列读取点”时（即PUT位于圆环的第二圈，而GET位于圆环的第一圈时），本操作将被取消，然后返回给客户端以下信息：

HTTPSQS_MAXQUEUE_CANCEL



　　(8)、不停止服务的情况下，修改定时刷新内存缓冲区内容到磁盘的间隔时间：

　　从HTTPSQS 1.3版本开始支持此功能。

　　默认间隔时间：5秒 或 httpsqs -s <second> 参数设置的值。

　　HTTP GET 协议（以curl命令为例）：

curl "http://host:port/?name=your_queue_name&opt=synctime&num=10&auth=mypass123"


　　num >=1 and <= 1000000000

　　如果修改间隔时间成功，则返回：

HTTPSQS_SYNCTIME_OK


　　如果 num 不在 1 ～ 1000000000 之间，本操作将被取消，然后返回给客户端以下信息：

HTTPSQS_SYNCTIME_CANCEL



　　(9)、密码校验失败：

　　从HTTPSQS 1.5版本开始支持此功能。

　　如果密码校验失败（/?auth=xxx），将返回以下信息：
HTTPSQS_AUTH_FAILED



　　(10)、全局错误：

　　如果发生全局错误（即指令、参数错误等），将返回以下信息：
HTTPSQS_ERROR



　　6、HTTPSQS 客户端

　　(1)、PHP 客户端说明文档：

　　A、PHP 客户端扩展（第三方提供，详情请访问：http://code.google.com/p/php-httpsqs-client/）

　　B、PHP 客户端 Class 文件（官方提供：适用于 HTTPSQS 1.7 以上版本，推荐使用。）

　　查看 PHP Class 源代码：httpsqs_client.php

　　PHP Client 所有函数使用示例：test_example.php

　　PHP Client 命令行运行示例：test_commandline.php
