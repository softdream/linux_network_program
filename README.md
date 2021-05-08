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
&emsp; 3.1.1功能：
&emsp;&emsp;内核会产生一个epoll实例数据结构并返回一个文件描述符，这个特殊的数据结构就是epoll实例的句柄，另外两个API都以他为基础。
&emsp; 3.1.2参数：
&emsp;&emsp;size表示要监视的未按描述符的最大值；
&emsp; 3.1.3返回值：
&emsp;&emsp;返回值是epoll的文件描述符。
### 3.2 ```int epoll_ctl( int epfd, int opt, int fd, struct epoll_event *event )```
&emsp;3.2.1功能：
&emsp;&emsp;将被监听的描述符添加到红黑树或者从红黑树中删除或者对监听的事件进行修改。<br>
&emsp;3.2.2参数：<br>
&emsp;&emsp;epfd是指epoll的文件描述符，即```int epoll_create( int size )```的返回值。<br>
&emsp;&emsp;opt参数的操作类型有：<br>
&emsp;&emsp;&emsp;```EPOLL_CTL_ADD```，向interest list中添加一个需要监视的描述符；<br>
&emsp;&emsp;&emsp;```EPOLL_CTL_DEL```, 向interest list中删除一个描述符；<br>
&emsp;&emsp;&emsp;```EPOLL_CTL_MOD```, 修改interest list中的一个描述符。<br>
&emsp;&emsp;fd是要对其进行操作的事件的文件描述符。<br>
&emsp;&emsp;event是一个事件的结构体。其中：<br>
&emsp;&emsp;```struct epoll_event```的结构如下：<br>
&emsp;&emsp;```typedef union epoll_data```<br>
&emsp;&emsp;&emsp;```void *ptr;```<br>
&emsp;&emsp;&emsp;```int fd;```<br>
&emsp;&emsp;&emsp;```uint32_t u32;```<br>
&emsp;&emsp;&emsp;```uint64_t u64;```<br>
&emsp;&emsp;```}epoll_data_t;```<br>
&emsp;&emsp;```struct epoll_event{```<br>
&emsp;&emsp;&emsp;```uint32_t events;```<br>
&emsp;&emsp;&emsp;```epoll_data_t data;```<br>
&emsp;&emsp;```};```<br>
