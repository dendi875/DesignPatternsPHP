# 迭代器模式 (Iterator Pattern)

> 作者：张权
>
> 创建日期：2018-11-28

---

### 0. 前言

20世纪80年代，那时我家有一台“古老的”电视机，牌子我忘了，只记得是台黑白电视机，没有遥控器，每次开关机或者换台都需要通过电视机上面的那些按钮来完成，我印象最深的是那个用来换台的按钮，需要亲自用手去旋转（还要使点劲才能拧动），每转一下就“啪”的响一声，如果没有收到任何电视频道就会出现一片让人眼花的雪花点。当然，电视机上面那两根可以前后左右移动，并能够变长变短的天线也是当年电视机的标志性部件之一，我记得小时候每次画电视机时一定要画那两根天线，要不总觉得不是电视机。随着科技的飞速发展，越来越高级的电视机相继出现，那种古老的电视机已经很少能够看到了。与那时的电视机相比，现今的电视机给我们带来的最大便利之一就是增加了电视机遥控器，我们在进行开机、关机、换台、改变音量等操作时都无须直接操作电视机，可以通过遥控器来间接实现。我们可以将电视机看成一个存储电视频道的集合对象，通过遥控器可以对电视机中的电视频道集合进行操作，如返回上一个频道、跳转到下一个频道或者跳转至指定的频道。遥控器为我们操作电视频道带来很大的方便，用户并不需要知道这些频道到底如何存储在电视机中。电视机遥控器和电视机示意图如图1所示：

![tv](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/tv.png)

图1 电视机遥控器与电视机示意图

在软件开发中，也存在大量类似电视机一样的类，它们可以存储多个成员对象（元素），这些类通常称为聚合类(Aggregate Classes)，对应的对象称为聚合对象。为了更加方便地操作这些聚合对象，同时可以很灵活地为聚合对象增加不同的遍历方法，我们也需要类似电视机遥控器一样的角色，可以访问一个聚合对象中的元素但又不需要暴露它的内部结构。

### 1. 模式概述

在软件开发中，我们经常需要使用聚合对象来存储一系列数据。聚合对象拥有两个职责：一是存储数据；二是遍历数据。从依赖性来看，前者是聚合对象的基本职责；而后者既是可变化的，又是可分离的。因此，可以将遍历数据的行为从聚合对象中分离出来，封装在一个被称之为“迭代器”的对象中，由迭代器来提供遍历聚合对象内部数据的行为，这将简化聚合对象的设计，更符合“单一职责原则”的要求。

> 迭代器模式 (Iterator Pattern)：提供一种方法来访问聚合对象，而不用暴露这个对象的内部表示，其别名为游标(Cursor)。

迭代器模式是一种**对象行为型模式**。

### 2. 迭代器模式结构

迭代器模式包含如下角色：

+ ``` Iterator ```：**抽象迭代器**
    + 它定义了访问和遍历元素的接口，声明了用于遍历数据元素的方法，例如：用于获取第一个元素的first()方法，用于访问下一个元素的next()方法，用于判断是否还有下一个元素的hasNext()方法，用于获取当前元素的currentItem()方法等，在具体迭代器中将实现这些方法。在PHP中已经内置了迭代器接口**Iterator**。

+ ``` ConcreteIterator ```：**具体迭代器**
    + 它实现了抽象迭代器接口，完成对聚合对象的遍历，同时在具体迭代器中通过游标来记录在聚合对象中所处的当前位置，在具体实现时，游标通常是一个表示位置的非负整数。

+ ``` Aggregate ```：**抽象聚合类**
    + 它用于存储和管理元素对象，声明一个createIterator()方法用于创建一个迭代器对象。在PHP中已经内置了聚合式迭代器接口**IteratorAggregate**。

+ ``` ConcreteAggregate ```：**具体聚合类**
    + 它实现了在抽象聚合类中声明的createIterator()方法，该方法返回一个与该具体聚合类对应的具体迭代器ConcreteIterator实例。

![Iterator-Pattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/Iterator-Pattern-0.jpg)

### 3. 迭代器模式应用实例

下面通过一个实例进一步学习和理解迭代器模式。

### 实例说明

我们可以考虑下面这样一个题目：
使对象可以像数组一样进行foreach循环，要求属性必须是私有。

