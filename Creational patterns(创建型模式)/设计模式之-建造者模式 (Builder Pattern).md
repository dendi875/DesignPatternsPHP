# 建造者模式 (Builder Pattern)

> 作者：张权
>
> 创建日期：2018-05-12

---

### 0. 建造者模式概述

无论是在现实世界中还是在软件系统中，都存在一些复杂的对象，它们拥有多个组成部分，如汽车，它包括车轮、方向盘、发动机等各种部件。而对于大多数用户而言，无须知道这些部件的装配细节，也几乎不会使用单独某个部件，而是使用一辆完整的汽车，可以通过建造者模式对其进行设计与描述，建造者模式可以将部件和其组装过程分开，一步一步创建一个复杂的对象。用户只需要指定复杂对象的类型就可以得到该对象，而无须知道其内部的具体构造细节。

在软件开发中，也存在大量类似汽车一样的复杂对象，它们拥有一系列成员属性，这些成员属性中有些是引用类型的成员对象。而且在这些复杂对象中，还可能存在一些限制条件，如某些属性没有赋值则复杂对象不能作为一个完整的产品使用；有些属性的赋值必须按照某个顺序，一个属性没有赋值之前，另一个属性可能无法赋值等。

复杂对象相当于一辆有待建造的汽车，而对象的属性相当于汽车的部件，建造产品的过程就相当于组合部件的过程。由于组合部件的过程很复杂，因此，这些部件的组合过程往往被“外部化”到一个称作建造者的对象里，建造者返还给客户端的是一个已经建造完毕的完整产品对象，而用户无须关心该对象所包含的属性以及它们的组装方式，这就是建造者模式的模式动机。


### 1. 模式定义

> 建造者模式 (Builder Pattern)：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

建造者模式又称为生成器模式，它是一种**对象创建型模式**。

### 2. 模式结构

建造者模式包含如下角色：

+ ``` Builder ```：**抽象建造者**
    + 它为创建一个产品Product对象的各个部件指定抽象接口，在该接口中一般声明两类方法，一类方法是buildPartX()，它们用于创建复杂对象的各个部件；另一类方法是getResult()，它们用于返回复杂对象。Builder既可以是抽象类，也可以是接口。

+ ``` ConcreteBuilder ```：**具体建造者**
    + 它实现了Builder接口，实现各个部件的具体构造和装配方法，定义并明确它所创建的复杂对象，也可以提供一个方法返回创建好的复杂产品对象。

+ ``` Product ```：**产品角色**
    + 它是被构建的复杂对象，包含多个组成部件，具体建造者创建该产品的内部表示并定义它的装配过程。

+ ``` Director ```：**指挥者**
    + 指挥者又称为导演类，它负责安排复杂对象的建造次序，指挥者与抽象建造者之间存在关联关系，可以在其construct()建造方法中调用建造者对象的部件构造与装配方法，完成复杂对象的建造。客户端一般只需要与指挥者进行交互，在客户端确定具体建造者的类型，并实例化具体建造者对象，然后通过指挥者类的构造函数或者Setter方法将该对象传入指挥者类中。

![BuilderPattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/BuilderPattern-0.png)

### 3. 模式实现

* 建造者模式是较为复杂的创建型模式，它将客户端与包含多个组成部分（或部件）的复杂对象的创建过程分离，客户端无须知道复杂对象的内部组成部分与装配方式，只需要知道所需建造者的类型即可。它关注如何一步一步创建一个的复杂对象，不同的具体建造者定义了不同的创建过程，且具体建造者相互独立，增加新的建造者非常方便，无须修改已有代码，系统具有较好的扩展性。

* 在建造者模式的结构中引入了一个指挥者类Director，该类主要有两个作用：一方面它隔离了客户端与产品创建的过程；另一方面它控制产品的创建过程，包括某个buildPartX()方法是否被调用以及多个buildPartX()方法调用的先后次序等。指挥者针对抽象建造者编程，客户端只需要知道具体建造者的类型，即可通过指挥者类调用建造者的相关方法，返回一个完整的产品对象。

### 4. 建造者模式应用实例

下面通过一个实例进一步学习和理解建造者模式。

### 实例说明

