# 备忘录模式 (Memento Pattern)

> 作者：张权
>
> 创建日期：2018-07-22

---

### 0. 前言

Sunny软件公司欲开发一款可以运行在Android平台的触摸式中国象棋软件，由于考虑到有些用户是“菜鸟”，经常不小心走错棋；还有些用户因为不习惯使用手指在手机屏幕上拖动棋子，常常出现操作失误，因此该中国象棋软件要提供“悔棋”功能，用户走错棋或操作失误后可恢复到前一个步骤。如图1所示：

![chessman](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/chessman.jpg)

图1 中国象棋软件界面示意图

如何实现“悔棋”功能是Sunny软件公司开发人员需要面对的一个重要问题，“悔棋”就是让系统恢复到某个历史状态，在很多软件中通常称之为“撤销”。下面我们来简单分析一下撤销功能的实现原理：

在实现撤销时，首先必须保存软件系统的历史状态，当用户需要取消错误操作并且返回到某个历史状态时，可以取出事先保存的历史状态来覆盖当前状态。如图2所示：

![history](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/history.jpg)

图2 撤销功能示意图

备忘录模式正为解决此类撤销问题而诞生，它为我们的软件提供了“后悔药”，通过使用备忘录模式可以使系统恢复到某一特定的历史状态。

### 1. 模式定义

> 备忘录模式 (Memento Pattern)：在不破坏封装的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样可以在以后将对象恢复到原先保存的状态。又叫做快照模式（Snapshot）或Token模式。

备忘录模式是一种**对象行为型模式**。

### 2. 备忘录模式结构

备忘录模式包含如下角色：

+ ``` Originator ```：**原发器**
    + 它是一个普通类，可以创建一个备忘录，并存储它的当前内部状态，也可以使用备忘录来恢复其内部状态，一般将需要保存内部状态的类设计为原发器。

+ ``` Memento ```：**备忘录**
    + 存储原发器的内部状态，根据原发器来决定保存哪些内部状态。备忘录的设计一般可以参考原发器的设计，根据实际需要确定备忘录类中的属性。

+ ``` Caretaker ```：**负责人**
    + 负责人又称为管理者，它负责保存备忘录，但是不能对备忘录的内容进行操作或检查。在负责人类中可以存储一个或多个备忘录对象，它只负责存储对象，而不能修改对象，也无须知道对象的实现细节。

![MemontoPattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/MemontoPattern-0.png)

### 3. 备忘录模式应用实例

下面通过一个实例进一步学习和理解备忘录模式。

### 实例说明

用备忘录模式来实现中国象棋的撤销功能。

### 实例代码

* Chessman：``` 原发器 ```

``` php
/**
 * 象棋棋子类：原发器
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Chessman
{
    /* @var mixed */
    private $state;

    public function __construct($state)
    {
        $this->state = $state;
    }

    /**
     * @param mixed $state
     */
    public function setState($state)
    {
        $this->state = $state;
    }

    /**
     * @return mixed
     */
    public function getState()
    {
        return $this->state;
    }

    /** 创建一个备忘录，并存储它的当前状态 */
    public function save(): ChessmanMemonto
    {
        $state = is_object($this->state) ? clone $this->state : $this->state;
        return new ChessmanMemonto($state);
    }

    /** 根据备忘录来恢复其内部状态 */
    public function restore(ChessmanMemonto $memonto)
    {
        $this->state = $memonto->getState();
    }
}
```

* ChessmanMemonto： ``` 备忘录 ```

``` php
/**
 * 象棋棋子备忘录类：备忘录
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class ChessmanMemonto
{
    /**
     * @var mixed
     */
    private $state;

    public function __construct($stateToSave)
    {
        $this->state = $stateToSave;
    }

    /**
     * @param mixed $state
     */
    public function setState($state)
    {
        $this->state = $state;
    }

    /**
     * @return mixed
     */
    public function getState()
    {
        return $this->state;
    }
}
```

* MemontoCaretaker： ``` 负责人 ```

