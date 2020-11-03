> 我们在操作数据库的时候，可能会由于并发问题而引起的数据的不一致性（数据冲突），所以需要使用锁保证数据的一致性

## 乐观锁

乐观锁，顾名思义就是很乐观，每次去拿数据都认为别人不会修改，所以不会上锁，但在提交更新的时候会判断在此期间有没有人更新过这个数据。

**乐观锁适合多读少写的情况，这样可以提高吞吐量。**

乐观锁需要自己实现，实现方式主要有两种：

1. 使用版本号（比较常用）

- 取出记录时，获取当前version

- 更新时，带上这个version

- 执行更新时， set version = newVersion where version = oldVersion

- 如果version不对，就更新失败

  示例Java代码

  ```java
  int id = 100;
  int version = 2;
  
  User u = new User();
  u.setId(id);
  u.setVersion(version);
  u.setXXX(xxx);
  
  if(userService.updateById(u)){
      System.out.println("Update successfully");
  }else{
      System.out.println("Update failed due to modified by others");
  }
  ```

  示例SQL原理

  ```sql
  update tbl_user set name = 'update',version = 3 where id = 100 and version = 2
  ```

2. 使用时间戳（timestamp）

   同样是在需要乐观锁控制的table中增加一个字段，名称无所谓，字段类型使用时间戳（timestamp）, 和上面的version类似，也是在更新提交的时候检查当前数据库中数据的时间戳和自己更新前取到的时间戳进行对比，如果一致则OK，否则就是版本冲突。

   

   Java JUC中的atomic包就是乐观锁的一种实现，AtomicInteger 通过CAS（Compare And Set）操作实现线程安全的自增。

## 悲观锁

与乐观锁相对应的就是悲观锁了。悲观锁就是在操作数据时，认为此操作会出现数据冲突，所以在进行每次操作时都要通过获取锁才能进行对相同数据的操作

### 共享锁(S）

又称读锁，简单讲就是多个事务对同一数据进行共享一把锁，都能访问到数据，但是只能读不能修改。

### 排他锁(X)

又称写锁，排他锁就是不能与其他锁并存，如一个事务获取了一个数据行的排他锁，其他事务就不能再获取该行的其他锁，只有获取排他锁的事务可以对数据进行读取和修改

## Mysql锁

### 行锁

MyISAM不支持事务，InnoDB支持事务。

行锁的劣势：开销大；加锁慢；会出现死锁
行锁的优势：锁的粒度小，发生锁冲突的概率低；处理并发的能力强
加锁的方式：自动加锁。对于UPDATE、DELETE和INSERT语句，InnoDB会自动给涉及数据集加排他锁；对于普通SELECT语句，InnoDB不会加任何锁；当然我们也可以显示的加锁：
共享锁：select * from tableName where ... + lock in share more
排他锁：select * from tableName where ... + for update

**行锁可以解决脏读数据一致性问题**

**InnoDB的行锁是针对索引加的锁，不是针对记录加的锁。并且该索引不能失效，否则都会从行锁升级为表锁**。

#### 优化：

1 尽可能让所有数据检索都通过索引来完成，避免无索引行或索引失效导致行锁升级为表锁。
2 尽可能避免间隙锁带来的性能下降，减少或使用合理的检索范围。
3 尽可能减少事务的粒度，比如控制事务大小，而从减少锁定资源量和时间长度，从而减少锁的竞争等，提高性能。
4 尽可能低级别事务隔离，隔离级别越高，并发的处理能力越低。

### 表锁

加锁的方式：自动加锁。查询操作（SELECT），会自动给涉及的所有表加读锁，更新操作（UPDATE、DELETE、INSERT），会自动给涉及的表加写锁。也可以显示加锁：

共享读锁：lock table tableName read;
独占写锁：lock table tableName write;
批量解锁：unlock tables;

#### 什么场景使用表锁：

第一种情况：**全表更新**。事务需要更新大部分或全部数据，且表又比较大。若使用行锁，会导致事务执行效率低，从而可能造成其他事务长时间锁等待和更多的锁冲突。

第二种情况：**多表查询**。事务涉及多个表，比较复杂的关联查询，很可能引起死锁，造成大量事务回滚。这种情况若能一次性锁定事务涉及的表，从而可以避免死锁、减少数据库因事务回滚带来的开销。

### 页锁

开销和加锁时间介于表锁和行锁之间；会出现死锁；锁定粒度介于表锁和行锁之间，并发处理能力一般。

### 锁算法

Next KeyLocks锁，同时锁住记录(数据)，并且锁住记录前面的Gap   

Gap锁，不锁记录，仅仅记录前面的Gap

Recordlock锁（锁数据，不锁Gap）

### QUESTION:

事务和锁机制是什么关系？ 开启事务就自动加锁了吗？

1、事务与锁是不同的。事务具有ACID（原子性、一致性、隔离性和持久性），锁是用于解决隔离性的一种机制。

2、事务的隔离级别通过锁的机制来实现。另外锁有不同的粒度，同时事务也是有不同的隔离级别的。

3、开启事务就自动加锁。

**参考：**

[MySQL 表锁和行锁机制](https://segmentfault.com/a/1190000012773157)

[对mysql乐观锁、悲观锁、共享锁、排它锁、行锁、表锁概念的理解](https://blog.csdn.net/puhaiyang/article/details/72284702)

[关于有了隔离级别为什么还要乐观锁](https://blog.csdn.net/qq_21294095/article/details/84888802)

[经典问题之乐观锁和悲观锁及使用场景](https://blog.csdn.net/u010739551/article/details/81184203)