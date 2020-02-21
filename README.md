# ConnectionPool
数据库连接池

### 开发平台
有关MySQL数据库编程、多线程编程、线程互斥和同步通信操作、智能指针、设计模式、容器等等这些 技术在C++语言层面都可以直接实现，因此该项目选择直接在windows平台上进行开发，当然放在 Linux平台下用g++也可以直接编译运行

### 关键技能点
MySQL数据库编程、单例模式、queue队列容器、C++11多线程编程、线程互斥、线程同步通信和 unique_lock、基于CAS的原子整形、智能指针shared_ptr、lambda表达式、生产者-消费者线程模型 

### 实现功能
1.连接池只需要一个实例，所以ConnectionPool以单例模式进行设计
2.从ConnectionPool中可以获取和MySQL的连接Connection
3.空闲连接Connection全部维护在一个线程安全的Connection队列中，使用线程互斥锁保证队列的线 程安全 
4.如果Connection队列为空，还需要再获取连接，此时需要动态创建连接，上限数量是maxSize 
5.队列中空闲连接时间超过maxIdleTime的就要被释放掉，只保留初始的initSize个连接就可以了，这个 功能点肯定需要放在独立的线程中去做 
6.如果Connection队列为空，而此时连接的数量已达上限maxSize，那么等待connectionTimeout时间 如果还获取不到空闲的连接，那么获取连接失败，此处从Connection队列获取空闲连接，可以使用带 超时时间的mutex互斥锁来实现连接超时时间
7.用户获取的连接用shared_ptr智能指针来管理，用lambda表达式定制连接释放的功能（不真正释放 连接，而是把连接归还到连接池中） 
8.连接的生产和连接的消费采用生产者-消费者线程模型来设计，使用了线程间的同步通信机制条件变量 和互斥锁 

