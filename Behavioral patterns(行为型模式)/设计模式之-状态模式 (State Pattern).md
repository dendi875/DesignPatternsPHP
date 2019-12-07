# 状态模式 (State Pattern)

> 作者：张权
>
> 创建日期：2018-06-02

---

### 0. 前言

“人有悲欢离合，月有阴晴圆缺”，包括人在内，很多事物都具有多种状态，而且在不同状态下会具有不同的行为，这些状态在特定条件下还将发生相互转换。就像水，它可以凝固成冰，也可以受热蒸发后变成水蒸汽，水可以流动，冰可以雕刻，蒸汽可以扩散。我们可以用UML状态图来描述H2O的三种状态，如图1所示：

![0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/0.jpg)

图1 H2O的三种状态

在软件系统中，有些对象也像水一样具有多种状态，这些状态在某些情况下能够相互转换，而且对象在不同的状态下也将具有不同的行为。为了更好地对这些具有多种状态的对象进行设计，我们可以使用一种被称之为状态模式的设计模式。

### 1. 模式定义

> 状态模式 (State Pattern)：允许一个对象在其内部状态改变时改变它的行为，对象看起来似乎修改了它的类。

状态模式是一种**对象行为型模式**。

* 状态模式用于解决系统中复杂对象的状态转换以及不同状态下行为的封装问题。当系统中某个对象存在多个状态，这些状态之间可以进行转换，而且对象在不同状态下行为不相同时可以使用状态模式。状态模式将一个对象的状态从该对象中分离出来，封装到专门的状态类中，使得对象状态可以灵活变化，对于客户端而言，无须关心对象状态的转换以及对象所处的当前状态，无论对于何种状态的对象，客户端都可以一致处理。

### 2. 模式结构

状态模式包含如下角色：

+ ``` Context ```：**环境类**
    + 环境类又称为上下文类，它是拥有多种状态的对象。由于环境类的状态存在多样性且在不同状态下对象的行为有所不同，因此将状态独立出去形成单独的状态类。在环境类中维护一个抽象状态类State的实例，这个实例定义当前状态，在具体实现时，它是一个State子类的对象。

+ ``` State ```：**抽象状态类**
    + 它用于定义一个接口以封装与环境类的一个特定状态相关的行为，在抽象状态类中声明了各种不同状态对应的方法，而在其子类中实现类这些方法，由于不同状态下对象的行为可能不同，因此在不同子类中方法的实现可能存在不同，相同的方法可以写在抽象状态类中。

+ ``` ConcreteState ```：**具体状态类**
    + 它是抽象状态类的子类，每一个子类实现一个与环境类的一个状态相关的行为，每一个具体状态类对应环境的一个具体状态，不同的具体状态类其行为有所不同。

![StatePattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/StatePattern-0.jpg)

### 3. 状态模式应用实例

下面通过一个实例进一步学习和理解状态模式。

### 实例说明

我们就用状态模式来实现一下水的三种形态转换。

### 实例代码

* Water：``` 环境类 ```

``` php
/**
 * Water：环境类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-06-02
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Water
{
    /**
     * 维持一个对抽象状态对象的引用
     *
     * @var WaterState
     */
    private $waterState;

    /**
     * 水的分类；比如淡水、咸水
     *
     * @var string
     */
    private $category;

    /**
     * 水的温度
     *
     * @var float
     */
    private $temperature;

    public function __construct(string $category, float $temperature)
    {
        $this->category = $category;
        $this->temperature = $temperature;
        $this->waterState = new LiquidState($this); /* 设置初始状态 */

        printf("%s初始温度为%.2f \n", $this->category, $this->temperature);
        printf("---------------- \n");
    }

    public function setTemperature(float $temperature): void
    {
        $this->temperature = $temperature;
    }

    public function getTemperature(): float
    {
        return $this->temperature;
    }

    public function setState(WaterState $waterState): void
    {
        $this->waterState = $waterState;
    }

    public function warming(float $temperature): void
    {
        printf("%s升温%.2f \n", $this->category, $temperature);

        $this->waterState->warming($temperature);   /* 调用具体状态对象的warming()方法*/

        printf("现在温度为%.2f \n", $this->temperature);
        printf("现在水的状态为%s \n", get_class($this->waterState));
        printf("---------------- \n");
    }

    public function cooling(float $temperature): void
    {
        printf("%s降温%.2f \n", $this->category, $temperature);

        $this->waterState->cooling($temperature);   /* 调用具体状态对象的cooling()方法*/

        printf("现在温度为%.2f \n", $this->temperature);
        printf("现在水的状态为%s \n", get_class($this->waterState));
        printf("---------------- \n");
    }

    public function action(): void
    {
        $this->waterState->action();    /* 调用具体状态对象的action()方法 */
    }
}

```

* WaterState： ``` 抽象状态类 ```

``` php
/**
 * 水状态抽象类：抽象状态类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-06-02
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class WaterState
{
    /**
     * 水环境类的实例
     *
     * @var Water
     */
    protected $water;

    public function __construct(Water $water)
    {
        $this->water = $water;
    }

    /* 用于把水温度升高 */
    abstract public function warming(float $temperature);

    /* 用于把水温度降低 */
    abstract public function cooling(float $temperature);

    /* 不同形态的水具有各自己的行为 */
    abstract public function action();

     /* 用于在每一次执行升温和降温后根据温度来判断是否要进行状态转换并实现状态转换 */
    abstract public function stateCheck();
}
```

* LiquidState、SolidState、GasState： ``` 具体状态类 ```

