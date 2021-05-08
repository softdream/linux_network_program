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
### 1. ```int epoll_create( int size )```
