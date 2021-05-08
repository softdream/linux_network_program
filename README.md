# linux_network_program
linux网络编程学习
## 1. Epoll的概念
&emsp;Epoll是一种IO事件通知机制，是linux内核实现IO多路复用的一种方式。
IO多路复用是指，在一个操作里同时监听多个输入输出源，在其中一个或多个输入源可用的时候返回，并对其进行读写操作。
## 2. 事件
&emsp; 可读事件，当文件描述符关联的内核读缓冲区可读时，则触发读事件。
&emsp; 可写事件，当文件描述符关联的内核写缓冲区可写，则触发可写事件。
## 3. Epoll的API
&emsp; epoll的核心有三个API，核心数据结构是一个红黑树和一个链表。
### 3.1 ```int epoll_create( int size )```
&emsp; 3.1.1 功能：
&emsp;&emsp;内核会产生一个epoll实例数据结构并返回一个文件描述符，这个特殊的数据结构就是epoll实例的句柄，另外两个API都以他为基础。
&emsp; 3.1.2 参数：
&emsp;&emsp;size表示要监视的未按描述符的最大值；
&emsp; 3.1.3 返回值：
&emsp;&emsp;返回值是epoll的文件描述符。
### 3.2 ```int epoll_ctl( int epfd, int opt, int fd, struct epoll_event *event )```
&emsp;3.2.1 功能：
&emsp;&emsp;将被监听的描述符添加到红黑树或者从红黑树中删除或者对监听的事件进行修改。<br>
&emsp;3.2.2 参数：<br>
&emsp;&emsp;epfd是指epoll的文件描述符，即```int epoll_create( int size )```的返回值。<br>
&emsp;&emsp;opt参数的操作类型有：<br>
&emsp;&emsp;&emsp;```EPOLL_CTL_ADD```，向interest list中添加一个需要监视的描述符；<br>
&emsp;&emsp;&emsp;```EPOLL_CTL_DEL```, 向interest list中删除一个描述符；<br>
&emsp;&emsp;&emsp;```EPOLL_CTL_MOD```, 修改interest list中的一个描述符。<br>
&emsp;&emsp;fd是要对其进行操作的事件的文件描述符。<br>
&emsp;&emsp;event是一个事件的结构体。其中：<br>
&emsp;&emsp;```struct epoll_event```的结构如下：
```
typedef union epoll_data
  void *ptr;//指向用户自定义数据
  int fd;   //注册的文件描述符
  uint32_t u32;//32位整数
  uint64_t u64;//64位整数
}epoll_data_t;

struct epoll_event{
  uint32_t events;//描述epoll事件
  epoll_data_t data;
};
```
&emsp;&emsp;其中data域是用来给出描述符信息的字段，在调用epoll_ctl()加入一个需要检测的描述符的时候，一定要在此域中写入描述符相关信息。<br>
&emsp;&emsp;events域是用来描述一组epoll事件。其值为：
```
EPOLLIN：描述符处于可读状态；
EPOLLOUT：描述符处于可写状态；
EPOLLET：将epoll event通知模式设置为edge triggered；
EPOLLONESHOT：第一次进行通知，之后不在进行检测；
EPOLLHUP：本端描述符产生一个挂断事件，默认检测事件；
EPOLLRDHUP：对端描述符产生一个挂断事件；
EPOLLPRI：由带外数据触发；
EPOLLERR：描述符产生错误时触发，默认检测事件
```
### 3.3 ```int epoll_wait( int epfd, struct epoll_event *events, int maxevents, int timeout )```
&emsp;3.3.1 功能：<br>
&emsp;&emsp;阻塞等待注册事件发生，返回事件的数目，并将触发的事件写入events数组中。<br>
&emsp;3.3.2 参数：<br>
&emsp;&emsp;epfd是已创建的epoll的文件描述符；<br>
&emsp;&emsp;events是用来记录触发事件的集合；<br>
&emsp;&emsp;maxevents是返回events的最大个数；<br>
&emsp;&emsp;timeout描述函数调用过程中阻塞的时间上限。timeout=-1表示调用将一直阻塞，直到文件描述符进入ready状态或者捕获到信号才返回。<br>
&emsp;timeout==0用于非阻塞检测是否有描述符处于ready状态，不管结果怎么样，调用都立即返回。<br>
&emsp;timeout>0表示调用将最多持续timeout时间，如果期间有检测对象变成ready或者捕获到信号则返回，否则直到超时。<br>
&emsp;3.3.3 参数解释：<br>
&emsp;&emsp;处于ready状态的那些文件描述符会被复制进ready list中，epoll_wait用于向用户进程返回ready list。events和maxevents两个参数<br>&emsp;描述一个由用户分配的struct epoll event数组，调用返回时，内核将ready list复制到这个数组中，并将实际复制个数作为返回值，注意，如<br>&emsp;果ready list比maxevents大，则只能复制前maxevents个成员。<br>