### 实例代码

* UserIterator：``` 具体迭代器 ```

``` php
/**
 * 用户迭代器：具体迭代器
 *
 * @author     <dendi875@163.com>
 * @createDate DataTime
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class UserIterator implements \Iterator
{
    private $position = 0;

    private $usersCollection;

    public function __construct(UserCollection $usersCollection)
    {
        $this->usersCollection = $usersCollection;
    }

    public function current()
    {
        return $this->usersCollection->getUser($this->position);
    }

    public function key()
    {
        return $this->position;
    }

    public function next()
    {
        ++$this->position;
    }

    public function rewind()
    {
        $this->position = 0;
    }

    public function valid()
    {
        return !is_null($this->usersCollection->getUser($this->position));
    }
}
```

* UserCollection： ``` 具体聚合类 ```

``` php
/**
 * 用户数据类：具体聚合类
 *
 * @author     <dendi875@163.com>
 * @createDate DataTime
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class UserCollection implements \IteratorAggregate
{
    private $users = [];

    /**
     * 创建具体用户迭代器
     */
    public function getIterator()
    {
        return new UserIterator($this);
    }

    /**
     * 存储用户
     */
    public function addUser(object $user)
    {
        $this->users[] = $user;
    }

    /**
     * 获取用户
     */
    public function getUser(int $key): ?object
    {
        if (isset($this->users[$key])) {
            return $this->users[$key];
        }
        return null;
    }
}
```

* 客户端测试代码

``` php
$usersCollection = new UserCollection();

$usersCollection->addUser(
    new class {
        public $id = 1;
        public $name = '小A';
    }
);
$usersCollection->addUser(
    new class {
        public $id = 2;
        public $name = '小B';
    }
);
$usersCollection->addUser(
    new class {
        public $id = 3;
        public $name = '小C';
    }
);

foreach ($usersCollection as $user) {
    echo "id：{$user->id}－name：{$user->name}<br/>";
}

以上例程会输出：

id：1－name：小A<br/>
id：2－name：小B<br/>
id：3－name：小C<br/>
```
$usersCollection对象的``` $users ```属性是私有的，但最后能通过foreach进行遍历出来。

### 4. UML类图

示例代码UML图：

![Iterator-Pattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/Iterator-Pattern-1.png)

### 5. 迭代器模式总结

* 在迭代器模式结构图中，我们可以看到具体迭代器类和具体聚合类之间存在双重关系，其中一个关系为关联关系，在具体迭代器中需要维持一个对具体聚合对象的引用，该关联关系的目的是访问存储在聚合对象中的数据，以便迭代器能够对这些数据进行遍历操作。

* 迭代器模式是一种使用频率非常高的设计模式，通过引入迭代器可以将数据的遍历功能从聚合对象中分离出来，聚合对象只负责存储数据，而遍历数据由迭代器来完成。

### 6. 迭代器模式的优点

迭代器模式的主要优点如下：

(1)迭代器简化了聚合类。由于引入了迭代器，在原有的聚合对象中不需要再自行提供数据遍历等方法，这样可以简化聚合类的设计。

(2)在迭代器模式中，由于引入了抽象层，增加新的聚合类和迭代器类都很方便，无须修改原有代码，满足“开闭原则”的要求。

### 7. 迭代器模式的缺点

迭代器模式的主要缺点如下：

由于迭代器模式将存储数据和遍历数据的职责分离，增加新的聚合类需要对应增加新的迭代器类，类的个数成对增加，这在一定程度上增加了系统的复杂性。

### 8.迭代器模式适用场景

在以下情况下可以考虑使用迭代器模式：

(1)访问一个聚合对象的内容而无须暴露它的内部表示。将聚合对象的访问与内部数据的存储分离，使得访问聚合对象时无须了解其内部实现细节。

(2)为遍历不同的聚合结构提供一个统一的接口，在该接口的实现类中为不同的聚合结构提供不同的遍历方式，而客户端可以一致性地操作该接口。

### 9. 参考资料

- [PHP: Iterator](http://php.net/manual/zh/class.iterator.php)
- [PHP: IteratorAggregate](http://php.net/manual/zh/class.iteratoraggregate.php)