``` php
/**
 * 象棋棋子备忘录管理类：负责人
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class MemontoCaretaker
{
    /**
     * 定义一个集合来存储多个备忘录
     * @var array
     */
    protected $history = [];

    public function getMemonto(int $i): ChessmanMemonto
    {
        return $this->history[$i];
    }

    public function setMemonto(ChessmanMemonto $memonto): void
    {
        $this->history[] = $memonto;
    }
}
```

* 客户端测试代码

``` php
/**
 * 客户端测试类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Client
{
    /**
     * 定义一个索引来记录当前状态所在位置
     * @var integer
     */
    private static $index = -1;

    private static $mc;

    public function __construct()
    {
        static::$mc = new MemontoCaretaker();
    }

    public function run(): void
    {
        $chess = new Chessman(new Class {
            public $label = '车';
            public $x = 1;
            public $y = 1;
        });

        static::play($chess);
        $chess->getState()->x = 3;
        static::play($chess);
        $chess->getState()->x = 5;
        static::play($chess);

        static::undo($chess, static::$index);
        static::undo($chess, static::$index);
        static::reod($chess, static::$index);
        static::reod($chess, static::$index);
    }

    /* 下棋 */
    protected static function play(Chessman $chess): void
    {
        /* 创建一个备忘录并把当前状态保存在备忘录里，然后把创建好的备忘录保存在管理者里 */
        static::$mc->setMemonto($chess->save());
        static::$index++;
        printf("棋子%s当前位置为：第%d行第%d列。\n", $chess->getState()->label, $chess->getState()->x, $chess->getState()->y);
    }

    /* 悔棋 */
    protected static function undo(Chessman $chess, int $i): void
    {
        printf("***********悔棋************\n");
        static::$index--;
        /* 从管理者那里取出一个备忘录对象并将原发器状态恢复 */
        $chess->restore(static::$mc->getMemonto($i - 1));  /* 撤销到上一个备忘录 */
        printf("棋子%s当前位置为：第%d行第%d列。\n", $chess->getState()->label, $chess->getState()->x, $chess->getState()->y);
    }

    /* 撤销悔棋 */
    protected static function reod(Chessman $chess, int $i): void
    {
        printf("***********撤销悔棋************\n");
        static::$index++;
        /* 从管理者那里取出一个备忘录对象并将原发器状态恢复 */
        $chess->restore(static::$mc->getMemonto($i + 1));  /* 恢复到下一个备忘录 */
        printf("棋子%s当前位置为：第%d行第%d列。\n", $chess->getState()->label, $chess->getState()->x, $chess->getState()->y);
    }
}


$c = new Client();
$c->run();

以上例程会输出：

棋子车当前位置为：第1行第1列。<br/>
棋子车当前位置为：第3行第1列。<br/>
棋子车当前位置为：第5行第1列。<br/>
***********悔棋************<br/>
棋子车当前位置为：第3行第1列。<br/>
***********悔棋************<br/>
棋子车当前位置为：第1行第1列。<br/>
***********撤销悔棋************<br/>
棋子车当前位置为：第3行第1列。<br/>
***********撤销悔棋************<br/>
棋子车当前位置为：第5行第1列。
```

### 4. UML类图

示例代码UML图：

![MemontoPattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/MemontoPattern-1.png)

### 5. 备忘录模式的优点

备忘录模式的主要优点如下：

(1)它提供了一种状态恢复的实现机制，使得用户可以方便地回到一个特定的历史步骤，当新的状态无效或者存在问题时，可以使用暂时存储起来的备忘录将状态复原。

(2)备忘录实现了对信息的封装，一个备忘录对象是一种原发器对象状态的表示，不会被其他代码所改动。备忘录保存了原发器的状态，采用列表、堆栈等集合来存储备忘录对象可以实现多次撤销操作。

### 6. 备忘录模式的缺点

备忘录模式的主要缺点如下：

资源消耗过大，如果需要保存的原发器类的成员变量太多，就不可避免需要占用大量的存储空间，每保存一次对象的状态都需要消耗一定的系统资源。

### 7.备忘录模式适用场景

在以下情况下可以考虑使用备忘录模式：

保存一个对象在某一个时刻的全部状态或部分状态，这样以后需要时它能够恢复到先前的状态，实现撤销操作。
