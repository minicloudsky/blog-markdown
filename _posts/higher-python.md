---
title: Python 高级编程笔记
date: 2020-04-03 15:16:24
tags:
- python
---

1. 模块间循环导入，A导入B，B导入A

2. is 和 == 区别

   is比较内存地址，== 比较值

   ```python
   In [34]: a=1                                                                      
   In [35]: b=1                                                                      
   In [36]: a==b                                                                     
   Out[36]: True
   
   In [37]: a is b                                                                   
   Out[37]: True
   
   In [38]: list = [1,2,3]                                                           
   
   In [39]: list2 = [1,2,3]                                                          
   In [40]: list is list2                                                            
   Out[40]: False
   
   In [41]: list==list2                                                              
   Out[41]: True
   In [42]: id(list)                                                                 
   Out[42]: 140116611518152
   
   In [43]: id(list2)                                                                
   Out[43]: 140116636517064
   ```

3. 深拷贝、浅拷贝

   浅拷贝内存共享，变量修改后浅拷贝的变量都跟着改变，深拷贝会新建内存区把数据复制进去，和原来变量是独立的

   ```python
   In [10]: a = [1,2,3]                                                              
   
   In [11]: b= a                                                                     
   
   In [12]: id(a)                                                                    
   Out[12]: 139967263031624
   
   In [13]: id(b)                                                                    
   Out[13]: 139967263031624 浅拷贝内存地址相同
   
   In [14]: import copy                                                              
   
   In [15]: c = copy.deepcopy(a)                                                     
   
   In [16]: id(a)                                                                    
   Out[16]: 139967263031624
   
   In [17]: id(c)                                                                    
   Out[17]: 139967348141896   深拷贝内存地址不同
   
   In [18]: a.append(4)                                                              
   
   In [19]: a                                                                        
   Out[19]: [1, 2, 3, 4]
   
   In [20]: c                                                                        
   Out[20]: [1, 2, 3]   深拷贝的值未变，因为在新的内存区
   
   In [21]: b                                                                        
   Out[21]: [1, 2, 3, 4]
   ```

   深拷贝时候，如果遇到内存地址，会进行递归拷贝。

   ```python
   In [23]: a = [1,2,3]                                                              
   
   In [24]: b = [4,5,6]                                                              
   
   In [25]: c  = [a,b]    c里面存储a、b的引用                                                           
   
   In [26]: d=c                                                                      
   
   In [27]: id(c)                                                                    
   Out[27]: 139967287746376
   
   In [28]: id(d)                                                                    
   Out[28]: 139967287746376
   
   In [29]: e = copy.deepcopy(c)      e 进行深拷贝                                           
   
   In [30]: id(e)                                                                    
   Out[30]: 139967264133832
   
   In [31]: e                                                                        
   Out[31]: [[1, 2, 3], [4, 5, 6]]
   
   In [32]: e[0]                                                                     
   Out[32]: [1, 2, 3]
   
   In [33]: a.append(5)                                                              
   
   In [34]: c                                                                        
   Out[34]: [[1, 2, 3, 5], [4, 5, 6]]
   
   In [35]: d                                                                        
   Out[35]: [[1, 2, 3, 5], [4, 5, 6]]
   
   In [36]: e                                                                        
   Out[36]: [[1, 2, 3], [4, 5, 6]]
   ```

   copy.copy 只复制第一层，不会递归复制数据

   ```python
   In [30]: a = [1,2,3]                                                              
   
   In [31]: b = [4,5,6]                                                              
   
   In [32]: c= [a,b]                                                                 
   
   In [33]: e = copy.copy(c)                                                         
   
   In [34]: a.append(7)                                                              
   
   In [35]: c                                                                        
   Out[35]: [[1, 2, 3, 7], [4, 5, 6]]
   
   In [36]: e                                                                        
   Out[36]: [[1, 2, 3, 7], [4, 5, 6]]
   
   In [37]: id(c)                                                                    
   Out[37]: 140219958952584
   
   In [38]: id(e)                                                                    
   Out[38]: 140219974150984
   ```

   copy.copy 复制不可变类型时候，直接是浅拷贝，将对应内存地址赋值过去，不会进行递归复制数据。

   ```python
   In [2]: a = [1,2,3]                                                               
   
   In [3]: b= [4,5,6]                                                                
   
   In [4]: c = [a,b]                                                                 
   
   In [5]: import copy                                                               
   
   In [6]: c = (a,b)                                                                 
   
   In [7]: e = copy.copy(c)                                                          
   
   In [8]: id(c)                                                                     
   Out[8]: 140327472307016
   
   In [9]: id(e)                                                                     
   Out[9]: 140327472307016
   ```

   

