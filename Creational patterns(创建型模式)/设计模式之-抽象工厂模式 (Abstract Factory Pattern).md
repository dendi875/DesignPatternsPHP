# 抽象工厂模式 (Abstract Factory Pattern)

> 作者：张权
>
> 创建日期：2018-05-26

---

### 0. 抽象工厂模式概述

在工厂方法模式中具体工厂负责生产具体的产品，每一个具体工厂对应一种具体产品，工厂方法具有唯一性，一般情况下，一个具体工厂中只有一个或者一组重载的工厂方法。但是有时候我们希望一个工厂可以提供多个产品对象，而不是单一的产品对象，如一个电器工厂，它可以生产电视机、电冰箱、空调等多种电器，而不是只生产某一种电器。为了更好地理解抽象工厂模式，我们先引入两个概念：

(1)产品等级结构：产品等级结构即产品的继承结构，如一个抽象类是电视机，其子类有海尔电视机、海信电视机、TCL电视机，则抽象电视机与具体品牌的电视机之间构成了一个产品等级结构，抽象电视机是父类，而具体品牌的电视机是其子类。

(2)产品族：在抽象工厂模式中，产品族是指由同一个工厂生产的，位于不同产品等级结构中的一组产品，如海尔电器工厂生产的海尔电视机、海尔电冰箱，海尔电视机位于电视机产品等级结构中，海尔电冰箱位于电冰箱产品等级结构中，海尔电视机、海尔电冰箱构成了一个产品族。

产品等级结构与产品族示意图如图1所示：

![1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/1.jpg)

图1 产品族与产品等级结构示意图

在图1中，不同颜色的多个正方形、圆形和椭圆形分别构成了三个不同的产品等级结构，而相同颜色的正方形、圆形和椭圆形构成了一个产品族，每一个形状对象都位于某个产品族，并属于某个产品等级结构。图1中一共有五个产品族，分属于三个不同的产品等级结构。我们只要指明一个产品所处的产品族以及它所属的等级结构，就可以唯一确定这个产品。

当系统所提供的工厂生产的具体产品并不是一个简单的对象，而是多个位于不同产品等级结构、属于不同类型的具体产品时就可以使用抽象工厂模式。抽象工厂模式是所有形式的工厂模式中最为抽象和最具一般性的一种形式。抽象工厂模式与工厂方法模式最大的区别在于，工厂方法模式针对的是一个产品等级结构，而抽象工厂模式需要面对多个产品等级结构，一个工厂等级结构可以负责多个不同产品等级结构中的产品对象的创建。当一个工厂等级结构可以创建出分属于不同产品等级结构的一个产品族中的所有对象时，抽象工厂模式比工厂方法模式更为简单、更有效率。抽象工厂模式示意图如图2所示：

![2](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/2.jpg)

图2 抽象工厂模式示意图


### 1. 模式定义

> 抽象工厂模式 (Abstract Factory Pattern)：提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。

抽象工厂模式是一种**对象创建型模式**。

* 抽象工厂模式为创建一组对象提供了一种解决方案。与工厂方法模式相比，抽象工厂模式中的具体工厂不只是创建一种产品，它负责创建一族产品。

* 在抽象工厂模式中，每一个具体工厂都提供了多个工厂方法用于产生多种不同类型的产品，这些产品构成了一个产品族。

### 2. 模式结构

抽象工厂模式包含如下角色：

+ ``` AbstractFactory ```：**抽象工厂**
    + 它声明了一组用于创建一族产品的方法，每一个方法对应一种产品。

+ ``` ConcreteFactory ```：**具体工厂**
    + 它实现了在抽象工厂中声明的创建产品的方法，生成一组具体产品，这些产品构成了一个产品族，每一个产品都位于某个产品等级结构中。

+ ``` AbstractProduct ```：**抽象产品**
    + 它为每种产品声明接口，在抽象产品中声明了产品所具有的业务方法。

+ ``` ConcreteProduct ```：**具体产品**
    + 它定义具体工厂生产的具体产品对象，实现抽象产品接口中声明的业务方法。

![AbstractFactoryPattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/AbstractFactoryPattern-0.jpg)

### 3. 抽象工厂模式应用实例

下面通过一个实例进一步学习和理解抽象工厂模式。

### 实例说明

我们就以生产家用电器的例子来学习下抽象工厂模式，现有多个工厂，每个工厂都生产自己品牌电视机、冰箱、洗衣机；客户端可以很灵活的选择某个工厂生产的一系列产品，也能方便的增加新的工厂。

### 实例代码

* HomeAppliancesFactory：``` 抽象工厂类 ```

``` php
/**
 * 家用电器工厂接口：抽象工厂
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class HomeAppliancesFactory
{
    abstract public function createTv();
    abstract public function createIceBox();
    abstract public function createWasher();
}
```

* HaierFactory、MideaFactory： ``` 具体工厂 ```

``` php
/**
 * Haier工厂：具体工厂
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class HaierFactory extends HomeAppliancesFactory
{
    public function createTv(): HaierTv
    {
        return new HaierTv();
    }

    public function createIceBox(): HaierIceBox
    {
        return new HaierIceBox();
    }

    public function createWasher(): HaierWasher
    {
        return new HaierWasher();
    }
}

/**
 * Midea工厂：具体工厂
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class MideaFactory extends HomeAppliancesFactory
{
    public function createTv(): MideaTv
    {
        return new MideaTv();
    }

    public function createIceBox(): MideaIceBox
    {
        return new MideaIceBox();
    }

    public function createWasher(): MideaWasher
    {
        return new MideaWasher();
    }
}
```

