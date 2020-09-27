# synchronized、Lock、volatile?

​	并发编程中,为了保证线程安全，使用到锁（即synchronized、Lock）
​	线程安全：同时访问同一个 共享（多个线程可以同时访问该变量、对象或文件）、可变（数据在生命周期中可以被改变）资源 的情况，始终都不会导致数据破坏以及其他不该出现的结果
​	

# synchronized是什么？
-  定义：是java关键字，修饰类、方法或代码块，当一个线程获取了相应的锁，并执行这个被Synchronized修饰的类、方法或代码块时，其他线程等待获取锁的线程释放锁，保证类、方法或代码块以同步方式执行，

有对象锁和类锁（static方法和class上枷锁）区分，两者不冲突可以并行存在。
 + 特点：
     1. 无法判断锁的状态
     	2. 可重入、不可中断、非公平锁
     	3. 不需要手动释放锁，在方法执行完自动释放锁
     * 底层实现：底层使用指令码方式来控制锁的，映射成字节码指令就是增加来两个指令：monitorenter和monitorexit。当线程执行遇到monitorenter指令时会尝试获取内置锁，
     如果获取锁则锁计数器+1，如果没有获取锁则阻塞；当遇到monitorexit指令时锁计数器-1，如果计数器为0则释放锁


# Lock是什么？
	- 定义：是java.util.concurrent.locks包下常用的类，是一个接口，通过这个类可以实现同步访问，拥有比synchronized更多的功能和优点
	+ 特点：
		1. 可以判断锁的状态
		2. 可重入、可中断、可公平也可非公平
		3. 需要手动释放锁，如果没有主动释放锁，就有可能导致出现死锁现象。
	* 底层实现：底层是CAS乐观锁，依赖AbstractQueuedSynchronizer类，把所有的请求线程构成一个CLH队列。而对该队列的操作均通过Lock-Free（CAS）操作

# volatile
	- 定义：是java关键字，修饰变量，当一个线程修改了某个共享变量的值，其他线程能够立马得知这个修改，实现变量在多个线程之间的可见性
	+ 特点：
		1. 多线程访问volatile不会发生阻塞
		2. 禁止特定的处理器重排序
	* 实现： 当写一个volatile变量的时候，jmm会把本地内存中的共享变量刷新到主内存。当读一个volatile变量的是时候，jmm会把线程本地内存的值设置为无效，然后从主内存中读取共享变量。

# 比较synchronized、Lock
	1. Synchronized是关键字，内置语言实现，Lock是接口；
	2. synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；
而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
	3. Lock是可以中断锁，Synchronized是非中断锁，必须等待线程执行完成释放锁；
	4. 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到;
	5. Lock可以提高多个线程进行读操作的效率;
	6. 在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时Lock的性能要远远优于synchronized

# 比较synchronized、volatile
	1. volatile相对synchronized是一种轻量级同步策略（所以volatile性能比synchronized要好，并且随着JDK新版本的发布，sychronized关键字在执行上得到很大的提升，在开发中使用synchronized关键字的比率还是比较大）；
	2. volatile不具备互斥性
	3. volatile能保证变量在多个线程之间的可见性,不能保证原子性,sychronized可以保证原子性，也可以间接保证可见性，因为它会将私有内存和公有内存中的数据做同步