现在需要开发一个创建RPG游戏角色的功能，像大多数RPG游戏一样不同类型的游戏角色，其性别、脸型、服装、发型等外部特性都有所差异，例如“天使”拥有美丽的面容和披肩的长发，并身穿一袭白裙；而“恶魔”极其丑陋，留着光头并穿一件刺眼的黑衣。

现使用建造者模式来实现游戏角色的创建。

### 实例代码

* Actor：``` 产品角色类 ```

``` php
/**
 * 游戏角色类(复杂产品类)，本例子成员属性(部件或零件)都是很简单的string，真实情况下
 *可能会存在多个成员属性是其它类的对象，所以才构成一个复杂对象
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-12
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Actor
{
    /**
     * 角色类型
     * @var string
     */
    private $type;

    /**
     * 角色性别
     * @var string
     */
    private $sex;

    /**
     * 角色脸形
     * @var string
     */
    private $face;

    /**
     * 角色服装
     * @var string
     */
    private $constume;

    /**
     * 角色发型
     * @var string
     */
    private $hairstype;

    public function setType(string $type): void
    {
        $this->type = $type;
    }

    public function setSex(string $sex): void
    {
        $this->sex = $sex;
    }

    public function setFace(string $face): void
    {
        $this->face = $face;
    }

    public function setConstume(string $constume): void
    {
        $this->constume = $constume;
    }

    public function setHairstype(string $hairstype): void
    {
        $this->hairstype = $hairstype;
    }

    public function getType(): string
    {
        return $this->type;
    }

    public function getSex(): string
    {
        return $this->sex;
    }

    public function getFace(): string
    {
        return $this->face;
    }

    public function getConstume(): string
    {
        return $this->constume;
    }

    public function getHairstype(): string
    {
        return $this->hairstype;
    }
}
```

* ActorBuilder： ``` 抽象建造者类 ```

``` php
/**
 * 角色建造器：抽象建造者
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-12
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class ActorBuilder
{
    /**
     * 游戏角色产品对象
     * @var Actor
     */
    protected $actor;

    /**
     *+-----------------------------------------------------------
     *| 这里ActorBuilder 与 Actor 表现为组合关系。
     *|----------------------------------------------------------
     *| ActorBuilder为整体对象，Actor为成员对象。
     *|-----------------------------------------------------------
     *| 组合关系表示类之间整体和部分的关系，成员对象与整体对象之间
     *| 具有同生共死的关系，一旦整体对象不存在，成员对象也将不存在。
     *+-----------------------------------------------------------
     *| 组合关系通常通过在整体类的构造方法中直接实例化成员类。
     *+-----------------------------------------------------------
     *
     * @return Actor
     */
    public function __construct()
    {
        $this->actor = new Actor();
    }

    abstract public function buildType();

    abstract public function buildSex();

    abstract public function buildFace();

    abstract public function buildConstume();

    abstract public function buildHairstyle();

    /**
     * 返回一个构建好的完整的游戏角色对象
     *
     * @return Actor
     */
    public function createActor(): Actor
    {
        return $this->actor;
    }
}
```

* HeroBuilder、AngelBuilder、DevilBuilder： ``` 具体建造类 ```

``` php
/**
 * 英雄角色建造器：具体建造者
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-12
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class HeroBuilder extends ActorBuilder
{
    public function buildType(): void
    {
        $this->actor->setType('英雄');
    }

    public function buildSex(): void
    {
        $this->actor->setSex('男');
    }

    public function buildFace(): void
    {
        $this->actor->setFace('英俊');
    }

    public function buildConstume(): void
    {
        $this->actor->setConstume('盔甲');
    }

    public function buildHairstyle(): void
    {
        $this->actor->setHairstype('飘逸');
    }
}

/**
 * 天使角色建造器：具体建造者
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-12
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class AngelBuilder extends ActorBuilder
{
    public function buildType(): void
    {
        $this->actor->setType('天使');
    }

    public function buildSex(): void
    {
        $this->actor->setSex('女');
    }

    public function buildFace(): void
    {
        $this->actor->setFace('漂亮');
    }

    public function buildConstume(): void
    {
        $this->actor->setConstume('白裙');
    }

    public function buildHairstyle(): void
    {
        $this->actor->setHairstype('披肩长发');
    }
}

/**
 * 恶魔角色建造器：具体建造者
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-12
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class DevilBuilder extends ActorBuilder
{
    public function buildType(): void
    {
        $this->actor->setType('恶魔');
    }

    public function buildSex(): void
    {
        $this->actor->setSex('妖');
    }

    public function buildFace(): void
    {
        $this->actor->setFace('丑陋');
    }

    public function buildConstume(): void
    {
        $this->actor->setConstume('黑衣');
    }

    public function buildHairstyle(): void
    {
        $this->actor->setHairstype('光头');
    }
}
```

