# linux 高性能服务器编程

[TOC]

## 第一篇 TCP/IP协议详解

### 第1章TCP/IP协议族 / 2

#### 1.1 TCP/IP 协议族体系结构以及主要协议 / 2
##### 1.1.1 数据链路层 / 2
##### 1.1.2 网络层 / 3
##### 1.1.3 传输层 / 4
##### 1.1.4 应用层 / 5

#### 1.2 封装 / 6

#### 1.3 分用 / 7

#### 1.4 测试网络 / 7

#### 1.5 ARP 协议工作原理 / 9
##### 1.5.1 以太网ARP请求/应答报文详解 / 9
##### 1.5.2 ARP 高速缓存的查看和修改 / 10
##### 1.5.3 使用 tcpdump 观察 ARP 通信过程 / 10

#### 1.6 DNS 工作原理 / 12
##### 1.6.1 DNS 查询和应答报文详解 / 12
##### 1.6.2 Linux下访问DNS服务 / 14
##### 1.6.3 使用tcpdump观察DNS通信过程 / 15

#### 1.7 socket和TCP/IP协议族的关系 / 16

### 第2章 IP协议详解 / 17

#### 2.1 IP服务的特点 / 17

#### 2.2 IPv4头部结构 / 18
##### 2.2.1 IPv4头部结构 / 18
##### 2.2.2 使用tcpdump观察IPv4头部结构 / 20

#### 2.3 IP分片 / 21

#### 2.4 IP路由 / 22
##### 2.4.1 IP模块工作流程 / 23
##### 2.4.2 路由机制 / 24
##### 2.4.3 路由表更新 / 25

#### 2.5 IP转发 / 25

#### 2.6 重定向 / 26
##### 2.6.1 ICMP重定向报文 / 26
##### 2.6.2 主机重定向实例 / 27

#### 2.7 IPv6头部结构 / 27
##### 2.7.1 IPv6固定头部结构 / 28
##### 2.7.2 IPv6扩展头部 / 29

### 第3章 TCP 协议详解 / 30
#### 3.1 TCP服务的特点 / 30

#### 3.2 TCP 头部结构 / 32
##### 3.2.1 TCP 固定头部结构 / 32
##### 3.2.2 TCP 头部选项 / 33
##### 3.2.3 使用 tcpdump观察TCP头部信息 / 35

#### 3.3 TCP 连接的建立和关闭 / 37
##### 3.3.1 使用tcpdump观察TCP连接的建立和关闭 / 37
##### 3.3.2 半关闭状态 / 39
##### 3.3.3 连接超时 / 39

#### 3.4 TCP状态转移 / 40
##### 3.4.1 TCP 状态转移总图 / 41
##### 3.4.2 TIME_WAIT 状态 / 43

#### 3.5 复位报文段 / 44
##### 3.5.1 访问不存在的端口 / 44
##### 3.5.2 异常终止连接 / 45
##### 3.5.3 处理半打开连接 / 45

#### 3.6 TCP交互数据流 / 46

#### 3.7 TCP成块数据流 / 48

#### 3.8 带外数据 / 50

#### 3.9 TCP超时重传 / 51

#### 3.10 拥塞控制 / 53
##### 3.2.1 拥塞控制概述 / 53
##### 3.2.1 慢启动和拥塞避免 / 54
##### 3.2.1 快速重传和快速恢复 / 55

### 第4章 TCP/IP通信案例: 访问Internet上的Web服务器 / 57

## 第二篇 深入解析高性能服务器编程

### 第5章 Linux网络编程基础 API / 70
#### 5.1 socket 地址API / 70
#### 5.1.1 主机字节序和网字节序 / 70
#### 5.1.2 通用socket 地址 / 71
#### 5.1.3 专用 socket 地址 / 72
#### 5.1.4 IP地址转换函数 / 73

### 5.2 创建 socket / 74
### 5.3 命名 socket / 75
### 5.4 监听 socket / 76 
### 5.5 接受连接 / 78
### 5.6 发起连接 / 80
### 5.7 关闭连接 / 80
### 5.8 数据读写 / 81
#### 5.8.1 TCP数据读写 / 81
#### 5.8.2 UDP数据读写 / 85
#### 5.8.3 通用数据读写 / 86
### 5.9 带外数据 / 87
### 5.10 地址信息函数 / 87
### 5.11 socket 选项 / 87
#### 5.11.1 SO_REUSEREADD 选项 / 89
#### 5.11.2 SO_RECVBUF和SO_SNDBUF选项 / 93
#### 5.11.3 SO_RCVLOWAT和SO_SNDLOWAT选项
#### 5.11.4 SO_LINGER 选项 / 93
### 5.12 网络信息 API / 94
#### 5.12.1 gethostbyname和gethostbyaddr / 94
#### 5.12.2 getservbyname getservbyport / 95
#### 5.12.3 getaddrinfo
#### 5.12.4 getnameinfo

