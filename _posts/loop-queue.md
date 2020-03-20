---
title: 循环队列 Java实现
date: 2020-03-11 00:03:19
tags:
- 数据结构
---

一般的队列通常用数组实现，队头出队，队尾入队，入队在数组头部插入元素，然后把头部空出来插进去，头部以后的元素都需要向后移动，时间复杂度是O(n),出队删除队尾部元素，时间复杂度是O(1)。

循环队列其实是在普通队列基础上加了指向头部和尾部的指针，入队时候，尾指针+1，因为可能出现头部以前有空位，需要对队列的长度求余，即`tail=(tail+1)/array.length`,队列长度+1，  出队时候,头指针向后移动一个位置，同理，头指针也需要对队列长度求余，即front=(front+1)/array.length,队列长度-1，为了节省空间，一般会对循环队列的数组进行动态扩容和缩容，其实就是新建个数组，把原来数组的引用指向新的数组就好了，具体实现如下。

#### 队列的抽象定义

```java
public interface Queue<E> {
    int getSize();

    boolean isEmpty();

    void enqueue(E e);

    E dequeue();

    E getFront();
}

```

#### 循环队列实现队列的接口的方式实现循环队列

```java
public class LoopQueue<E> implements Queue<E> {
    private E[] data;
    private int front, tail; // 指向队首和队尾部
    private int size;

    public LoopQueue(int capacity) {
        data = (E[]) new Object[capacity + 1];
        front = 0;
        tail = 0;
        size = 0;
    }
	// 默认分配10个空间的数组
    public LoopQueue() {
        this(10);
    }

    public int getCapacity() {
        return data.length - 1;
    }

    @Override
    public boolean isEmpty() {
        return front == tail;
    }

    @Override
    public int getSize() {
        return size;
    }

    @Override
    public void enqueue(E e) {
        if ((tail + 1) % data.length == front) {
            resize(getCapacity() * 2);
        }
        data[tail] = e;
        tail = (tail + 1) % data.length;
        size++;
    }

    /**
     * 出队
     *
     * @return
     */
    @Override
    public E dequeue() {
        if (isEmpty()) {
            throw new IllegalArgumentException("Can not dequeue from a empty queue .");
        }
        E temp = data[front];
        data[front] = null;
        front = (front + 1) % data.length;
        size--;
        // 出队后进行缩容
        if (size == getCapacity() / 4 && getCapacity() / 2 != 0) {
            resize(getCapacity() / 2);
        }
        return temp;
    }

    /**
     * 获取队首
     *
     * @return
     */
    @Override
    public E getFront() {
        if (isEmpty()) {
            throw new IllegalArgumentException("Can not dequeue from a empty queue .");
        }
        return data[front];
    }

    /**
     * 扩容数组
     *
     * @param newCapacity
     */
    private void resize(int newCapacity) {
        E[] newData = (E[]) new Object[newCapacity + 1];
        for (int i = 0; i < size; i++) {
            newData[i] = data[(i + front) % data.length]; //这里注意，旧数组的队首是front,第i个元素就是 i+front
        }
        data = newData;
        front = 0;
        tail = size;
    }

    @Override
    public String toString() {
        StringBuilder res = new StringBuilder();
        res.append(String.format("LoopQueue : size = %d , capacity = %d \n",
                getSize(), getCapacity()));
        res.append("front [");
        for (int i = front; i != tail; i = (i + 1) % data.length) {
            res.append(data[i]);
            if ((i + 1) % data.length != tail) {
                res.append(", ");
            }
        }
        res.append(" ] tail");
        return res.toString();
    }

    public static void main(String[] args) {
        LoopQueue<Integer> queue = new LoopQueue<>();
        for (int i = 0; i < 10; i++) {
            queue.enqueue(i);
            System.out.println(queue);
            if (i % 3 == 2) {
                queue.dequeue();
                System.out.println(queue);
            }
        }

    }
}
```

#### 测试下结果

```bash
LoopQueue : size = 1 , capacity = 10 
front [0 ] tail
LoopQueue : size = 2 , capacity = 10 
front [0, 1 ] tail
LoopQueue : size = 3 , capacity = 10 
front [0, 1, 2 ] tail
LoopQueue : size = 2 , capacity = 5 
front [1, 2 ] tail
LoopQueue : size = 3 , capacity = 5 
front [1, 2, 3 ] tail
LoopQueue : size = 4 , capacity = 5 
front [1, 2, 3, 4 ] tail
LoopQueue : size = 5 , capacity = 5 
front [1, 2, 3, 4, 5 ] tail
LoopQueue : size = 4 , capacity = 5 
front [2, 3, 4, 5 ] tail
LoopQueue : size = 5 , capacity = 5 
front [2, 3, 4, 5, 6 ] tail
LoopQueue : size = 6 , capacity = 10 
front [2, 3, 4, 5, 6, 7 ] tail
LoopQueue : size = 7 , capacity = 10 
front [2, 3, 4, 5, 6, 7, 8 ] tail
LoopQueue : size = 6 , capacity = 10 
front [3, 4, 5, 6, 7, 8 ] tail
LoopQueue : size = 7 , capacity = 10 
front [3, 4, 5, 6, 7, 8, 9 ] tail

Process finished with exit code 0

```

