# 观察者模式 (Observer Pattern)

> 作者：张权
>
> 创建日期：2018-05-26

---

### 0. 观察者模式概述

“红灯停，绿灯行”，在日常生活中，交通信号灯装点着我们的城市，指挥着日益拥挤的城市交通。当红灯亮起，来往的汽车将停止；而绿灯亮起，汽车可以继续前行。在这个过程中，交通信号灯是汽车（更准确地说应该是汽车驾驶员）的观察目标，而汽车是观察者。随着交通信号灯的变化，汽车的行为也将随之而变化，一盏交通信号灯可以指挥多辆汽车。

在软件系统中，有些对象之间也存在类似交通信号灯和汽车之间的关系，一个对象的状态或行为的变化将导致其他对象的状态或行为也发生改变，它们之间将产生联动，正所谓“触一而牵百发”。为了更好地描述对象之间存在的这种一对多（包括一对一）的联动，观察者模式应运而生，它定义了对象之间一种一对多的依赖关系，让一个对象的改变能够影响其他对象。


### 1. 模式定义

> 观察者模式 (Observer Pattern)：定义对象之间的一种一对多依赖关系，使得每当一个对象状态发生改变时，其相关依赖对象皆得到通知并被自动更新。

观察者模式是一种**对象行为型模式**。

观察者模式的别名包括：

+ 发布-订阅（Publish/Subscribe）模式
+ 模型-视图（Model/View）模式
+ 源-监听器（Source/Listener）模式
+ 从属者（Dependents）模式


该模式建立一种对象与对象之间的依赖关系，一个对象发生改变时将自动通知其他对象，其他对象将相应作出反应。在观察者模式中，发生改变的对象称为观察目标，而被通知的对象称为观察者，一个观察目标可以对应多个观察者，而且这些观察者之间可以没有任何相互联系，可以根据需要增加和删除观察者，使得系统更易于扩展。

### 2. 模式结构

观察者模式包含如下角色：

+ ``` Subject ```：**目标**
    + 目标又称为主题，它是指被观察的对象。在目标中定义了一个观察者集合，一个观察目标可以接受任意数量的观察者来观察，它提供一系列方法来增加和删除观察者对象，同时它定义了通知方法notify()。目标类可以是接口，也可以是抽象类或具体类。

+ ``` ConcreteSubject ```：**具体目标**
    + 具体目标是目标类的子类，通常它包含有经常发生改变的数据，当它的状态发生改变时，向它的各个观察者发出通知；同时它还实现了在目标类中定义的抽象业务逻辑方法（如果有的话）。

+ ``` Observer ```：**观察者**
    + 观察者将对观察目标的改变做出反应，观察者一般定义为接口，该接口声明了更新数据的方法update()，因此又称为抽象观察者。

+ ``` ConcreteObserver ```：**具体观察者**
    + 在具体观察者中维护一个指向具体目标对象的引用，它存储具体观察者的有关状态，这些状态需要和具体目标的状态保持一致；它实现了在抽象观察者Observer中定义的update()方法。通常在实现时，可以调用具体目标类的attach()方法将自己添加到目标类的集合中或通过detach()方法将自己从目标类的集合中删除。

![ObserverPattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/ObserverPattern-0.png)

### 3. 模式实现

* 观察者模式描述了如何建立对象与对象之间的依赖关系，以及如何构造满足这种需求的系统。观察者模式包含观察目标和观察者两类对象，一个目标可以有任意数目的与之相依赖的观察者，一旦观察目标的状态发生改变，所有的观察者都将得到通知。作为对这个通知的响应，每个观察者都将监视观察目标的状态以使其状态与目标状态同步，这种交互也称为发布-订阅(Publish-Subscribe)。观察目标是通知的发布者，它发出通知时并不需要知道谁是它的观察者，可以有任意数目的观察者订阅它并接收通知。

* 抽象观察者角色一般定义为一个接口，通常只声明一个update()方法，为不同观察者的更新（响应）行为定义相同的接口，这个方法在其子类中实现，不同的观察者具有不同的响应方法。