## 第6章
### 6.1 pipe函数 / 100
### 6.2 dup函数和dup2函数 / 101
### 6.3 readv函数和writev函数
### 6.4 sendfile 函数 / 106
### 6.5 mmap函数和munmap函数 / 107
### 6.6 splice 函数 / 108
### 6.7 tee函数 / 110
### 6.8 fcntl函数 / 112
## 第7章
### 7.1 日志 / 114
#### 7.1.1 linux 系统日志 / 114
#### 7.1.1 syslog 函数 / 115
### 7.2 用户信息 / 116
#### 7.2.1 UID、EUID、GID和EGID / 116
#### 7.2.2 切换用户 / 117
### 7.3 进程间关系 / 118
#### 7.3.1 进程组 / 118
#### 7.3.2 会话 / 118
#### 7.3.3 用 ps 命令查看进程关系 / 119
### 7.4 系统资源限制 / 119
### 7.5 改变工作目录和跟目录 / 120
### 7.6 服务器程序后台优化 / 121

## 第8章 高性能服务器程序框架 / 123
### 8.1 服务器模型 / 123
#### 8.1.1 C/S模型 / 123


![image](https://note.youdao.com/yws/public/resource/240b8d139a5221848d617ba237d15d2f/xmlnote/WEBRESOURCE9222479e84e7f0053d54ba06ba06186b/2962)


#### 8.1.2 P2P模型 / 124

P2P(Peer to Peer,点对点)
P2P 模型通常有一个发现服务器，发现服务器提供查找服务(甚至还可以提供内容服务)，使每个客户端都能尽快的找到自己需要的资源。

![image](https://note.youdao.com/yws/public/resource/240b8d139a5221848d617ba237d15d2f/xmlnote/WEBRESOURCE7aee739a3c7b96883f74eaaf3090bccd/2968)

### 8.2 服务器编程框架 / 125


模块 | 单个服务器程序 | 服务器机制
---|---|---
I/O 处理单元 | 处理客户链接，读写网络数据 | 作为接入服务器，实现负载均衡
逻辑单元 | 业务进程或线程 | 逻辑服务器
网络储存单元 | 本地数据库，文件或缓存 | 数据库服务器
请求队列 | 各单元之间的通信方式 | 各服务器之间的永久 TCP 链接



### 8.3 I/O模型 / 126

 - 我们只有在事件已经发生的情况下操作非阻塞 `I/O`(读、写等)，才能提高程序的效率。因此，非阻塞`I/O`通常需要和其他 `I/O` 通知机制一起使用，比如`I/O`复用和 `SIGIO` 信号 

 - `I/O`复用函数本身是阻塞的，它们能提高程序效率的原因在于它们具有同时监听多个`I/O`事件的能力。
 - 同步`I/O`模型要求用户代码自执行`I/O`操作（将数据从内核缓冲区读入用户缓冲区。或将数据缓冲区和用户缓冲区写入内核缓冲区），而异步`I/O`机制则由内核来执行`I/O`操作，同步`I/O`向应用程序通知的是`I/O`就绪事件，而异步是向应用程序通知的是`I/O`完成事件。
 
***表8-2 I/O模型对比***
`I/O`模型 | 读写操作和阻塞阶段
---|---
阻塞 `I/O` | 程序阻塞于读写函数
`I/O` 复用 | 程序阻塞于 `I/O` 复用系统调用，但可同时监听多个 `I/O` 事件。对于`I/O`本身的读写操作是非阻塞的
`SIGIO` 信号 | 信号触发读写就绪事件，用户程序执行读写操作。程序没有阻塞阶段
异步 `I/O` | 内核执行读写操作并触发读写完成事件。程序没有阻塞阶段

### 8.4 两种高效的事件处理模型 / 127
#### 8.4.1 Reactor 模式 / 128 

`Reactor` 是这样一种模式，它要求主线程（`I/O`处理单元，下同）只负责监听文件描述符上是否有事件发生，有的话就立即将改时间通知工作线程（逻辑单元，下同）。除此之外，主线程不做任何其他
实质性的工作。读写数据，接受新的连接，以及处理客户请求均在工作线程中完成。

![image](https://note.youdao.com/yws/public/resource/240b8d139a5221848d617ba237d15d2f/xmlnote/634A91E7FF8F420AB89A1AE21BE7EA34/3018)

#### 8.4.2 Proactor 模式 / 128

`Proactor` 与 Reactor 模式不同，`Reactor` 模式将所有的 `I/O` 操作都交给主线程和内核来处理，工作线程仅仅负责业务逻辑。因此，`Proactor` 模式更符合图 8-4 所描述的服务器编程框架。

![image](https://note.youdao.com/yws/public/resource/240b8d139a5221848d617ba237d15d2f/xmlnote/F83E3C98D2E84B0F86164CB6D89C0C20/3041)


#### 8.4.3 模拟 Proactor 模式 / 128

![image](https://note.youdao.com/yws/public/resource/240b8d139a5221848d617ba237d15d2f/xmlnote/3066693874344EDEA73F4100D08D4173/3045)

### 8.5 两种高效的并发模式  /130

并发编程的目的是让程序“同时”执行多个任务。如果程序是计算密集型，并发编程并没有优势，反而任务切换使效降低。但如果程序是 `I/O` 密集型的，比如经常读写文件，访问数据库等，则情况就不一样了。由于`I/O`操作的速度远没有 CPU 的计算速度快，所以让程序阻塞的执行线程可以主动放弃 CPU (或由操作系统来调度)，并发执行权转移到其他线程。这样一来，CPU 就可以做更加有意义的事情（除非所有的线程都同时被 `I/O`操作所阻塞），而不是等待`I/O`操作完成，一次 CPU 的利用率显著上升。

并发模式是指 `I/O` 处理单元和多个逻辑单元之间完成任务的方法。服务器主要有两种并发编程模式：半同步 / 半异步（`half-sync`/ `half-async`） 模式和领导者 / 追随者模式（`Leader`/`Followers`） 模式。

#### 8.5.1 半同步/半异步模式 / 131

首先，半同步/半异步模式中的“同步”和“异步”与前面讨论的I/O模型中的“同步”和“异步”是完全不同的概念，在`I/O`模型中，“同步”和“异步”区分的是内核向应用程序通知的是何种`I/O`事件（是就绪事件还是完成事件），以及该由谁来完成`I/O`读写（是应用程序还是内核）。在并发模式中，“同步”指的是程序完全按照代码序列的顺序执行，“异步”指的是程序的执行需要由系统事件来驱动。常见的系统事件包括中断、信号等。

![image](https://note.youdao.com/yws/public/resource/240b8d139a5221848d617ba237d15d2f/xmlnote/27C21D84437E40C198932C7027BBC94F/3093)

对于像服务器这种即要求较好的实时性，又要求能同时处理多个客户请求的应用程序，我们就应该同时使用同步线程和异步线程来实现，即采用半同步/半异步模式来实现。

- 同步线程用于处理客户逻辑
- 异步线程用于处理`I/O`事件。

![image](https://note.youdao.com/yws/public/resource/240b8d139a5221848d617ba237d15d2f/xmlnote/BD225778948C4EA280DFF1DF3385D66C/3098)

![image](https://note.youdao.com/yws/public/resource/240b8d139a5221848d617ba237d15d2f/xmlnote/D0FC9F3BD93042C79C8708B1151C351B/3101)

#### 8.5.2 领导者/追随者模式 / 134

**领导者/追随者** 模式是多个工作线程轮流获得事件源集合,轮流监听、分发并处理事件的一种模式。在任意时间点,程序都仅有一个领导者线程,它负责监听`I/O`事件。而其他线程则都是追随者,它们休眠在线程池中等待成为新的领导者。当前的领导者如果检测到`I/O`事件,首先要从线程池中推选出新的领导者线程,然后处理`I/O`事件。此时,新的领导者等待新的`I/O`事件,而原来的领导者则处理`I/O`事件,二者实现了并发。

领导者/追随者模式包含如下几个组件:

- 句柄集(HandleSet)
- 线程集(ThreadSet)
- 事件处理器(EventHandler)
- 具体的事件处理器(ConcreteEventHandler)

它们的关系如图8-12所示[4]

### 8.6 有限状态机 / 136


```
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <assert.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <fcntl.h>

#define BUFFER_SIZE 4096
enum CHECK_STATE { CHECK_STATE_REQUESTLINE = 0, CHECK_STATE_HEADER, CHECK_STATE_CONTENT };
enum LINE_STATUS { LINE_OK = 0, LINE_BAD, LINE_OPEN };
enum HTTP_CODE { NO_REQUEST, GET_REQUEST, BAD_REQUEST, FORBIDDEN_REQUEST, INTERNAL_ERROR, CLOSED_CONNECTION };
static const char* szret[] = { "I get a correct result\n", "Something wrong\n" };

LINE_STATUS parse_line( char* buffer, int& checked_index, int& read_index )
{
    char temp;
    for ( ; checked_index < read_index; ++checked_index )
    {
        temp = buffer[ checked_index ];
        if ( temp == '\r' )
        {
            if ( ( checked_index + 1 ) == read_index )
            {
                return LINE_OPEN;
            }
            else if ( buffer[ checked_index + 1 ] == '\n' )
            {
                buffer[ checked_index++ ] = '\0';
                buffer[ checked_index++ ] = '\0';
                return LINE_OK;
            }
            return LINE_BAD;
        }
        else if( temp == '\n' )
        {
            if( ( checked_index > 1 ) &&  buffer[ checked_index - 1 ] == '\r' )
            {
                buffer[ checked_index-1 ] = '\0';
                buffer[ checked_index++ ] = '\0';
                return LINE_OK;
            }
            return LINE_BAD;
        }
    }
    return LINE_OPEN;
}

HTTP_CODE parse_requestline( char* szTemp, CHECK_STATE& checkstate )
{
    char* szURL = strpbrk( szTemp, " \t" );
    if ( ! szURL )
    {
        return BAD_REQUEST;
    }
    *szURL++ = '\0';

    char* szMethod = szTemp;
    if ( strcasecmp( szMethod, "GET" ) == 0 )
    {
        printf( "The request method is GET\n" );
    }
    else
    {
        return BAD_REQUEST;
    }

    szURL += strspn( szURL, " \t" );
    char* szVersion = strpbrk( szURL, " \t" );
    if ( ! szVersion )
    {
        return BAD_REQUEST;
    }
    *szVersion++ = '\0';
    szVersion += strspn( szVersion, " \t" );
    if ( strcasecmp( szVersion, "HTTP/1.1" ) != 0 )
    {
        return BAD_REQUEST;
    }

    if ( strncasecmp( szURL, "http://", 7 ) == 0 )
    {
        szURL += 7;
        szURL = strchr( szURL, '/' );
    }

    if ( ! szURL || szURL[ 0 ] != '/' )
    {
        return BAD_REQUEST;
    }

    //URLDecode( szURL );
    printf( "The request URL is: %s\n", szURL );
    checkstate = CHECK_STATE_HEADER;
    return NO_REQUEST;
}

HTTP_CODE parse_headers( char* szTemp )
{
    if ( szTemp[ 0 ] == '\0' )
    {
        return GET_REQUEST;
    }
    else if ( strncasecmp( szTemp, "Host:", 5 ) == 0 )
    {
        szTemp += 5;
        szTemp += strspn( szTemp, " \t" );
        printf( "the request host is: %s\n", szTemp );
    }
    else
    {
        printf( "I can not handle this header\n" );
    }

    return NO_REQUEST;
}

HTTP_CODE parse_content( char* buffer, int& checked_index, CHECK_STATE& checkstate, int& read_index, int& start_line )
{
    LINE_STATUS linestatus = LINE_OK;
    HTTP_CODE retcode = NO_REQUEST;
    while( ( linestatus = parse_line( buffer, checked_index, read_index ) ) == LINE_OK )
    {
        char* szTemp = buffer + start_line;
        start_line = checked_index;
        switch ( checkstate )
        {
            case CHECK_STATE_REQUESTLINE:
            {
                retcode = parse_requestline( szTemp, checkstate );
                if ( retcode == BAD_REQUEST )
                {
                    return BAD_REQUEST;
                }
                break;
            }
            case CHECK_STATE_HEADER:
            {
                retcode = parse_headers( szTemp );
                if ( retcode == BAD_REQUEST )
                {
                    return BAD_REQUEST;
                }
                else if ( retcode == GET_REQUEST )
                {
                    return GET_REQUEST;
                }
                break;
            }
            default:
            {
                return INTERNAL_ERROR;
            }
        }
    }
    if( linestatus == LINE_OPEN )
    {
        return NO_REQUEST;
    }
    else
    {
        return BAD_REQUEST;
    }
}

int main( int argc, char* argv[] )
{
    if( argc <= 2 )
    {
        printf( "usage: %s ip_address port_number\n", basename( argv[0] ) );
        return 1;
    }
    const char* ip = argv[1];
    int port = atoi( argv[2] );
    
    struct sockaddr_in address;
    bzero( &address, sizeof( address ) );
    address.sin_family = AF_INET;
    inet_pton( AF_INET, ip, &address.sin_addr );
    address.sin_port = htons( port );
    
    int listenfd = socket( PF_INET, SOCK_STREAM, 0 );
    assert( listenfd >= 0 );
    
    int ret = bind( listenfd, ( struct sockaddr* )&address, sizeof( address ) );
    assert( ret != -1 );
    
    ret = listen( listenfd, 5 );
    assert( ret != -1 );
    
    struct sockaddr_in client_address;
    socklen_t client_addrlength = sizeof( client_address );
    int fd = accept( listenfd, ( struct sockaddr* )&client_address, &client_addrlength );
    if( fd < 0 )
    {
        printf( "errno is: %d\n", errno );
    }
    else
    {
        char buffer[ BUFFER_SIZE ];
        memset( buffer, '\0', BUFFER_SIZE );
        int data_read = 0;
        int read_index = 0;
        int checked_index = 0;
        int start_line = 0;
        CHECK_STATE checkstate = CHECK_STATE_REQUESTLINE;
        while( 1 )
        {
            data_read = recv( fd, buffer + read_index, BUFFER_SIZE - read_index, 0 );
            if ( data_read == -1 )
            {
                printf( "reading failed\n" );
                break;
            }
            else if ( data_read == 0 )
            {
                printf( "remote client has closed the connection\n" );
                break;
            }
    
            read_index += data_read;
            HTTP_CODE result = parse_content( buffer, checked_index, checkstate, read_index, start_line );
            if( result == NO_REQUEST )
            {
                continue;
            }
            else if( result == GET_REQUEST )
            {
                send( fd, szret[0], strlen( szret[0] ), 0 );
                break;
            }
            else
            {
                send( fd, szret[1], strlen( szret[1] ), 0 );
                break;
            }
        }
        close( fd );
    }
    
    close( listenfd );
    return 0;
}

```


### 8.7 提高服务器性能的其他建议 / 144
#### 8.7.1 池 / 144

既然服务器的硬件资源“**充裕**”，那么提高服务器性能的一个很直接的方法就是以空间换时间，即“**浪费**”服务器的硬件资源，以换取其运行效率。这就是池（`pool`）的概念。池是一组资源的集合，这组资源在服务器启动之初就被完全创建好并初始化，这称为静态资源分配。当服务器进人正式运行阶段，即开始处理客户请求的时候，如果它需要相关的资源，就可以直接从池中获取，无须动态分配。很显然，直接从池中取得所需资源比动态分配资源的速度要快得多，因为分配系统资源的系统调用都是很耗时的。当服务器处理完一个客户连接后，可以把相关的资源放回池中，无须执行系统调用来释放资源。从最终的效果来看，池相当于服务器管理系统资源的应用层设施，它避免了服务器对内核的频繁访问。

不过，既然池中的资源是预先静态分配的，我们就无法预期应该分配多少资源。这个问题又该如何解决呢？最简单的解决方案就是分配“**足够多**”的资源，即针对每个可能的客户连接都分配必要的资源。这通常会导致资源的浪费，因为任一时刻的客户数量都可能远远没有达到服务器能支持的最大客户数量。好在这种资源的浪费对服务器来说一般不会构成问题。还有一种解决方案是预先分配一定的资源，此后如果发现资源不够用，就再动态分配一些并加入池中。

根据不同的资源类型，池可分为多种，常见的有**内存池**、**进程池**、**线程池**和**连接池**。它们的含义都很明确。

内存池通常用于 `socket` 的接收缓存和发送缓存。对于某些长度有限的客户请求，比如 `HTTP` 请求，预先分配一个大小足够（比如 5000 字节）的接收缓存区是很合理的。当客户请求的长度超过接收缓冲区的大小时，我们可以选择丢弃请求或者动态扩大接收缓冲区。

进程池和线程池都是并发编程常用的“伎俩”。当我们需要一个工作进程或工作线程来处理新到来的客户请求时，我们可以直接从进程池或线程池中取得一个执行实体，而无须动态地调用 `fork` 或 `pthread_ create` 等函数来创建进程和线程。

连接池通常用于服务器或服务器机群的内部永久连接。图 8-4 中，每个逻辑单元可能都需要频繁地访问本地的某个数据库。简单的做法是：逻辑单元每次需要访问数据库的时候，就向数据库程序发起连接，而访问完毕后释放连接。很显然，这种做法的效率太低。一种解决方案是使用连接池。连接池是服务器预先和数据库程序建立的一组连接的集合。当某个逻辑单元需要访问数据库时，它可以直接从连接池中取得一个连接的实体并使用之。待完成数据库的访问之后，逻辑单元再将该连接返还给连接池。

#### 8.7.2 数据复制 / 145

高性能服务器应该避免不必要的数据复制，尤其是当数据复制发生在用户代码和内核之间的时候。如果内核可以直接处理从 `socket` 或者文件读入的数据，则应用程序就没必要将这些数据从内核缓冲区复制到应用程序缓冲区中。这里说的“直接处理”指的是应用程序不关心这些数据的内容，不需要对它们做任何分析。比如 `ftp` 服务器，当客户请求一个文件时，服务器只需要检测目标文件是否存在，以及客户是否有读取它的权限，而绝对不会关心文件的具体内容。这样的话，`ftp` 服务器就无须把目标文件的内容完整地读人到应用程序缓冲区中并调用 `send` 函数来发送，而是可以使用“零拷贝”函数 `sendfile` 来直接将其发送给客户端。

此外，用户代码内部（不访问内核）的数据复制也是应该避免的。举例来说，当两个工作进程之间要传递大量的数据时，我们就应该考虑使用共享内存来在它们之间直接共享这些数据，而不是使用管道或者消息队列来传递。又比如代码清单 8-3 所示的解析 `HTTP` 请求的实例中，我们用指针（`start_ line`）来指出每个行在 `buffer` 中的起始位置，以便随后对行内容进行访向，而不是把行的内容复制到另外一个缓冲区中来使用，因为这样既浪费空间，又效率低下。

#### 8.7.3 上下文切换和锁 / 145

并发程序必须考虑**上下文切换**（`context switch`）的问题，即**进程切换或线程切换导致的的系统开销**。即使是`I/O`密集型的服务器，也不应该使用过多的工作线程(或工作进程，下同），否则线程间的切换将占用大量的 CPU 时间，服务器真正用于处理业务逻辑的 CPU 时间的比重就显得不足了。因此，为每个客户连接都创建一个工作线程的服务器模型是不可取的。图 8-11 所描述的半同步 / 半异步模式是一种比较合理的解决方案，它允许一个线程同时处理多个客户连接。此外，多线程服务器的一个优点是不同的线程可以同时运行在不同的 CPU 上。当线程的数量不大于 CPU 的数目时，上下文的切换就不是问题了。

并发程序需要考虑的另外一个问题是**共享资源的加锁保护**。锁通常被认为是导致服务器效率低下的一个因素，因为由它引入的代码不仅不处理任何业务逻辑，而且需要访问内核资源。因此，服务器如果有更好的解决方案，就应该避免使用锁。显然，图 8-11 所描述的半同步 1 半异步模式就比图 8-10 所描述的半同步 / 半反应堆模式的效率高。如果服务器必须使用“锁”，则可以考虑**减小锁的粒度**，比如使用读写锁。当所有工作线程都只读取一块共享内存的内容时，读写锁并不会增加系统的额外开销。只有当其中某一个工作线程需要写这块内存时，系统才必须去锁住这块区域。

## 第9章 I/O 复用 / 146

`I/O` 复用使得程序能同时监听多个文件描述符，这对提高程序的性能至关重要。通常，网络程序在下列情况下需要使用 `I/O` 复用技术：

- 客户端程序要同时处理多个 `socket`。比如本章将要讨论的非阻塞 `connect` 技术。
- 客户端程序要同时处理用户输人和网络连接。比如本章将要讨论的聊天室程序。
- `TCP` 服务器要同时处理监听 `socket` 和连接 `socket`。这是 `I/O` 复用使用最多的场合。后续章节将展示很多这方面的例子。
- 服务器要同时处理 `TCP` 请求和`UDP`请求。比如本章将要讨论的回射服务器。
- 服务器要同时监听多个端口，或者处理多种服务。比如本章将要讨论的`xinetd` 服务器。

需要指出的是，`I/O` 复用虽然能同时监听多个文件描述符，但它本身是阻塞的。并且当多个文件描述符同时就绪时，如果不采取额外的措施，程序就只能按顺序依次处理其中的每一个文件描述符，这使得服务器程序看起来像是串行工作的。如果要实现并发，只能使用多进程或多线程等编程手段。

`Linux` 下实现 `I/O` 复用的系统调用主要有 **`sclect`、`poll` 和 `epoll`**，本章将依次讨论之，然后介绍使用它们的几个实例。

### 9.1 select 系统调用 / 146

`select` **系统调用的用途是：在一段指定时间内，监听用户感兴趣的文件描述符上的可读、可写和异常等事件**。本节先介绍 `select` 系统调用的 `API`，然后讨论 `select` 判断文件描述符就绪的条件，最后给出它在处理带外数据中的实际应用。

#### 9.1.1 select API / 146

`select` 系统调用的原型如下：

```
#include <sys/select.h>

int select (int nfds, fd_set* readfds, fd_set* writefds, fd_set* exceptfds,struct timeval# timeout);
```

1) `nfds` 参数指定被监听的文件描述符的总数。它通常被设置为 `sclect` 监听的所有文件描述符中的最大值加 1, 因为文件描述符是从 0 开始计数的。

2) `readfds`、`writefds` 和 `exceptfds` 参数分别指向可读、可写和异常等事件对应的文件描述符集合：应用程序调用 `select` 函数时，通过这 3 个参数传人自己感兴趣的文件描述符。`sclect` 调用返回时，内核将修改它们来通知应用程序哪些文件描述符已经就绪。这 3 个参数是 `fd_set` 结构指针类型。`fd_set` 结构体的定义如下：

```
#include  <typesizes.h>
#define __FD_SETSIZE 1024

#include <sys/select.h>
#define FD_SETSIZE __FD_SETSIZE
typedef long int __fd_mask;
#undef __NFDBITS
#define __NFDBITS  (8 *（int) sizeof  (__fd_mask))

typedef struct
{
    #ifdef __USE_XOPEN
    
        __fd_mask fds_bits[ __FD_SETSIZE / __NFDBITS];
        
    # define __FDS__BITS(set)  ((set)->fds_bits)
    # else
    
        __fd_mask __fds_bits[ __FD_SETSIZE / __NFDBITS];
    
    # define __FDS_BITS(set)  ((set)->__fds_bits) 
    # endif
    
} fd_set;
```

由以上定义可见，`fd_set`结构体仅包含一个整型数组，该数组的每个元素的每位（bit）标记一个文件描述符。`fd_set` 能容纳的文件描述符数量由 `FD_ SETSIZE` 指定，这就限制了`select`能同时处理的文件描述符的总量。

由于位操作过于烦琐，我们应该使用下面的一系列宏来访问 `fd_set`结构体中的位:

```
#include <sys/select.h>
FD_ZERO (fd set *fdset); /*清除 fdset 的所有位*/
FD_SET (int fd, fd_set *fdset); /*设置 fdset 的位 fd */
FD_CLR (int fd, fd_set *fdset); /*清除 fdset 的位 fd */
int FD_ISSET (int fd, fd_set *fdset); /*测试 fdset 的位 rd 是否被设置*/
```

3) `timeout` 参数用来设置 `selecet` 函数的超时时间。它是一个 `timeval` 结构类型的指针，采用指针参数是因为内核将修改它以告诉应用程序 `seleet` 等待了多久。不过我们不能完全信任 `select` 调用返回后的 `timeout` 值，比如调用失败时 `timeout` 值是不确定的。`timeval` 结构体的定义如下：

```
struct timeval
{
    long tv_sec; /*秒数*/
    long tv_usec; /*微秒数*/  
}
```

由以上定义可见，`select` 给我们提供了一个微秒级的定时方式。如果给 `timeout` 变量的 `tv_sec` 成员和 `tv_usec` 成员都传递 `0`，则 `select` 将立即返回。如果给`timeout`传递`NULL`，则`select`将一直阻塞，直到某个文件描述符就绪。

`select` 成功时返回就绪（可读、可写和异常）文件描述符的总数。如果在超时时间内没有任何文件描述符就绪，`select`将返回`0`，`select`失败时返回`-1`并设置 `error`。如果在 `select` 等待期间，程序接收到信号，则 `select` 立即返回`-1`，并设置 `error` 为 `EINTR`。

#### 9.1.2 文件描述符就绪条件 / 148

哪些情况下文件描述符可以被认为是可读、可写或者出现异常，对于`select` 的使用非常关键。在网络编程中，下列情况下 `socket` 可读：

- `socket` 内核接收缓存区中的字节数大于或等于其低水位标记 `SO_RCVLOWAT`。此时我们可以无阻塞地读该 socket，并且读操作返回的字节数大于 0。
- `socket` 通信的对方关闭连接。此时对该 `socket` 的读操作将返回 0。
- 监听 `socket` 上有新的连接请求。
- `socket` 上有未处理的错误。此时我们可以使用`getsockopt`来读取和清除该错误。

下列情况下 `socket` 可写：

- `socket` 内核发送缓存区中的可用字节数大于或等于其低水位标记 `SO_SNDLOWAT`。此时我们可以无阻塞地写该`socket`，并且写操作返回的字节数大于 0。

- `socket` 的写操作被关闭。对写操作被关闭的`socket`执行写操作将触发一个 `SIGPIPE` 信号。
- `socket` 使用非阻塞 `connect` 连接成功或者失败（超时）之后。
- `socket` 上有未处理的错误。此时我们可以使用`getsockopt`来读取和清除该错误。网络程序中，`select` 能处理的异常情况只有一种：`socket` 上接收到带外数据。下面我们详细讨论之一。

#### 9.1.3 处理带外数据 / 148

上一小节提到，`socket` 上接收到普通数据和带外数据都将使 `sclect` 返回，但 `socket` 处于不同的就绪状态：前者处于可读状态，后者处于异常状态。代码清单 9-1 描述了 `select` 是如何同时处理二者的。

<center>代码清单 9-1 同时接受普通数据和带外数据</center>


```
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <assert.h>
#include <stdio.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <fcntl.h>
#include <stdlib.h>

int main( int argc, char* argv[] )
{
	if( argc <= 2 )
	{
		printf( "usage: %s ip_address port_number\n", basename( argv[0] ) );
		return 1;
	}
	const char* ip = argv[1];
	int port = atoi( argv[2] );
	printf( "ip is %s and port is %d\n", ip, port );

	int ret = 0;
    struct sockaddr_in address;
    bzero( &address, sizeof( address ) );
    address.sin_family = AF_INET;
    inet_pton( AF_INET, ip, &address.sin_addr );
    address.sin_port = htons( port );

	int listenfd = socket( PF_INET, SOCK_STREAM, 0 );
	assert( listenfd >= 0 );

    ret = bind( listenfd, ( struct sockaddr* )&address, sizeof( address ) );
	assert( ret != -1 );

	ret = listen( listenfd, 5 );
	assert( ret != -1 );

	struct sockaddr_in client_address;
    socklen_t client_addrlength = sizeof( client_address );
	int connfd = accept( listenfd, ( struct sockaddr* )&client_address, &client_addrlength );
	if ( connfd < 0 )
	{
		printf( "errno is: %d\n", errno );
		close( listenfd );
	}

	char remote_addr[INET_ADDRSTRLEN];
	printf( "connected with ip: %s and port: %d\n", inet_ntop( AF_INET, &client_address.sin_addr, remote_addr, INET_ADDRSTRLEN ), ntohs( client_address.sin_port ) );

	char buf[1024];
        fd_set read_fds;
        fd_set exception_fds;

        FD_ZERO( &read_fds );
        FD_ZERO( &exception_fds );

    int nReuseAddr = 1;
	setsockopt( connfd, SOL_SOCKET, SO_OOBINLINE, &nReuseAddr, sizeof( nReuseAddr ) );
	while( 1 )
	{
		memset( buf, '\0', sizeof( buf ) );
		FD_SET( connfd, &read_fds );
		FD_SET( connfd, &exception_fds );

    	ret = select( connfd + 1, &read_fds, NULL, &exception_fds, NULL );
	    printf( "select one\n" );
    	if ( ret < 0 )
    	{
            	printf( "selection failure\n" );
            	break;
    	}
	
        if ( FD_ISSET( connfd, &read_fds ) )
		{
        	ret = recv( connfd, buf, sizeof( buf )-1, 0 );
			if( ret <= 0 )
			{
				break;
			}
			printf( "get %d bytes of normal data: %s\n", ret, buf );
		}
		else if( FD_ISSET( connfd, &exception_fds ) )
    	{
    		ret = recv( connfd, buf, sizeof( buf )-1, MSG_OOB );
    		if( ret <= 0 )
    		{
    			break;
    		}
    		printf( "get %d bytes of oob data: %s\n", ret, buf );
    	}

	}

	close( connfd );
	close( listenfd );
	return 0;
}
```


### 9.2 poll 系统调用 / 150
### 9.3 epoll系列系统调用  /151
#### 9.3.1 内核事件表 / 151
#### 9.3.2 epoll_wait函数 / 152
#### 9.3.3 LT和ET模式 / 153
#### 9.3.4 EPOLLONESHOT事件 / 157

### 9.4 三组I/O复用函数的比较 / 161
### 9.5 I/O复用的高级应用一：非阻塞connent / 162
### 9.6 I/O复用的高级应用二：聊天室程序 / 165
#### 9.6.1 客户端 / 165
#### 9.6.2 服务器 / 167
### 9.7 I/O复用的高级应用三：同时处理TCP和UDP服务 / 171
### 9.8 超级服务xinted / 175
#### 9.8.1 xinted配置文件 / 175
#### 9.8.2 xinted工作流程 / 176

## 第10章 信号 / 178
### 10.1 Linux信号概述 / 178
#### 10.1.1 发送信号 / 178
#### 10.1.2 信号处理方式 / 179
#### 10.1.3 Linux 信号 / 179
#### 10.1.4 中断系统调用 / 181

### 10.2 信号函数 / 181
#### 10.2.1 signal系统调用 / 181
#### 10.2.2 sigaction系统调用 / 181

### 10.3 信号集 / 182
#### 10.3.1 信号集函数 / 182
#### 10.3.2 进程信号掩码 / 183
#### 10.3.3 被挂起的信号 / 183

### 10.4 统一事件源 / 184
### 10.5 网路编程相关信号 / 188
#### 10.5.1 SIGHUP / 188
#### 10.5.2 SIGPIPE / 189
#### 10.5.3 SIGURG / 190

## 第11章 定时器 / 193
### 11.1 socket选项 SO_RCVTIMEO和SO_SNDTIMEO / 193
### 11.2 SIGALRM 信号 / 195
#### 11.2.1 基于升序链表的定时器 / 195
#### 11.2.2 处理非活动连接 / 200
### 11.3 I/O复用系统调用的超时参数 / 205
### 11.4 高性能定时器  / 206
#### 11.4.1 事件轮 / 206
#### 11.4.2 时间堆 / 211

## 第12章 高性能I/O框架 Libevent / 218
### 12.1 I/O框架概述 / 218
### 12.2 Libevent源码分析 / 220
#### 12.2.1 一个实例 / 220
#### 12.2.2 源代码组织结构 / 222
#### 12.2.3 event 结构体 / 224
#### 12.2.4 往注册事件队列中添加事件处理器 / 226
#### 12.2.5 往事件多路分发器中注册事件 / 230
#### 12.2.6 eventop结构体 / 223
#### 12.2.7 event_base结构体 / 235
#### 12.2.8 事件循环 / 236

## 第13章 多进程编程 / 239
### 13.1 fork 系统调用 / 239
### 13.2 exec 系列系统调用 / 240
### 13.3 处理僵尸进程 / 240
### 13.4 管道 / 241
### 13.5 信号量 / 243
#### 13.5.1  信号量原语 / 243
#### 13.5.2 semget 系统调用 / 244
#### 13.5.3 semop 系统调用 / 245
#### 13.5.4 semctl 系统调用 / 247
#### 13.5.5 特殊键值IPC_PRIVATE / 249
### 13.6 共享内存 / 251
### 13.6.1 shmget 系统调用 / 251
### 13.6.2 shamat 和 shmdt 系统调用 / 252
### 13.6.3 shmctl 系统调用 / 253
### 13.6.4 共享内存的POSIX方法 / 254
### 13.6.5 共享内存实例 / 254

### 13.7 消息队列 / 263
#### 13.7.1 msgget 系统调用 / 263
#### 13.7.2 msgsnd 系统调用 / 264
#### 13.7.3 msgrcv 系统调用 / 264
#### 13.7.4 msgctl 系统调用 / 265
### 13.8 IPC命令 / 266
### 13.9 在进程间传递文件描述符 / 267

## 第14章 多线程编程 / 269
### 14.1 Linux线程概述 / 269
#### 14.1.1 线程模型  /269
#### 14.1.2 Linux 线程库  /270
### 14.2 创建线程和结束线程 / 271
### 14.3 线程属性 / 273
### 14.4 POSIX 信号量 / 278
### 14.5 互斥锁 / 276
#### 14.5.1 互斥锁基础API / 276
#### 14.5.2 互斥锁属性 / 277
#### 14.5.3 死锁举例 / 278
### 14.6 条件变量 / 279
### 14.7 线程同步机制包装类 / 280
### 14.8 多线程环境 / 282
#### 14.8.1 可重入函数 / 282
#### 14.8.2 线程和进程 / 283
#### 14.8.3 线程和信号 / 284

## 第15章 进程池和线程池概述 / 287
### 15.1 进程池和线程池概述 / 287
### 15.2 处理多客户 / 288
### 15.3 半同步/半异步进程池实现 / 289
### 15.4 用进程池实现简单CGI服务器 / 298
### 15.5 半同步半异步/反应堆线程池实现  /301
### 15.6 用线程池实现的简单Web服务器 / 304
#### 15.6.1 http_conn类 / 304
#### 15.6.2 main函数 / 318

# 第三篇 高性能服务器优化检测

## 第16章 服务器调制、调试和测试 / 324
### 16.1 最大文件描述符数 / 324
### 16.2 调整内核参数 / 325
#### 16.2.1 /proc/sys/fs 目录下的部分文件 / 325
#### 16.2.2 /proc/sys/net 目录下的部分文件 / 325
### 16.3 gdp调试 / 326
#### 16.3.1 用gdp调试多进程程序 / 326
#### 16.3.2 用gdp调试多线程程序 / 328
### 16.4 压力测试 / 329

## 第17章 系统检测工具 / 333

### 17.1 tcpdump / 333
### 17.2 lsof / 334
### 17.3 nc / 336
### 17.4 strace / 338
### 17.5 netstat / 341
### 17.6 vmstat / 342
### 17.7 ifsfat / 344
### 17.8 mapstat / 344

## 文献 / 346