* ActorDirector： ``` 指挥者类 ```

``` php
/**
 * 游戏角色创建者：指挥者
 *
 * @author     <quan.zhang@guanaitong.com>
 * @createDate 2018-05-12
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class ActorDirector
{

    /**
     * 逐步构建复杂产品对象
     *+-----------------------------------------------------------
     *| 这里ActorDirector 与 ActorBuilder 表现为是依赖关系。
     *+-----------------------------------------------------------
     *|依赖关系通常有三种方式来实现
     *|1.将一个类的对象作为另一个类中方法的参数
     *|2.一个类的方法中将另一个类的对象作为其局部变量
     *|3.在一个类的方法中调用另一个类的静态方法
     *+-----------------------------------------------------------
     *
     * @return Actor
     */
    public function build(ActorBuilder $actorBuilder): Actor
    {
        $actorBuilder->buildType();
        $actorBuilder->buildSex();
        $actorBuilder->buildFace();
        $actorBuilder->buildConstume();
        $actorBuilder->buildHairstyle();

        return $actorBuilder->createActor();
    }
}
```

* 客户端测试代码

``` php
/* 指明要创造的具体建造者 */
$builder = new HeroBuilder();

/* 实例化指挥者类 */
$actorDirector = new ActorDirector();

/* 通过指挥者创建完整的产品 */
$actor = $actorDirector->build($builder);

echo "类型：".$actor->getType().PHP_EOL.
     "性别：".$actor->getSex().PHP_EOL.
     "面容：".$actor->getFace().PHP_EOL.
     "服装：".$actor->getConstume().PHP_EOL.
     "发型：".$actor->getHairstype();
```

以上例程会输出：

类型：英雄
性别：男
面容：英俊
服装：盔甲
发型：飘逸

### 5. UML类图

示例代码UML图：

![BuilderPattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/BuilderPattern-1.png)

### 6. 建造者模式的优点

建造者模式的主要优点如下：

(1)在建造者模式中，客户端不必知道产品内部组成的细节，将产品本身与产品的创建过程解耦，使得相同的创建过程可以创建不同的产品对象。

(2)每一个具体建造者都相对独立，而与其他的具体建造者无关，因些可以很方便地替换具体建造者或增加新的具体建造者，用户使用不同的具体建造者即可得到不同的产品对象。

(3)可以更加精细地控制产品的创建过程。将复杂产品的创建步骤分解在不同的方法中，使得创建过程更加清晰，也更方便使用程序来控制创建过程。

(4)由于指挥者类针对抽象建造者编程，增加新的具体建造者无须修改原有类库的代码，系统扩展方便，符合“开闭原则”

### 7. 建造者模式的缺点

建造者模式的主要缺点如下：

(1)建造者模式所创建的产品一般具有较多的共同点，其组成部分相似，如果产品之间的差异性很大，例如很多组成部分都不相同，不适合使用建造者模式，因此其使用范围受到一定的限制。

(2)如果产品的内部变化复杂，可能会导致需要定义很多具体建造者类来实现这种变化，导致系统变得很庞大，增加系统的理解难度和运行成本。

### 8. 建造者模式适用场景

在以下情况下可以考虑使用建造者模式：

(1)需要生成的产品对象有复杂的内部结构，这些产品对象通常包含多个成员属性。

(2)需要生成的产品对象的属性相互依赖，需要指定其生成顺序。

(3)对象的创建过程独立于创建该对象的类。在建造者模式中通过引入了指挥者类，将创建过程封装在指挥者类中，而不在建造者类和客户类中。

(4)隔离复杂对象的创建和使用，并使得相同的创建过程可以创建不同的产品。