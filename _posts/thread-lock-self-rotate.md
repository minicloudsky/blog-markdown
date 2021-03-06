---
title: 自旋锁和互斥锁的区别
date: 2018-05-15 16:49:52
tags:
- 操作系统
categories: 
- 操作系统
---
POSIX threads(简称Pthreads)是在多核平台上进行并行编程的一套常用的API。线程同步(Thread Synchronization)是并行编程中非常重要的通讯手段，其中最典型的应用就是用Pthreads提供的锁机制(lock)来对多个线程之间共 享的临界区(Critical Section)进行保护(另一种常用的同步机制是barrier)。

Pthreads提供了多种锁机制：
(1) Mutex（互斥量）：pthread_mutex_***
(2) Spin lock（自旋锁）：pthread_spin_***
(3) Condition Variable（条件变量）：pthread_con_***
(4) Read/Write lock（读写锁）：pthread_rwlock_***
<!-- more -->
Pthreads提供的Mutex锁操作相关的API主要有：
pthread_mutex_lock (pthread_mutex_t *mutex);
pthread_mutex_trylock (pthread_mutex_t *mutex);
pthread_mutex_unlock (pthread_mutex_t *mutex);

Pthreads提供的与Spin Lock锁操作相关的API主要有：
pthread_spin_lock (pthread_spinlock_t *lock);
pthread_spin_trylock (pthread_spinlock_t *lock);
pthread_spin_unlock (pthread_spinlock_t *lock);

从 实现原理上来讲，Mutex属于sleep-waiting类型的锁。例如在一个双核的机器上有两个线程(线程A和线程B)，它们分别运行在Core0和 Core1上。假设线程A想要通过pthread_mutex_lock操作去得到一个临界区的锁，而此时这个锁正被线程B所持有，那么线程A就会被阻塞 (blocking)，Core0 会在此时进行上下文切换(Context Switch)将线程A置于等待队列中，此时Core0就可以运行其他的任务(例如另一个线程C)而不必进行忙等待。而Spin lock则不然，它属于busy-waiting类型的锁，如果线程A是使用pthread_spin_lock操作去请求锁，那么线程A就会一直在 Core0上进行忙等待并不停的进行锁请求，直到得到这个锁为止。



所以，自旋锁一般用用多核的服务器。 



自旋锁(Spin lock)

自旋锁与互斥锁有点类似，只是自旋锁不会引起调用者睡眠，如果自旋锁已经被别的执行单元保持，调用者就一直循环在那里看是 否该自旋锁的保持者已经释放了锁，"自旋"一词就是因此而得名。其作用是为了解决某项资源的互斥使用。因为自旋锁不会引起调用者睡眠，所以自旋锁的效率远 高于互斥锁。虽然它的效率比互斥锁高，但是它也有些不足之处：
    1、自旋锁一直占用CPU，他在未获得锁的情况下，一直运行－－自旋，所以占用着CPU，如果不能在很短的时 间内获得锁，这无疑会使CPU效率降低。
    2、在用自旋锁时有可能造成死锁，当递归调用时有可能造成死锁，调用有些其他函数也可能造成死锁，如 copy_to_user()、copy_from_user()、kmalloc()等。
因此我们要慎重使用自旋锁，自旋锁只有在内核可抢占式或SMP的情况下才真正需要，在单CPU且不可抢占式的内核下，自旋锁的操作为空操作。自旋锁适用于锁使用者保持锁时间比较短的情况下。
       自旋锁的用法如下：
   首先定义：spinlock_t x;
 然后初始化：spin_lock_init(spinlock_t *x);   //自旋锁在真正使用前必须先初始化
 在2.6.11内核中将定义和初始化合并为一个宏：DEFINE_SPINLOCK(x)
    
  获得自旋锁：spin_lock(x);   //只有在获得锁的情况下才返回，否则一直“自旋”
                           spin_trylock(x);  //如立即获得锁则返回真，否则立即返回假
      释放锁：spin_unlock(x);
    
结合以上有以下代码段：

    spinlock_t lock;        //定义一个自旋锁
    spin_lock_init(&lock);
    spin_lock(&lock);    
    .......        //临界区
    spin_unlock(&lock);   //释放锁
    
    还有一些其他用法：
spin_is_locked(x)
    //　　该宏用于判断自旋锁x是否已经被某执行单元保持（即被锁），如果是，   返回真，否则返回假。
spin_unlock_wait(x)
    //　　该宏用于等待自旋锁x变得没有被任何执行单元保持，如果没有任何执行单元保持该自旋锁，该宏立即返回，否
    //将循环    在那里，直到该自旋锁被保持者释放。

spin_lock_irqsave(lock, flags)
    //　　该宏获得自旋锁的同时把标志寄存器的值保存到变量flags中并失效本地中//断。相当于：spin_lock()+local_irq_save()
spin_unlock_irqrestore(lock, flags)
    //　　该宏释放自旋锁lock的同时，也恢复标志寄存器的值为变量flags保存的//值。它与spin_lock_irqsave配对使用。
    //相当于：spin_unlock()+local_irq_restore()

spin_lock_irq(lock)
　    //该宏类似于spin_lock_irqsave，只是该宏不保存标志寄存器的值。相当         //于：spin_lock()+local_irq_disable()
spin_unlock_irq(lock)
    //该宏释放自旋锁lock的同时，也使能本地中断。它与spin_lock_irq配对应用。相当于: spin_unlock()+local_irq+enable()

spin_lock_bh(lock)
    //　　该宏在得到自旋锁的同时失效本地软中断。相当于：  //spin_lock()+local_bh_disable()
spin_unlock_bh(lock)
      //该宏释放自旋锁lock的同时，也使能本地的软中断。它与spin_lock_bh配对//使用。相当于：spin_unlock()+local_bh_enable()

spin_trylock_irqsave(lock, flags)
    //该宏如果获得自旋锁lock，它也将保存标志寄存器的值到变量flags中，并且失//效本地中断，如果没有获得锁，它什么也不做。因此如果能够立即 获得锁，它等//同于spin_lock_irqsave，如果不能获得锁，它等同于spin_trylock。如果该宏//获得自旋锁lock，那需要 使用spin_unlock_irqrestore来释放。

spin_trylock_irq(lock)
    //该宏类似于spin_trylock_irqsave，只是该宏不保存标志寄存器。如果该宏获得自旋锁lock，需要使用spin_unlock_irq来释放。
spin_trylock_bh(lock)
    //　　该宏如果获得了自旋锁，它也将失效本地软中断。如果得不到锁，它什么//也不做。因此，如果得到了锁，它等同于spin_lock_bh，如果得 不到锁，它等同//于spin_trylock。如果该宏得到了自旋锁，需要使用spin_unlock_bh来释放。
spin_can_lock(lock)
    //　　该宏用于判断自旋锁lock是否能够被锁，它实际是spin_is_locked取反。//如果lock没有被锁，它返回真，否则，返回 假。该宏在2.6.11中第一次被定义，在//先前的内核中并没有该宏。
    
参考:    http://soft.yesky.com/os/lin/10/2303010_4.shtml)
        https://blog.csdn.net/kyokowl/article/details/6294341)
        https://blog.csdn.net/sunmenggmail/article/details/8105279)
