# 装饰模式 (Decorator Pattern)

> 作者：张权
>
> 创建日期：2018-03-31 13:05:12

---

### 0、早餐店的故事

* 我们借助早餐店的故事来认识一下装饰模式。小张刚创业一个人开了一家早餐店提供煎饼、手抓饼出售，现在需要实现一个制作面饼的系统。

> * 实现方案

``` php
<?php
/**
 * 一个各种面饼的制作接口
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-30 13:55:28
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

interface Pie
{
    public function make();
}


/**
 * 煎饼套餐：加鸡蛋、香肠、番茄酱
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-30 14:16:40
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class JianBing implements Pie
{
    public function make()
    {
        echo "普通的煎饼";
        $this->attachEgg()
             ->attachSausage()
             ->attachKetchup();
    }

    private function attachEgg()
    {
        echo "+鸡蛋";
        return $this;
    }

    private function attachSausage()
    {
        echo "+香肠";
        return $this;
    }

    private function attachKetchup()
    {
        echo "+番茄酱";
        return $this;
    }
}

/**
 * 手抓饼套餐：加鸡蛋、油条、辣椒酱
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-30 13:58:45
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class ShouZhuaBing implements Pie
{
    public function make()
    {
        echo "普通的手抓饼";
        $this->attachEgg()
             ->attachCruller()
             ->attachChiliPaste();
    }

    private function attachEgg()
    {
        echo "+鸡蛋";
        return $this;
    }

    private function attachCruller()
    {
        echo "+油条";
        return  $this;
    }

    private function attachChiliPaste()
    {
        echo "+辣椒酱";
        return $this;
    }

}


/**
 * 早餐类，出售各种面饼
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-30 14:03:50
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Breakfast
{
    private $pie;

    public function __construct(Pie $pie)
    {
        $this->pie = $pie;
    }

    public function sale()
    {
        $this->pie->make();
    }
}


//Client 调用
$breakfastOne = new Breakfast(new JianBing());
$breakfastOne->sale(); //普通的煎饼+鸡蛋+香肠+番茄酱

$breakfastTwo = new Breakfast(new ShouZhuaBing());
$breakfastTwo->sale(); //普通的手抓饼+鸡蛋+油条+辣椒酱
```
---

+ 由于小张每天起早贪黑的努力工作生意越来越好，娶上了媳妇现在多了一个帮手，小张想要扩张一下出售面饼的各类，还想要出售鸡蛋灌饼、葱花鸡蛋饼等。原来的面饼系统就出现了一些问题
    + 每增加一种面饼种类就要增加一个该面饼的实现类。
    + 随着面饼种类越来越多类的数量也就越多，类的维护成本就很大。
    + 面饼的制作不够灵活，制作方法里硬编码了制作的配料。
    + 公用的配料制作方法没有得到复用，比如鸡蛋配料方法。

+ 问：那小张该如何做？
    + 答：请看下面的**装饰模式**

### 1. 模式定义

装饰模式 (Decorator Pattern)：它属于对象**结构型模式**。动态地给一个对象添加一些额外的职责，就增加功能来说，装饰模式比生成子类更加灵活。

### 2. 模式结构

装饰模式包含如下角色：

+ ``` Component ```：抽象构件类
    + 定义一个对象接口，可以给这些对象动态地添加职责（方法）。

+ ``` ConcreteComponent ```：具体构件类
    + 定义了具体的构件对象，实现了在抽象构件中声明的方法，装饰器可以给它增加额外的职责（方法）。

+ ``` Decorator ```：抽象装饰类
    + 抽象装饰类是抽象构件类的子类，用于给具体构件增加职责，但是具体职责在其子类中实现。

+ ``` ConcreteDecorator ```：具体装饰类
    + 具体装饰类是抽象装饰类的子类，负责向构件中添加新的职责。

![DecoratorPattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/DecoratorPattern-0.png)

### 3. 装饰模式实现的面饼系统代码示例

* Grain：抽象构件类

``` php
/**
 * 一个制作杂粮的接口
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 12:14:38
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

interface Grain
{
    public function make();
}
```

* JianBing、ShouZhuaBing：具体构件类

``` php
/**
 * 煎饼类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 12:14:38
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class JianBing implements Grain
{
    public function make()
    {
        echo "普通的煎饼";
    }
}

/**
 * 手抓饼类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 12:14:38
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class ShouZhuaBing implements Grain
{
    public function make()
    {
        echo "普通的手抓饼";
    }
}
```

* GrainDecorator：抽象装饰类

``` php
/**
 * 面饼装饰抽象类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 13:14:42
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class GrainDecorator implements Grain
{
    protected $grain;

    public function __construct(Grain $grain)
    {
        $this->grain = $grain;
    }

    public function make()
    {
        $this->grain->make();
    }
}
```

* Egg、Sausage、Ketchup、Cruller、ChiliPaste：具体装饰类