### 4. 观察者模式应用实例

下面通过一个实例进一步学习和理解观察者模式。

### 实例说明

现在需要开发一款多人联机对战游戏（比如LOL），在该游戏中，多个玩家可以加入同一战队组成联盟，当战队中某一成员受到敌人攻击时将给所有其他盟友发送通知，盟友收到通知后将作出响应。

由于联盟成员在受到攻击时需要通知他的每一个盟友，因此每个联盟成员都需要持有其他所有盟友的信息，这将导致系统开销较大，因此决定引入一个新的角色——“战队控制中心”——来负责维护和管理每个战队所有成员的信息。当一个联盟成员受到攻击时，将向相应的战队控制中心发送求助信息，战队控制中心再逐一通知每个盟友，盟友再作出响应。

受攻击的联盟成员将与战队控制中心产生联动，战队控制中心还将与其他盟友产生联动。如何实现对象之间的联动？如何让一个对象的状态或行为改变时，依赖于它的对象能够得到通知并进行相应的处理？

观察者模式将为对象之间的联动提供一个优秀的解决方案。

### 实例代码

* AbstractAllyControlCenter: ``` 抽象目标类 ```

``` php
/**
 * 抽象战队控制中心类：抽象目标类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class AbstractAllyControlCenter
{
    /**
     * 战队名称
     *
     * @var string
     */
    protected $allyName;

   /**
    *用于存储战队成员（相当于观察者集合它用于存储所有观察者对象）
    *
    * @var \SplObjectStorage
    */
    protected $players;

    public function setAllyName(string $allyName): void
    {
        $this->allyName = $allyName;
    }

    public function getAllyName(): string
    {
        return $this->allyName;
    }

    /* 注册方法，用于向观察者集合中增加一个观察者 */
    abstract public function attach(Observer $observer);

    /* 注销方法，用于在观察者集合中删除一个观察者 */
    abstract public function detach(Observer $observer);

    /* 抽象通知方法 */
    abstract public function notify(string $name);
}
```

* ConcreteAllyControlCenter: ``` 具体目标类 ```

``` php
/**
 * 具体战队控制中心类：具体目标类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class ConcreteAllyControlCenter extends AbstractAllyControlCenter
{
    public function __construct(string $allyName)
    {
        echo "{$allyName}战队组建成功！<br>";
        echo "-------------------------<br>";

        $this->allyName = $allyName;
        $this->players = new \SplObjectStorage();
    }

    /**
     * 实现注册方法，用于向观察者集合中增加一个观察者
     *
     * @param  Observer $observer
     *
     * @return void
     */
    public function attach(Observer $observer): void
    {
        echo $observer->getName()."加入".$this->allyName."战队！<br>";

        $this->players->attach($observer);
    }

    /**
     * 实现注销方法，用于在观察者集合中删除一个观察者
     *
     * @param  Observer $observer
     *
     * @return void
     */
    public function detach(Observer $observer): void
    {
        echo $observer->getName()."退出".$this->allyName."战队！<br>";

        $this->players->detach($observer);
    }

    /**
     * 实现通知方法
     *
     * @param  string $name 战队成员名称
     *
     * @return void
     */
    public function notify(string $name): void
    {
        echo $this->allyName."战队紧急通知，盟友".$name."遭受敌人攻击！<br>";

        //遍历观察者集合，调用每一个盟友（自己除外）的支援方法
        foreach ($this->players as $player) {
            if ($player->getName() != $name) {
                $player->help();
            }
        }
    }
}
```

* Observer: ``` 抽象观察者类 ```

```php
/**
 * 抽象战队成员类：抽象观察者类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class Observer
{
    /**
     * 战队成员名称
     *
     * @var string
     */
    protected $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }

    public function setName(string $name): void
    {
        $this->name = $name;
    }

    public function getName(): string
    {
        return $this->name;
    }

    /**
     * 支援盟友方法
     *
     * @return void
     */
    public function help(): void
    {
        echo "坚持住，".$this->name."来救你！<br>";
    }

    /**
     * 声明遭受攻击方法
     *
     * +---------------------------------------------------------------------
     * |为不同观察者的更新（响应）行为定义相同的接口，这个方法在其子类中实现，
     * |不同的观察者具有不同的响应方法。
     * +---------------------------------------------------------------------
     */
    abstract public function update(AbstractAllyControlCenter $subject);
}
```