4. 原码、反码、补码

   正数： 原码 = 反码 = 补码

   负数:   反码 = 符号位不变，其他位数取反

   ​			补码 = 反码 +1

   进制转换

   ```python
   In [28]: bin(10)                                                                  
   Out[28]: '0b1010'
   
   In [29]: oct(10)                                                                  
   Out[29]: '0o12'
   
   In [30]: hex(10)                                                                  
   Out[30]: '0xa'
   In [32]: int('100',2)                                                             
   Out[32]: 4
   ```

5. 位运算

   -  & 按位与

   - | 按位或

   - ^  按位异或  不相同为1 ，相同为 0

   - ~ 按位取反

   - << 按位左移 >> 按位右移

   - 用途: 直接操作二进制数据，省内存，效率高

     ```python
     In [38]: 1<<10                                                                    
     Out[38]: 1024
     
     In [39]: 512>>6                                                                   
     Out[39]: 8
     
     In [40]: 100&99                                                                   
     Out[40]: 96
     
     In [41]: 0b1010101 & 0b0101101                                                    
     Out[41]: 5
     
     In [42]: 0b1011101 | 0b0101100                                                    
     Out[42]: 125
     In [44]: 0b1011101 ^  0b0101100                                                   
     Out[44]: 113
     In [49]: ~0b10101010111                                                           
     Out[49]: -1368
     ```

6. 私有化

   ```bash
   xx: 共有变量
   
   _x: 单前置下划线，私有化属性或方法，from somemodule import * 禁止导入，类对象和子类可以访问。
   
   __xx: 双前置下划线，避免与子类中的属性命名冲突，无法在外部直接访问（名字重整所以访问不到）
   
   __xx__: 双前后下划线，用户名字空间的魔法对象或属性。例如: __init__ ,__不要自己发明这样的名字
   xx_: 单后置下划线，用于避免与Python关键词的冲突
   ```

   ​	通过 name mangling （名字重整(目的就是防止子类意外重写基类的方法或属性) 如：_Class__object 机制就可以访问 private 了。

   私有属性添加 getter setter 方法

   ```python
   In [9]: class Money(object): 
      ...:     def __int__(self): 
      ...:         self.__money = 0 
      ...:     def getMoney(self): 
      ...:         return self.__money 
      ...:     def setMoney(self,value): 
      ...:         if isinstance(value,int): 
      ...:             self.__money = value 
      ...:         else: 
      ...:             print("error: 不是整型数字") 
      ...: 
   In [12]: m = Money()                                                              
   
   In [13]: m.setMoney(100)                                                          
   
   In [14]: m.getMoney()                                                             
   Out[14]: 100
   
   In [15]: m.__money                                                                
   ---------------------------------------------------------------------------
   AttributeError                            Traceback (most recent call last)
   <ipython-input-15-9159f1107225> in <module>
   ----> 1 m.__money
   
   AttributeError: 'Money' object has no attribute '__money'
   ```

   