``` php
/**
 * 液态水：具体状态类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-06-02
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class LiquidState extends WaterState
{

    public function warming(float $temperature): void
    {
        $temp = bcadd($this->water->getTemperature(), $temperature, 2);
        $this->water->setTemperature($temp);
        $this->stateCheck();
    }

    public function cooling(float $temperature): void
    {
        $temp = bcsub($this->water->getTemperature(), $temperature, 2);
        $this->water->setTemperature($temp);
        $this->stateCheck();
    }

    public function action(): void
    {
        printf("液态水，能流动！\n");
    }

    public function stateCheck(): void
    {
        if (bccomp($this->water->getTemperature(), 0, 2) !== 1) {   /* 水温小于等于0 */
            /* 水状态对象设置为固态 */
            $this->water->setState(new SolidState($this->water));
        } elseif (bccomp($this->water->getTemperature(), 100, 2) !== -1) { /* 水温大于等于100 */
            /* 水状态对象设置为气态*/
            $this->water->setState(new GasState($this->water));
        }
    }
}

/**
 * 固态水：具体状态类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-06-02
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class SolidState extends WaterState
{

    public function warming(float $temperature): void
    {
        $temp = bcadd($this->water->getTemperature(), $temperature, 2);
        $this->water->setTemperature($temp);
        $this->stateCheck();
    }

    public function cooling(float $temperature): void
    {
        $temp = bcsub($this->water->getTemperature(), $temperature, 2);
        $this->water->setTemperature($temp);
        $this->stateCheck();
    }

    public function action(): void
    {
        printf("固态水，能雕刻！\n");
    }

    public function stateCheck(): void
    {
        if (bccomp($this->water->getTemperature(), 100, 2) !== -1) { /* 水温大于等于100 */
            /* 水状态对象设置为气态*/
            $this->water->setState(new GasState($this->water));
        } elseif (bccomp($this->water->getTemperature(), 0, 2) === 1) {   /* 水温大于0 */
            /* 水状态对象设置为液态 */
            $this->water->setState(new LiquidState($this->water));
        }
    }
}

/**
 * 气态水：具体状态类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-06-02
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class GasState extends WaterState
{

    public function warming(float $temperature): void
    {
        $temp = bcadd($this->water->getTemperature(), $temperature, 2);
        $this->water->setTemperature($temp);
        $this->stateCheck();
    }

    public function cooling(float $temperature): void
    {
        $temp = bcsub($this->water->getTemperature(), $temperature, 2);
        $this->water->setTemperature($temp);
        $this->stateCheck();
    }

    public function action(): void
    {
        printf("气态水，能扩散！\n");
    }

    public function stateCheck(): void
    {
        if (bccomp($this->water->getTemperature(), 0, 2) !== 1) { /* 水温小于等于0 */
            /* 水状态对象设置为固态 */
            $this->water->setState(new SolidState($this->water));
        } elseif (bccomp($this->water->getTemperature(), 100, 2) === -1) {   /* 水温小于100 */
            /* 水状态对象设置为液态*/
            $this->water->setState(new LiquidState($this->water));
        }
    }
}
```

* 客户端测试代码

``` php
/* 实例化一种淡水 */
$water = new Water('淡水', 20.00);

$water->warming(60);
$water->cooling(10);
$water->warming(20);
$water->warming(10);

$water->action();
```

以上例程会输出：


淡水初始温度为20.00<br>
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-<br>
淡水升温60.00<br>
现在温度为80.00<br>
现在水的状态为LiquidState<br>
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-<br>
淡水降温10.00<br>
现在温度为70.00<br>
现在水的状态为LiquidState<br>
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-<br>
淡水升温20.00<br>
现在温度为90.00<br>
现在水的状态为LiquidState<br>
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-<br>
淡水升温10.00<br>
现在温度为100.00<br>
现在水的状态为GasState<br>
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-<br>
气态水，能扩散！<br>


* 环境类``` Water ```维持一个对抽象状态类的引用，通过``` setState() ```方法可以向环境类注入不同的状态对象，再在环境类的业务方法中调用状态对象的方法。

### 4. UML类图

示例代码UML图：

![StatePattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/StatePattern-1.png)

### 5. 状态模式的优点

状态模式的主要优点如下：

(1)封装了状态的转换规则，在状态模式中可以将状态的转换代码封装在环境类或者具体状态类中，可以对状态转换代码进行集中管理，而不是分散在一个个业务方法中。

(2)将所有与某个状态有关的行为放到一个类中，只需要注入一个不同的状态对象即可使环境对象拥有不同的行为。

(3)允许状态转换逻辑与状态对象合成一体，而不是提供一个巨大的条件语句块，状态模式可以让我们避免使用庞大的条件语句来将业务方法和状态转换代码交织在一起。

### 6. 状态模式的缺点

状态模式的主要缺点如下：

(1)状态模式的使用必然会增加系统中类和对象的个数，导致系统运行开销增大。

(2)状态模式的结构与实现都较为复杂，如果使用不当将导致程序结构和代码的混乱，增加系统设计的难度。

(3)状态模式对“开闭原则”的支持并不太好，增加新的状态类需要修改那些负责状态转换的源代码，否则无法转换到新增状态；而且修改某个状态类的行为也需修改对应类的源代码。

### 7. 状态模式适用场景

在以下情况下可以考虑使用状态模式：

(1)对象的行为依赖于它的状态（如某些属性值），状态的改变将导致行为的变化。

(2)在代码中包含大量与对象状态有关的条件语句，这些条件语句的出现，会导致代码的可维护性和灵活性变差，不能方便地增加和删除状态，并且导致客户类与类库之间的耦合增强。