* Player: ``` 具体观察者类 ```

```php
/**
 * 具体战队成员类：具体观察者类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Player extends Observer
{
    /**
     *遭受攻击方法的实现，当遭受攻击时将调用战队控制中心类的通知方法notify()来通知盟友
     *
     * @param AbstractAllyControlCenter $subject
     *
     * @return void
     */
    public function update(AbstractAllyControlCenter $subject): void
    {
        echo '<b>'.$this->name."被攻击！</b><br>";
        $subject->notify($this->name);
    }
}
```

* 客户端测试代码

```php
/* 实例观察目标对象 */
$acc = new ConcreteAllyControlCenter("金庸群侠");

/* 实例四个观察者 */
$player1 = new Player("杨过");
$player2 = new Player("令狐冲");
$player3 = new Player("张无忌");
$player4 = new Player("段誉");

/* 把观察者加入到观察者集合中 */
$acc->attach($player1);
$acc->attach($player2);
$acc->attach($player3);
$acc->attach($player4);

/* 某成员遭受攻击 */
$player1->update($acc);
```

以上例程会输出：

金庸群侠战队组建成功！<br>
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-<br>
杨过加入金庸群侠战队！<br>
令狐冲加入金庸群侠战队！<br>
张无忌加入金庸群侠战队！<br>
段誉加入金庸群侠战队！<br>
**杨过被攻击！**<br>
金庸群侠战队紧急通知，盟友杨过遭受敌人攻击！<br>
坚持住，令狐冲来救你！<br>
坚持住，张无忌来救你！<br>
坚持住，段誉来救你！

### 5. UML类图

示例代码UML图：

![ObserverPattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/ObserverPattern-1.png)

### 6. 观察者模式的优点

观察者模式的主要优点如下：

(1)观察者模式可以实现表示层和数据逻辑层的分离，定义了稳定的消息更新传递机制，并抽象了更新接口，使得可以有各种各样不同的表示层充当具体观察者角色。

(2)观察者模式在观察目标和观察者之间建立一个抽象的耦合。观察目标只需要维持一个抽象观察者的集合，无须了解其具体观察者。由于观察目标和观察者没有紧密地耦合在一起，因此它们可以属于不同的抽象化层次。

(3)观察者模式支持广播通信，观察目标会向所有已注册的观察者对象发送通知，简化了一对多系统设计的难度。

(4)观察者模式满足“开闭原则”的要求，增加新的具体观察者无须修改原有系统代码，在具体观察者与观察目标之间不存在关联关系的情况下，增加新的观察目标也很方便。

### 7. 观察者模式的缺点

观察者模式的主要缺点如下：

(1)如果一个观察目标对象有很多直接和间接观察者，将所有的观察者都通知到会花费很多时间。

(2)如果在观察者和观察目标之间存在循环依赖，观察目标会触发它们之间进行循环调用，可能导致系统崩溃。

(3)观察者模式没有相应的机制让观察者知道所观察的目标对象是怎么发生变化的，而仅仅只是知道观察目标发生了变化。

### 8. 观察者模式适用场景

在以下情况下可以考虑使用观察者模式：

(1)一个抽象模型有两个方面，其中一个方面依赖于另一个方面，将这两个方面封装在独立的对象中使它们可以各自独立地改变和复用。

(2)一个对象的改变将导致一个或多个其他对象也发生改变，而并不知道具体有多少对象将发生改变，也不知道这些对象是谁。

(3)需要在系统中创建一个触发链，A对象的行为将影响B对象，B对象的行为将影响C对象……，可以使用观察者模式创建一种链式触发机制。

### 9. 参考资料

- [PHP: SplSubject](http://php.net/manual/zh/class.splsubject.php)
- [PHP: SplObserver](http://php.net/manual/zh/class.splobserver.php)