# 原型模式 (Prototype Pattern)

> 作者：张权
>
> 创建日期：2018-04-21 14:00:59

---

### 0. 前言

有些时候，我们需要创建多个类似的大对象，如果每次去new，初始化开销很大，这个时候我们先new一个模版对象，然后其它实例都去clone这个模版，这样可以节约不少性能。这个模版就是原型（Prototype）,原型模式比单纯的clone要稍微升级一下。

### 1. 模式定义

原型模式 (Prototype Pattern)：它属于对象**创建型模式**。用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。

### 2. 模式结构

原型模式包含如下角色：

+ ``` Prototype ```：**抽象原型角色**
    + 声明一个克隆自身的接口。

+ ``` ConcretePrototype ```：**具体原型角色**
    + 实现一个克隆自身的操作。

![PrototypePattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/PrototypePattern-0.png)

### 3. 浅复制和深复制

用示例说明一下PHP中浅复制和深复制

``` php
/**
 * 员工类
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-04-21 14:14:48
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Person
{
    /**
     * @var string
     */
    public $name;

    /**
     * @var Account
     */
    public $account;

    public function __construct(string $name, Account $account)
    {
        $this->name = $name;
        $this->account = $account;
    }

    public function __clone()
    {

    }
}



/**
 * 账户类
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-04-21 14:22:04
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Account
{
    /**
     * @var float
     */
    public $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}



$p1 = new Person('小明', new Account(10000.00));
$p2 = clone $p1;

$p1->account->balance = 20000.00;
$p1->name = '小王';

echo $p1->name.PHP_EOL; //小王
echo $p1->account->balance.PHP_EOL; //20000

echo $p2->name.PHP_EOL; //小明
echo $p2->account->balance.PHP_EOL; //20000

```

+ 示例结果可以看出``` $p2 ```对象是``` $p1 ```clone后的一个副本，修改了``` $p1 ```对象``` name ```值后``` $p2 ```的``` name ```值并未改变（是我们所期望的），但``` $p1 ```中的``` account ```属性是一个指向``` Account ```对象的引用，clone后``` $p2 ```和原来的``` $p1 ```的``` account ```还是指向同一个对象（这显然不是我们所期望的），这就是**浅复制**

改造``` Person ```类后的**深复制**

``` php
/**
 * 员工类
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-04-21 14:14:48
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Person
{
    /**
     * @var string
     */
    public $name;

    /**
     * @var Account
     */
    public $account;

    public function __construct(string $name, Account $account)
    {
        $this->name = $name;
        $this->account = $account;
    }

    public function __clone()
    {
        $this->account = clone $this->account;
    }
}

$p1 = new Person('小明', new Account(10000.00));
$p2 = clone $p1;

$p1->account->balance = 20000.00;
$p1->name = '小王';

echo $p1->name.PHP_EOL; //小王
echo $p1->account->balance.PHP_EOL; //20000

echo $p2->name.PHP_EOL; //小明
echo $p2->account->balance.PHP_EOL; //10000
```

### 4. 基于深复制的原型模式示例

我们现在正开发一个游戏，有不同的地图，地图大小都是一样的，并且都有海洋，但是不同的地图温度不一样。

* MapPrototype：抽象原型类

``` php
/**
 * 抽象地图原型类，地图有长、宽、海洋
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-04-21 13:45:27
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class MapPrototype
{
    /**
     * @var int
     */
    public $width;

    /**
     * @var int
     */
    public $height;

    /**
     * @var Sea
     */
    public $sea;

    /**
     * @param array $attributes
     */
    public function setAttribute(array $attributes)
    {
        foreach ($attributes as $key=>$val) {
            $this->$key = $val;
        }
    }

    /**
     * @return void
     */
    abstract public function __clone();
}
```

* Map：具体原型类

``` php
/**
 * 具体原型类
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-04-21 13:50:40
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Map extends MapPrototype
{
    public function __clone()
    {
        $this->sea  = clone $this->sea;
    }
}
```

* Sea：海洋类

``` php
/**
 * 海洋类
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-04-21 13:52:16
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Sea
{
    public $color = '蓝色';
}
```


### 5. 客户端的使用

```php
//先创建一个原型对象
$mapPrototype = new Map;
$mapPrototype->setAttribute(array('width'=>200, 'height'=>100, 'sea'=>(new Sea)));

//原型对象已经有了（模版已经有了），如果我们需要一个新的map对象只需要克隆一下模版就行
$newMap1 = clone $mapPrototype;
$newMap1->sea->color = '深蓝'; //给第一张地图的海洋换个颜色
echo $newMap1->sea->color.PHP_EOL; //深蓝

//需要第二张地图只需再克隆一下模版
$newMap2 = clone $mapPrototype;
echo $newMap2->sea->color.PHP_EOL; //蓝色，可以看出修改地图1海洋颜色并不影响地图2的
```

* 我们可以发现利用原型模式，只需要实例并初始化一个地图原型对象。以后如果需要生产一个新的地图对象，都可以直接通过clone原型对象产生。省去了重新初始化的过程。

### 6. UML类图

原型模式实现的地图示例代码UML图：

![PrototypePattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/PrototypePattern-1.png)

### 7. 总结

+ 原型模式是创建型模式的一种，其特点在于通过**复制**一个已经存在的实例来返回新的实例,而不是新建实例。被复制的实例就是我们所称的**原型**，这个原型是可定制的。
+ 原型模式多用于创建复杂的或者耗时的实例，因为这种情况下，复制一个已经存在的实例使程序运行更高效；或者创建值相等，只是命名不一样的同类数据。