* Tv： ``` 抽象产品类 ```

``` php
/**
 * 电视机接口：抽象产品
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

interface Tv
{
    public function make();
}
```

* HaierTv、MideaTv： ``` 具体产品 ```

``` php
/**
 * Haier电视机类：具体产品
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class HaierTv
{
    public function make(): void
    {
        echo "Haier电视".PHP_EOL;
    }
}

/**
 * Midea电视机类：具体产品
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class MideaTv
{
    public function make(): void
    {
        echo "Midea电视".PHP_EOL;
    }
}
```

* IceBox： ``` 抽象产品 ```

``` php
/**
 * 冰箱接口：抽象产品
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

interface IceBox
{
    public function make();
}
```

* HaierIceBox、MideaIceBox： ``` 具体产品 ```

``` php
/**
 * Haier冰箱类：具体产品
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class HaierIceBox
{
    public function make(): void
    {
        echo "Haier冰箱".PHP_EOL;
    }
}

/**
 * Midea冰箱类：具体产品
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class MideaIceBox
{
    public function make(): void
    {
        echo "Midea冰箱".PHP_EOL;
    }
}
```

* Washer： ``` 抽象产品 ```

``` php
/**
 * 洗衣机接口：抽象产品
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

interface Washer
{
    public function make();
}
```

* HaierWasher、MideaWasher： ``` 具体产品 ```

``` php
/**
 * Haier洗衣机类：具体产品
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class HaierWasher
{
    public function make(): void
    {
        echo "Haier洗衣机".PHP_EOL;
    }
}

/**
 * Midea洗衣机类：具体产品
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-26
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class MideaWasher
{
    public function make(): void
    {
        echo "Midea洗衣机".PHP_EOL;
    }
}
```

* 客户端测试代码

``` php
//$factory = new HaierFactory();
$factory = new MideaFactory();

$tv = $factory->createTv();
$iceBox = $factory->createIceBox();
$washer = $factory->createWasher();

$tv->make();
$iceBox->make();
$washer->make();
```

以上例程会输出：


Midea电视<br>
Midea冰箱<br>
Midea洗衣机<br>

如果需要更换成Haier生产的电器则只需把 ``` new MideaFactory() ```变为``` new HaierFactory()  ```

### 4. UML类图

示例代码UML图：

![AbstractFactoryPattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/AbstractFactoryPattern-1.png)

### 5. 抽象工厂模式分析

在抽象工厂模式中，增加新的产品族很方便，但是增加新的产品等级结构很麻烦，抽象工厂模式的这种性质称为“开闭原则”的倾斜性。“开闭原则”要求系统对扩展开放，对修改封闭，通过扩展达到增强其功能的目的，对于涉及到多个产品族与多个产品等级结构的系统，其功能增强包括两方面：

(1)增加产品族：对于增加新的产品族，抽象工厂模式很好地支持了“开闭原则”，只需要增加具体产品并对应增加一个新的具体工厂，对已有代码无须做任何修改。

(2)增加新的产品等级结构：对于增加新的产品等级结构，需要修改所有的工厂角色，包括抽象工厂类，在所有的工厂类中都需要增加生产新产品的方法，违背了“开闭原则”。

正因为抽象工厂模式存在“开闭原则”的倾斜性，它以一种倾斜的方式来满足“开闭原则”，为增加新产品族提供方便，但不能为增加新产品结构提供这样的方便，因此要求设计人员在设计之初就能够全面考虑，不会在设计完成之后向系统中增加新的产品等级结构，也不会删除已有的产品等级结构，否则将会导致系统出现较大的修改，为后续维护工作带来诸多麻烦。

### 6. 抽象工厂模式的优点

抽象工厂模式的主要优点如下：

(1)抽象工厂模式隔离了具体类的生成，使得客户并不需要知道什么被创建。由于这种隔离，更换一个具体工厂就变得相对容易，所有的具体工厂都实现了抽象工厂中定义的那些公共接口，因此只需改变具体工厂的实例，就可以在某种程度上改变整个软件系统的行为。

(2)当一个产品族中的多个对象被设计成一起工作时，它能够保证客户端始终只使用同一个产品族中的对象。

(3)增加新的产品族很方便，无须修改已有系统，符合“开闭原则”。

### 7. 抽象工厂模式的缺点

抽象工厂模式的主要缺点如下：

增加新的产品等级结构麻烦，需要对原有系统进行较大的修改，甚至需要修改抽象层代码，这显然会带来较大的不便，违背了“开闭原则”。

### 8. 抽象工厂模式适用场景

在以下情况下可以考虑使用抽象工厂模式：

(1)一个系统不应当依赖于产品类实例如何被创建、组合和表达的细节，这对于所有类型的工厂模式都是很重要的，用户无须关心对象的创建过程，将对象的创建和使用解耦。

(2)系统中有多于一个的产品族，而每次只使用其中某一产品族。用户可以动态改变产品族，也可以很方便地增加新的产品族。

(3)属于同一个产品族的产品将在一起使用，这一约束必须在系统的设计中体现出来。同一个产品族中的产品可以是没有任何关系的对象，但是它们都具有一些共同的约束，如同一品牌的电视机和冰箱，电视机与冰箱之间没有直接关系，但它们都是属于某一品牌的，此时具有一个共同的约束条件：品牌的类型。

(4)产品等级结构稳定，设计完成之后，不会向系统中增加新的产品等级结构或者删除已有的产品等级结构。
