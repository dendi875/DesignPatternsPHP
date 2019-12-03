# 简单工厂模式 (Simple Factory Pattern)

> 作者：张权
>
> 创建日期：2018-03-09 10:08:10

---

### 1. 模式定义

简单工厂模式(Simple Factory Pattern)：又称为静态工厂方法(Static Factory Method)模式，它属于类创建型模式。在简单工厂模式中，可以根据参数的不同返回不同的实例。简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有相同的接口或父类。

### 2. 模式结构

简单工厂模式包含如下角色：

* Factory：工厂
    * 工厂负责实现创建所需类的实例

* AbstractProduct: 抽象产品
    * 抽象产品是所有具体产品类的父类

* ConcreteProduct：具体产品
    * 具体产品是创建目标

### 3. 示例

以一个简单的计算器功能来说明下各角色的应用：

* OperationFactory.php：工厂

```php
/**
 * 运算工厂类
 * 一、根据不同的参数来创建加法、减法类等实例。
 * 二、通常createOperate方法为公开的静态方法。
 * 三、通常createOperate方法中包含简单的switch...case判断逻辑。
 *
 * @author    <quan.zhang@guanaitong.com>
 * @createDate 2018-03-08 20:03:21
 * @copyright Copyright (c) 2017 guanaitong.com
 */

class OperationFactory
{
    public static function createOperate($operate)
    {
        $operation = null;
        switch ((string)$operate) {
            case '+':
                $operation = new OperationAdd();
                break;
            case '-':
                $operation = new OperationSub();
                break;
        }
        return $operation;
    }
}
```


* Operation.php：抽象产品

```php
/**
 * 运算抽象类
 *
 * @author    <quan.zhang@guanaitong.com>
 * @createDate 2018-03-08 20:00:07
 * @copyright Copyright (c) 2017 guanaitong.com
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

```php
/**
 * 加法运算类
 *
 * @author    <quan.zhang@guanaitong.com>
 * @createDate 2018-03-08 20:02:42
 * @copyright Copyright (c) 2017 guanaitong.com
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
 * @createDate 2018-03-08 20:03:01
 * @copyright Copyright (c) 2017 guanaitong.com
 */

class OperationSub extends Operation
{
    public function getResult()
    {
        return bcsub($this->numberA, $this->numberB, 2);
    }
}
```

### 4. 客户端的使用
```php
$operation = OperationFactory::createOperate('+');
$operation->setNumberA(2000.30);
$operation->setNumberB(299.40);
$result = $operation->getResult();
```

### 5. UML类图

简单工厂模式实现的计算器示例代码UML图：

![SimpleFactoryPattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/SimpleFactoryPattern-0.png)

### 6. 简单工厂模式的优点

* 简单工厂模式的最大优点在于工厂类中包含了必要的逻辑判断，根据客户端的选择条件动态实例化相关的类，对于客户端来说，去除了与具体产品的依赖。
* 客户端无须知道所创建产品类的类名，只需要知道具体产品类所对应的参数，对于一些复杂的类名，通过简单工厂模式可以减少使用者的记忆量

### 7. 简单工厂模式的缺点

* 使用简单工厂模式将会增加系统中类的个数，在一定程度上增加了系统的复杂度和理解难度。
* 在产品类型较多时，有可能造成工厂类逻辑过于复杂，不利于系统的扩展和维护。
* 一旦添加新的产品就不得不修改工厂类逻辑，在工厂类的方法中增加“Case”条件分支，修改了原生的类，这就违背了开放－封闭原则

### 8. 适用场景

以下情况可以使用简单工厂模式：

* 工厂类负责创建的对象比较少：由于创建的对象较少，不会造成工厂方法中的业务逻辑太过复杂。
* 客户端只关心传入工厂类的参数，对于如何创建对象不关心：客户端既不需要关心创建细节，甚至连类名都不需要记住，只需要知道各产品类所对应的参数。