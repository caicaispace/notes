# php 协程

[TOC]

## 并发模型
### 进程

> [进程（英语：process）](https://zh.wikipedia.org/wiki/%E8%A1%8C%E7%A8%8B)，是计算机中已运行程序的实体。进程为曾经是分时系统的基本运作单位。在面向进程设计的系统（如早期的UNIX，Linux 2.4及更早的版本）中，进程是程序的基本执行实体；在面向线程设计的系统（如当代多数操作系统、Linux 2.6及更新的版本）中，进程本身不是基本运行单位，而是线程的容器。程序本身只是指令、数据及其组织形式的描述，进程才是程序（那些指令和数据）的真正运行实例。若干进程有可能与同一个程序相关系，且每个进程皆可以同步（循序）或异步（平行）的方式独立运行。现代计算机系统可在同一段时间内以进程的形式将多个程序加载到内存中，并借由时间共享（或称时分复用），以在一个处理器上表现出同时（平行性）运行的感觉。同样的，使用多线程技术（多线程即每一个线程都代表一个进程内的一个独立执行上下文）的操作系统或计算机架构，同样程序的平行线程，可在多CPU主机或网络上真正同时运行（在不同的CPU上）。

### 线程

> [线程（英语：thread）](https://zh.wikipedia.org/wiki/%E7%BA%BF%E7%A8%8B)是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。在Unix System V及SunOS中也被称为轻量进程（lightweight processes），但轻量进程更多指内核线程（kernel thread），而把用户线程（user thread）称为线程。
线程是独立调度和分派的基本单位。线程可以操作系统内核调度的内核线程，如Win32线程；由用户进程自行调度的用户线程，如Linux平台的POSIX Thread；或者由内核与用户进程，如Windows 7的线程，进行混合调度。
同一进程中的多条线程将共享该进程中的全部系统资源，如虚拟地址空间，文件描述符和信号处理等等。但同一进程中的多个线程有各自的调用栈（call stack），自己的寄存器环境（register context），自己的线程本地存储（thread-local storage）。
一个进程可以有很多线程，每条线程并行执行不同的任务。
在多核或多CPU，或支持Hyper-threading的CPU上使用多线程程序设计的好处是显而易见，即提高了程序的执行吞吐率。在单CPU单核的计算机上，使用多线程技术，也可以把进程中负责IO处理、人机交互而常被阻塞的部分与密集计算的部分分开来执行，编写专门的workhorse线程执行密集计算，从而提高了程序的执行效率。

### 协程

> 协程不是进程或线程，其执行过程更类似于子例程，或者说不带返回值的函数调用。
一个程序可以包含多个协程，可以对比与一个进程包含多个线程，因而下面我们来比较协程和线程。我们知道多个线程相对独立，有自己的上下文，切换受系统控制；而协程也相对独立，有自己的上下文，但是其切换由自己控制，由当前协程切换到其他协程由当前协程来控制。


## 进程与线程的一个简单解释

进程（process）和线程（thread）是操作系统的基本概念，但是它们比较抽象，不容易掌握。最近，我读到一篇材料，发现有一个很好的类比，可以把它们解释地清晰易懂。

1. 计算机的核心是CPU，它承担了所有的计算任务。它就像一座工厂，时刻在运行。

2. 假定工厂的电力有限，一次只能供给一个车间使用。也就是说，一个车间开工的时候，其他车间都必须停工。背后的含义就是，单个CPU一次只能运行一个任务。
3. 进程就好比工厂的车间，它代表CPU所能处理的单个任务。任一时刻，CPU总是运行一个进程，其他进程处于非运行状态。
4. 一个车间里，可以有很多工人。他们协同完成一个任务。
5. 线程就好比车间里的工人。一个进程可以包括多个线程。
6. 车间的空间是工人们共享的，比如许多房间是每个工人都可以进出的。这象征一个进程的内存空间是共享的，每个线程都可以使用这些共享内存。
7. 可是，每间房间的大小不同，有些房间最多只能容纳一个人，比如厕所。里面有人的时候，其他人就不能进去了。这代表一个线程使用某些共享内存时，其他线程必须等它结束，才能使用这一块内存。
8. 一个防止他人进入的简单方法，就是门口加一把锁。先到的人锁上门，后到的人看到上锁，就在门口排队，等锁打开再进去。这就叫"互斥锁"（Mutual exclusion，缩写 Mutex），防止多个线程同时读写某一块内存区域。
9. 还有些房间，可以同时容纳n个人，比如厨房。也就是说，如果人数大于n，多出来的人只能在外面等着。这好比某些内存区域，只能供给固定数目的线程使用。
10. 这时的解决方法，就是在门口挂n把钥匙。进去的人就取一把钥匙，出来时再把钥匙挂回原处。后到的人发现钥匙架空了，就知道必须在门口排队等着了。这种做法叫做"信号量"（Semaphore），用来保证多个线程不会互相冲突。不难看出，mutex是semaphore的一种特殊情况（n=1时）。也就是说，完全可以用后者替代前者。但是，因为mutex较为简单，且效率高，所以在必须保证资源独占的情况下，还是采用这种设计。
11. 操作系统的设计，因此可以归结为三点：
    - 以多进程形式，允许多个任务同时运行；
    - 以多线程形式，允许单个任务分成不同的部分运行；
    - 提供协调机制，一方面防止进程之间和线程之间产生冲突，另一方面允许进程之间和线程之间共享资源。
（完）


[在 PHP 中使用 Promise + co/yield 协程](https://segmentfault.com/a/1190000007760513)

[协程实现mysql查询的异步化](http://www.bo56.com/%E9%80%9A%E8%BF%87%E5%8D%8F%E7%A8%8B%E5%AE%9E%E7%8E%B0mysql%E6%9F%A5%E8%AF%A2%E7%9A%84%E5%BC%82%E6%AD%A5%E5%8C%96/)

## yield

- 中断程序执行
- 保留上下文
- 生成器对象

## 实现原理

- setjmp、longjmp实现，在进行协程切换时会自动保存Zend VM的内存状态（主要是EG全局内存和vm stack）
- 协程基本都是setjmp、longjmp，汇编的jmp指令，更多用在抛异常

## 运行过程


- 调用onRequest事件回调函数时，底层会调用C函数coro_create创建一个协程（#1位置），同时保存这个时间点的CPU寄存器状态和ZendVM stack信息。
- 调用mysql->connect时发生IO操作，底层会调用C函数coro_save保存当前协程的状态，包括Zend VM上下文以及协程描述信息，并调用coro_yield让出程序控制权，当前的请求会挂起（#2位置）
- 协程让出程序控制权后，会继续进入EventLoop处理其他事件，这时Swoole会继续去处理其他客户端发来的Request
- IO事件完成后，MySQL连接成功或失败，底层调用C函数core_resume恢复对应的协程，恢复ZendVM上下文，继续向下执行PHP代码（#3位置）
- mysql->query的执行过程与mysql->connect一致，也会进行一次协程切换调度
- 所有操作完成后，调用end方法返回结果，并销毁此协程

## 协程开销

相比普通的异步回调程序，协程多增加额外的内存占用。

- Swoole2.0协程需要为每个并发保存zend stack栈内存并维护对应的虚拟机状态。如果程序并发很大可能会占用大量内存，取决于C函数、ZendVM 调用栈深度
- 协程调度会增加额外的一些CPU开销

## Exp
### mysql


```
function f1(){
    $db = new db();
    $obj = $db->async_query('select sleep(1)');
    echo "f1 async_query \n";
    yield $obj;
    $row = $db->fetch();
    echo "f1 fetch\n";
    yield $row;
}

function f2(){
    $db = new db();
    $obj = $db->async_query('select sleep(1)');
    echo "f2 async_query\n";
    yield $obj;
    $row = $db->fetch();
    echo "f2 fetch\n";
    yield $row;
}

$gen1 = f1();
$gen2 = f2();

$gen1->current();
$gen2->current();
$gen1->next();
$gen2->next();

$ret1 = $gen1->current();
$ret2 = $gen2->current();

var_dump($ret1);
var_dump($ret2);

class db{
    static $links;
    private $obj;

    function getConn(){
        $host = '127.0.0.1';
        $user = 'root';
        $password = 'goodluck888';
        $database = 'easyswoole';
        $this->obj = new mysqli($host, $user, $password, $database);
        self::$links[spl_object_hash($this->obj)] = $this->obj;
        return self::$links[spl_object_hash($this->obj)];
    }

   function async_query($sql){
        $link = $this->getConn();
        $link->query($sql, MYSQLI_ASYNC);
        return $link;
    }

    function fetch(){
        for($i = 1; $i <= 5; $i++){
            $read = $errors = $reject = self::$links;
            $re = mysqli_poll($read, $errors, $reject, 1);
            foreach($read as $obj){
                if($this->obj === $obj){
                    $sql_result = $obj->reap_async_query();
                    $sql_result_array = $sql_result->fetch_array(MYSQLI_ASSOC);//只有一行
                    $sql_result->free();
                    return $sql_result_array;
                }
            }
        }
    }

}
```
### http
```
require_once("hprose-php/src/Hprose.php");

use Hprose\Promise;

function fetch($url) {
    $dns_lookup = Promise\promisify('swoole_async_dns_lookup');
    $url = parse_url($url);
    echo '1' . PHP_EOL;
    list($host, $ip) = (yield $dns_lookup($url['host']));
    // var_dump($host, $ip);
    $cli = new swoole_http_client($ip, isset($url['port']) ? $url['port'] : 80);
    $cli->setHeaders([
        'Host' => $host,
        "User-Agent" => 'Chrome/49.0.2587.3',
    ]);
    $get = Promise\promisify([$cli, 'get']);
    echo '2' . PHP_EOL;
    yield $get($url['path']);
    $writefile = Promise\promisify('swoole_async_writefile');
    echo '3' . PHP_EOL;
    list($filename) = (yield $writefile(basename($url['path']), $cli->body));
    echo "write $filename ok.\r\n";
    $cli->close();
}

$urls = array(
    'http://b.hiphotos.baidu.com/baike/c0%3Dbaike116%2C5%2C5%2C116%2C38/sign=5f4519ba037b020818c437b303b099b6/472309f790529822434d08dcdeca7bcb0a46d4b6.jpg',
    'http://f.hiphotos.baidu.com/baike/c0%3Dbaike116%2C5%2C5%2C116%2C38/sign=1c37718b3cc79f3d9becec62dbc8a674/38dbb6fd5266d016dc2eaa5c902bd40735fa358a.jpg',
    'http://h.hiphotos.baidu.com/baike/c0%3Dbaike116%2C5%2C5%2C116%2C38/sign=edd05c9c502c11dfcadcb771024e09b5/d6ca7bcb0a46f21f3100c52cf1246b600c33ae9d.jpg',
    'http://a.hiphotos.baidu.com/baike/c0%3Dbaike92%2C5%2C5%2C92%2C30/sign=4693756e8094a4c21e2eef796f9d70b0/54fbb2fb43166d22df5181f5412309f79052d2a9.jpg',
    'http://a.hiphotos.baidu.com/baike/c0%3Dbaike92%2C5%2C5%2C92%2C30/sign=9388507144a98226accc2375ebebd264/faf2b2119313b07eb2cc820c0bd7912397dd8c45.jpg',
);

foreach ($urls as $url) {
    Promise\co(fetch($url));
}
```