``` php
**
 * 鸡蛋装饰类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 13:14:42
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Egg extends GrainDecorator
{
    public function __construct(Grain $grain)
    {
        parent::__construct($grain);
    }

    public function make()
    {
        parent::make();
        $this->attachEgg();
    }

    private function attachEgg()
    {
        echo "+鸡蛋";
    }
}

/**
 * 香肠装饰类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 13:15:54
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Sausage extends GrainDecorator
{
    public function __construct(Grain $grain)
    {
        parent::__construct($grain);
    }

    public function make()
    {
        parent::make();
        $this->attachSausage();
    }

    private function attachSausage()
    {
        echo "+香肠";
    }
}

/**
 * 番茄酱装饰类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 13:16:36
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Ketchup extends GrainDecorator
{
    public function __construct(Grain $grain)
    {
        parent::__construct($grain);
    }

    public function make()
    {
        parent::make();
        $this->attachKetchup();
    }

    private function attachKetchup()
    {
        echo "+番茄酱";
    }
}

/**
 * 油条装饰类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 13:16:55
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Cruller extends GrainDecorator
{
    public function __construct(Grain $grain)
    {
        parent::__construct($grain);
    }

    public function make()
    {
        parent::make();
        $this->attachCruller();
    }

    private function attachCruller()
    {
        echo "+油条";
    }
}


/**
 * 辣椒酱装饰类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 13:17:17
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class ChiliPaste extends GrainDecorator
{
    public function __construct(Grain $grain)
    {
        parent::__construct($grain);
    }

    public function make()
    {
        parent::make();
        $this->attachChiliPaste();
    }

    private function attachChiliPaste()
    {
        echo "+辣椒酱";
    }
}
```


### 4. 客户端的使用

```php
/**
 * 早餐类，出售各种面饼
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-31 13:22:19
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Breakfast
{
    private $grain;

    public function __construct(Grain $grain)
    {
        $this->grain = $grain;
    }

    public function sale()
    {
        $this->grain->make();
    }
}

//Client 调用
//制作一套煎饼
$pie = new Ketchup(new Sausage(new Egg(new JianBing())));
$breakfast = new Breakfast($pie);
$breakfast->sale(); //普通的煎饼+鸡蛋+香肠+番茄酱

//Client 调用
//制作一套手抓饼
$pie = new ChiliPaste(new Cruller(new Egg(new ShouZhuaBing())));
$breakfast = new Breakfast($pie);
$breakfast->sale(); //普通的手抓饼+鸡蛋+油条+辣椒酱
```

### 5. UML类图

装饰模式实现的面饼系统示例代码UML图：

![DecoratorPattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/DecoratorPattern-1.png)

现在的面饼系统就变得强大了许多，可以按照顾客的需求用各种配料来装饰面饼而且装饰的顺序也很灵活，各种配料也能得以**复用**（同种配料可以被不同种类面饼利用），系统也很容易维护。

### 6. 模式分析

* 与继承关系相比，关联关系的主要优势在于不会破坏类的封装性，而且继承是一种耦合度较大的静态关系，无法在程序运行时动态扩展。在软件开发阶段，关联关系虽然不会比继承关系减少编码量，但是到了软件维护阶段，由于关联关系使系统具有较好的松耦合性，因此使得系统更加容易维护。
* 装饰模式把每个要装饰的功能放在单独的类中，并让这个类包装它所要装饰的对象，因此，当需要执行特殊行为时，客户端代码就可以在运行时根据需要有选择地、按顺序地使用装饰功能包装对象。


### 7. 装饰模式的优点

* 装饰模式与继承关系的目的都是要扩展对象的功能，但是装饰模式可以提供比继承更多的灵活性。
* 通过使用不同的具体装饰类以及这些装饰类的排列组合，可以创造出很多不同行为的组合。可以使用多个具体装饰类来装饰同一对象，得到功能更为强大的对象。
* 具体构件类与具体装饰类可以独立变化，用户可以根据需要增加新的具体构件类和具体装饰类，在使用时再对其进行组合，原有代码无须改变，符合**开闭原则**。
* 有效地把类的核心职责和装饰功能区分开了。而且可以去除相关类中重复的装饰逻辑。

### 8. 装饰模式的缺点

* 使用装饰模式进行系统设计时将产生很多具体装饰类，将增加系统的复杂度，加大学习与理解的难度。
* 这种比继承更加灵活机动的特性，也同时意味着装饰模式比继承更加易于出错，排错也很困难，对于多次装饰的对象，调试时寻找错误可能需要逐级排查，较为烦琐。

### 9. 适用场景

以下情况可以使用装饰模式：

* 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。
* 需要动态地给一个对象增加功能，这些功能也可以动态地被撤销。
* 当不能采用继承的方式对系统进行扩充或者采用继承不利于系统扩展和维护时。不能采用继承的情况主要有两类：第一类是系统中存在大量独立的扩展，为支持每一种组合将产生大量的子类，使得子类数目呈爆炸性增长；第二类是因为类定义不能继承（如final类）。

### 10. 总结

* 一个装饰类的接口必须与被装饰类的接口保持相同，对于客户端来说无论是装饰之前的对象还是装饰之后的对象都可以一致对待。
* 尽量保持具体构件类```Component```作为一个“轻”类，也就是说不要把太多的逻辑和状态放在具体构件类中，可以通过装饰。
* 如果只有一个具体构件类而没有抽象构件类，那么抽象装饰类可以作为具体构件类的直接子类。
* 如果只有一个具体装饰类，那么就没有必要建立一个单独的```Decorator```，可以把```Decorator```和```ConcreteDecorator```的责任合并成一个类。