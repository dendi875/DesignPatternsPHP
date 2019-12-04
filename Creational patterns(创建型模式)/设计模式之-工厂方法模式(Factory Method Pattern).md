# 工厂方法模式 (Factory Method Pattern)

> 作者：张权
>
> 创建日期：2018-03-17 14:50:47

---

### 1. 模式定义

工厂方法模式 (Factory Method Pattern)：它属于类创建型模式。定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法模式使一个类的实例化延迟到其子类中完成。

### 2. 模式结构

工厂方法模式包含如下角色：

* AbstractProduct: 抽象产品
    * 抽象产品角色是所有需要实例化产品类的父类

* ConcreteProduct：具体产品
    * 具体的产品，继承了抽象产品

* Factory：抽象工厂
    * 声明工厂方法，该方法返回一个AbstractProduct类型的对象

* ConcreteFactory：具体工厂
    * 重新定义工厂方法以返回一个ConcreteProduct类型的对象

### 3. 示例

以一个简单的计算器功能来说明下各角色的应用：

* Operation.php：抽象产品

``` php
/**
 * 运算抽象类
 *
 * @author    <quan.zhang@guanaitong.com>
 * @createDate 2018-03-17 14:52:15
 * @copyright Copyright (c) 2018 guanaitong.com
 */

abstract class Operation
{
    protected $numberA;

    protected $numberB;

    abstract protected function getResult();

    public function setNumberA($numberA)
    {
        $this->numberA = $numberA;
    }

    public function getNumberA()
    {
        return $this->numberA;
    }

    public function setNumberB($numberB)
    {
        $this->numberB = $numberB;
    }

    public function getNumberB()
    {
        return $this->numberB;
    }
}
```


* OperationAdd.php、OperationSub.php：具体产品

``` php
/**
 * 加法运算类
 *
 * @author    <quan.zhang@guanaitong.com>
 * @createDate 2018-03-17 14:54:10
 * @copyright Copyright (c) 2018 guanaitong.com
 */

class OperationAdd extends Operation
{
    public function getResult()
    {
        return bcadd($this->numberA, $this->numberB, 2);
    }
}

/**
 * 减法运算类
 *
 * @author    <quan.zhang@guanaitong.com>
 * @createDate 2018-03-17 14:54:26
 * @copyright Copyright (c) 2018 guanaitong.com
 */

class OperationSub extends Operation
{
    public function getResult()
    {
        return bcsub($this->numberA, $this->numberB, 2);
    }
}
```

* FactoryInterface.php：抽象工厂

``` php
/**
 * 运算工厂接口类
 * 定义了一个用于创建对象的接口，让其子类决定实例化哪一个类
 *
 * @author    <quan.zhang@guanaitong.com>
 * @createDate 2018-03-17 14:56:24
 * @copyright Copyright (c) 2018 guanaitong.com
 */

interface FactoryInterface
{
    public function createOperate();
}
```

* AddFactory.php、SubFactory.php：具体工厂

``` php
/**
 * 加法类工厂
 *
 * @author    <quan.zhang@guanaitong.com>
 * @createDate 2018-03-17 15:01:27
 * @copyright Copyright (c) 2018 guanaitong.com
 */

class AddFactory implements FactoryInterface
{
    public function createOperate()
    {
        return new OperationAdd();
    }
}

/**
 * 减法类工厂
 *
 * @author    <quan.zhang@guanaitong.com>
 * @createDate 2018-03-17 17:21:41
 * @copyright Copyright (c) 2018 guanaitong.com
 */

class SubFactory implements FactoryInterface
{
    public function createOperate()
    {
        return new OperationSub();
    }
}
```

### 4. 客户端的使用

```php
$addFactory = new AddFactory();
$addOperation = $addFactory->createOperate();
$addOperation->setNumberA(2000.30);
$addOperation->setNumberB(299.40);
$result = $addOperation->getResult();
```

### 5. UML类图

工厂方法模式实现的简单计算器示例代码UML图：

![FactoryMethodPattern](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/FactoryMethodPattern.png)

### 6. 简单工厂模式的优点

* 基于抽象工厂和抽象产品的多态性设计是工厂方法模式的关键。它能够使工厂可以自主确定创建何种产品对象，而如何创建这个对象的细节则完全封装在具体工厂内部。
* 工厂方法模式是简单工厂模式的进一步抽象和推广，使得在系统中加入新产品时，无须修改抽象工厂和抽象产品，也无须修改具体工厂和具体产品，而只要添加一个具体工厂和具体产品就行。这样，系统的可扩展性也就变得非常好，完全符合“开放－封闭原则”。

### 7. 简单工厂模式的缺点

* 在添加新产品时，需要编写新的具体工厂类和具体产品类，系统中类的个数将成对增加，在一定程度上增加了系统的复杂度和额外的开放量，有更多的类需要加载和解析，会给系统带来一些额外的开销。
* 由于引入了抽象工厂类和抽象产品类，增加了系统的抽象性和理解难度。

### 8. 适用场景

以下情况可以使用简单工厂模式：

* 客户端不知道它所需要的对象的类：在工厂方法模式中，客户端不需要知道具体产品类的类名，只需要知道其所对应的工厂即可，具体的产品由具体工厂类创建，客户端需要知道创建具体产品的工厂类。
* 一个类通过其子类来指定创建哪个对象：在工厂方法模式中，对于抽象工厂类只需要提供一个创建产品的接口，而由其子类来确定具体要创建的对象，利用面向对象的多态性和里氏代换原则，在程序运行时，子类将覆盖父类的方法，从而使得系统更容易扩展。

### 9. 模式扩展

* 多态性的丧失和模式的退化：一般来说，具体工厂类应当有一个抽象工厂父类，如果只有一个具体工厂类的话，抽象工厂父类就可以省略，也将发生退化。当只有一个具体工厂类时，在具体工厂类中可以创建所有的具体产品对象，并且具体工厂类中的方法设计为静态方法时，工厂模式就退化成简单工厂模式。