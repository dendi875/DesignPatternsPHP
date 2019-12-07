# 策略模式 (Strategy Pattern)

> 作者：张权
>
> 创建日期：2018-03-24 14:02:20

---

### 0. 缘起

* 假设现在有这样一个需求，商城系统要搞促销，有两种促销方案一种是总价基础上打8折，另一种满300元减100元，现在需要计算客户应付的金额

> * 坏写法：直接在客户端**硬编码**(Hard Coding)各种促销算法

``` php
//Client 主要代码，为了说明问题核心对入参的检验和必要的判断就省略
switch (intval($_GET['type'])) {
    case 1:  //打8折
        $result = bcmul($_GET['money'], 0.8, 2);
        break;
    case 2: //满300返100
        $result = $_GET['money'];
        if (bccomp($_GET['money'], 300) >= 0) {
            $result = bcsub($_GET['money'], bcmul(floor(bcdiv($_GET['money'], 300, 2)), 100, 2), 2);
        }
        break;
}
```

> * 普通写法：封装一个函数通过```switch…case…```等条件判断语句来进行选择哪种算法

``` php
function acceptCash(int $type, float $money) {
    $result = $money;
    switch ($type) {
        case 1:  //打8折
            $result = bcmul($money, 0.8, 2);
            break;
        case 2: //满300返100
            if (bccomp($money, 300) >= 0) {
                $result = bcsub($money, bcmul(floor(bcdiv($money, 300, 2)), 100, 2), 2);
            }
            break;
    }
    return $result;
}

//Client 主要代码
$result = acceptCash($_GET['type'], $_GET['money']);
```

> * 中级写法：编写一个类，每种促销算法对应一个方法

``` php
class AcceptCash
{
    private $money;

    public function __construct(float $money)
    {
        $this->money = $money;
    }

    /**
     * 打折
     * @param  float $moneyRebate  折扣率
     * @return float
     */
    public function cashRebate(float $moneyRebate): float
    {
        return bcmul($this->money, $moneyRebate, 2);
    }

    /**
     * 返利
     * @param  float $moneyCondition 返利条件
     * @param  float $moneyReturn    返利值
     * @return float $result
     */
    public function cashReturn(float $moneyCondition, float $moneyReturn): float
    {
        $result = $this->money;

        if (bccomp($this->money, $moneyCondition) >= 0) {
            $result = bcsub($this->money, bcmul(floor(bcdiv($this->money, $moneyCondition, 2)), $moneyReturn, 2), 2);
        }
        return $result;
    }
}

//Client 主要代码
$acceptCash = new AcceptCash($_GET['money']);
$result = $_GET['money'];
switch (intval($_GET['type'])) {
    case 1:
        $result = $acceptCash->cashRebate(0.8); //打8折
        break;
    case 2:
        $result = $acceptCash->cashReturn(300, 100); //满300返100
        break;
}
```

---

+ 上面的实现方式看似都没什么大问题都能满足功能需求但如果需求迭代变更，需要再新增多种促销策略比如节日促销、赠品促销、抽奖促销等，还要求变更已有的促销策略比如变成打8.5折，变成满400减100等，那么前面几种做法就充满了**代码的坏味道**
    + 硬编码写法：需要修改客户端代码，客户端充满了多个```case```分支，充满了一堆算法逻辑，导致客户端代码**难理解、难维护、难扩展**，并且增加了修改客户端代码的风险。
    + 普通写法：需要修改```acceptCash```函数，导致这个函数包含了很多的逻辑，使函数变得很臃肿，使人难以理解这个函数到底要干什么事(好的函数应该遵循：**一个函数只做一件事，并做好这件事。**)，最终变得难以维护扩展更别谈复用，增加了修改函数本身的风险。
    + 中级写法：需要修改客户端代码，修改```AcceptCash```类，导致一个类中有多个促销策略方法，使这个类变得很庞大，类**难复用**，无论任何需求变更你要都修改这个类，违背了**单一职责原则**。


+ 问：那应该如何做？
    + 答：请看下面的**策略模式**

### 1. 模式定义

**策略模式 (Strategy Pattern)**:它属于对象**行为型模式**。该模式定义了一系列算法，将每一个算法分别封装起来，让它们之间可以互相替换，此模式让算法独立于使用它的客户端而变化。