7. property 属性

   1. property 属性可以用来给属性添加约束，比如温度属性，我们不允许低于 - 273 度；成绩属性，我们不允许 0 分以下等等。而且使用 property 属性，将来修改约束条件的时候也很方便，可以在代码的调用方式不变的情况下改变结果。

      python 中使用 property 属性有两种方法。使用 @property 装饰器和使用 property () 函数

      ```python
      In [25]: class Employee(object): 
          ...:     def __init__(self,salary): 
          ...:         self._salary = salary 
          ...:     def get_score(self): 
          ...:         print("getting score") 
          ...:         return self._salary 
          ...:     def set_score(self,salary): 
          ...:         print("setting score") 
          ...:         if not isinstance(salary,int): 
          ...:             raise ValueError("not a int value") 
          ...:         if salary<0 or salary>100000: 
          ...:             raise ValueError("salary value error") 
          ...:         self._salary= salary 
          ...:     def del_score(self): 
          ...:         print("deleting salary") 
          ...:         del self._score 
          ...:     salary = property(get_score,set_score,del_score,"-- salary --") 
          ...:                                                                          
      
      In [26]: e = Employee(100)                                                        
      
      In [27]: e.score                                                                  
      ---------------------------------------------------------------------------
      AttributeError                            Traceback (most recent call last)
      <ipython-input-27-2630057808b6> in <module>
      ----> 1 e.score
      
      AttributeError: 'Employee' object has no attribute 'score'
      
      getting score
      In [28]: e.salary                                                                 
      getting score
      Out[28]: 100
      
      getting score
      In [29]: e.salary = 10099                                                         
      setting score
      
      getting score
      In [30]: e.salary                                                                 
      getting score
      Out[30]: 10099
      
      getting score
      In [31]: del e.salary                                                             
      deleting salary
      ---------------------------------------------------------------------------
      AttributeError                            Traceback (most recent call last)
      <ipython-input-31-584b1f8ddf2c> in <module>
      ----> 1 del e.salary
      
      <ipython-input-25-1d9e8b7ec464> in del_score(self)
           14     def del_score(self):
           15         print("deleting salary")
      ---> 16         del self._score
           17     salary = property(get_score,set_score,del_score,"-- salary --")
           18 
      
      AttributeError: _score
      ```

      @property 装饰器方式实现

      python 源码中的介绍如下

      ```python
      property(fget=None, fset=None, fdel=None, doc=None) -> property attribute
      
      fget is a function to be used for getting an attribute value, and likewise
      fset is a function for setting, and fdel a function for del'ing, an
      attribute.  Typical use is to define a managed attribute x:
      
      class C(object):
          def getx(self): return self._x
          def setx(self, value): self._x = value
          def delx(self): del self._x
          x = property(getx, setx, delx, "I'm the 'x' property.")
      
      Decorators make defining new properties or modifying existing ones easy:
      
      class C(object):
          @property
          def x(self):
              "I am the 'x' property."
              return self._x
          @x.setter
          def x(self, value):
              self._x = value
          @x.deleter
          def x(self):
              del self._x
      ```

      用 property、@num.setter 实现

      ```python
      In [38]: class Student(object): 
          ...:     def __init__(self): 
          ...:         self.__num = 100 
          ...:     @property 
          ...:     def num(self): 
          ...:         print("----getter-----") 
          ...:         return self.__num 
          ...:     @num.setter 
          ...:     def num(self,newNum): 
          ...:         print('----setter----') 
          ...:         self.__num = newNum 
          ...:                                                                          
      
      In [39]:                                                                          
      
      In [39]: t = Test()                                                               
      
      In [40]: t.num                                                                    
      -----getter---
      Out[40]: 100
      
      In [41]: t.num = 123                                                              
      -----setter----
      ```

8. 迭代器

   1. 可迭代对象

      以直接作用于 for 循环的数据类型有以下几种:

      1.  集合数据类型：list 、tuple、dict、set、str 等
      2.  generator，报考生成器 和带yield 的generator function

      这些可以直接作用于 for 循环的对象统称为可迭代对象: Iterable

   2. 判断是否可迭代

      用 isinstance() 判断是否是 Iterable 对象
   
      - 凡是可作用于 for 循环的对象都是 Iterable 类型；
      - 凡是可作用于 next() 函数的对象都是 Iterator 类型
      - 集合数据类型如 list 、 dict 、 str 等是 Iterable 但不是 Iterator ，不过可以通过 iter() 函数获得一个 Iterator 对象。
   
   3. 迭代器 Iterator
   
      可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。
   
      可以使用 isinstance() 判断一个对象是否是 Iterator 对象：
   
9.  闭包 外部函数的返回值是内部函数的引用

   ```python
   def line_conf(a,b):
   	def line(x):
           return a*x + b
       return line
   line1  = line_conf(1,1)
   line2 = line_conf(4,5)
   print(line1(5))
   print(line2(5))
   ```

   

   

10. 装饰器

    

11. __slots__

    动态语言：可以在运行的过程中，修改代码

    静态语言：编译时已经确定好代码，运行过程中不能修改

    如果我们想要限制实例的属性怎么办？比如，只允许对Person实例添加name和age属性。

    为了达到限制的目的，Python允许在定义class的时候，定义一个特殊的__slots__变量，来限制该class实例能添加的属性：

    ```python
    >>> class Person(object):
        __slots__ = ("name", "age")
    
    >>> P = Person()
    >>> P.name = "老王"
    >>> P.age = 20
    >>> P.score = 100
    Traceback (most recent call last):
      File "<pyshell#3>", line 1, in <module>
    AttributeError: Person instance has no attribute 'score'
    >>>
    ```

    #### 注意:

    - 使用__slots__要注意，__slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用的

    ```python
    In [67]: class Test(Person):
        ...:     pass
        ...:
    
    In [68]: t = Test()
    
    In [69]: t.score = 100
    ```