### 2. 模式结构

策略模式包含如下角色：

+ ``` Strategy ```：抽象策略类
    + 定义所有支持的算法的公共接口

+ ``` ConcreteStrategy ```：具体策略类
    + 封装具体的算法或行为

+ ``` Context ```: 环境类
    + 维护一个对``` Strategy ```类对象的引用

### 3. 代码示例

* CashSuper.php：抽象策略类

``` php
<?php
/**
 * 现金收费抽象类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-24 14:17:35
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class CashSuper
{
    abstract protected function acceptCash(float $money);
}
```

* CashRebate.php、CashReturn.php：具体策略类

``` php
<?php
/**
 * 打折收费类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-24 14:20:14
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class CashRebate extends CashSuper
{
    /**
     * 折扣率
     *
     * @var float
     */
    private $moneyRebate;

    public function __construct(float $moneyRebate)
    {
        $this->moneyRebate = $moneyRebate;
    }

    public function acceptCash(float $money): float
    {
        return bcmul($money, $this->moneyRebate, 2);
    }
}

<?php
/**
 * 返利收费类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-24 14:24:58
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class CashReturn extends CashSuper
{
    /**
     * 返利条件
     *
     * @var float
     */
    private $moneyCondition;

    /**
     * 返利值
     *
     * @var float
     */
    private $moneyReturn;

    public function __construct(float $moneyCondition, float $moneyReturn)
    {
        $this->moneyCondition = $moneyCondition;
        $this->moneyReturn = $moneyReturn;
    }

    public function acceptCash(float $money) :float
    {
        $result = $money;

        if (bccomp($money, $this->moneyCondition) >= 0) {
            $result = bcsub($money, bcmul(floor(bcdiv($money, $this->moneyCondition, 2)), $this->moneyReturn, 2), 2);
        }
        return $result;
    }
}
```

* CashContext.php：环境类

``` php
/**
 * 现金环境类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-03-24 14:29:06
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class CashContext
{
    private $cashSuper = null;

    public function __construct(int $type)
    {
        switch ($type) {
            case 1:
                $this->cashSuper = new CashRebate(0.8);
                break;
            case 2:
                $this->cashSuper = new CashReturn(300, 100);
                break;
        }
    }

    public function getResult(float $money): float
    {
        return $this->cashSuper->acceptCash($money);
    }
}
```

### 4. 客户端的使用

```php
//Client主要调用代码
$cashContext = new CashContext($_GET['type']);
$result = $cashContext->getResult($_GET['money']);
```

### 5. UML类图

策略模式实现的商场促销功能示例代码UML图：

![StrategyPattern](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/StrategyPattern.png)

### 6. 策略模式的优点

* 策略模式提供了对**开放封闭原则**的完美支持，可以在不修改客户端原有系统的基础上选择算法或行为，也可以灵活地增加或修改算法。
* 策略模式提供了管理相关的算法族的办法，提高了代码的**复用性**。
* 使得具体的算法与客户端彻底的分离，减少了它们之间的**耦合**。
* 策略模式**简化了单元测试**，因为每个算法都有自己的类，可以单独测试。

### 7. 策略模式的缺点

* 策略模式将造成产生很多的策略类，增加了系统中类的个数

### 8. 适用场景

以下情况可以使用策略模式：

* 一个系统需要动态地在几种算法中选择一种。
* 如果一个对象有很多的行为，如果不用恰当的模式，这些行为就只好使用多重的条件选择语句来实现。
* 不希望客户端知道复杂的、与算法相关的数据结构，在具体策略类中封装算法和相关的数据结构，提高算法的保密性与安全性。

### 9. 总结

* 策略模式中环境类自己选择一个具体策略类，具体策略类无须关心环境类，它们之间是**聚合关系**。
* 策略模式是一种定义一系列算法的方法(```商场的各种促销方式```)，从概念上来看，所有这些算法完成的都是相同的工作(```计算费用结果```)，只是实现不同，它可以以相同的方式调用所有的算法(```getResult```)，减少了各种算法类与使用算法类之间的耦合。

### 10. 新发现
* 你有发现本文策略模式中运用了**简单工厂模式**？
* 你有发现本文的策略模式中体现了```OOP```中的**封装、继承、多态**特性？