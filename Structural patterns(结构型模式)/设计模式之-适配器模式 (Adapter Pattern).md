# 适配器模式 (Adapter Pattern)

> 作者：张权
>
> 创建日期：2018-06-25

---

### 0. 前言

我的笔记本电脑的工作电压是20V，而我国的家庭用电是220V，如何让20V的笔记本电脑能够在220V的电压下工作？答案是引入一个电源适配器(AC Adapter)，俗称充电器或变压器，有了这个电源适配器，生活用电和笔记本电脑即可兼容，如图1所示：

![ac](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/ac.jpg)

图1 电源适配器示意图

在软件开发中，有时也存在类似这种不兼容的情况，我们也可以像引入一个电源适配器一样引入一个称之为适配器的角色来协调这些存在不兼容的结构，这种设计方案即为适配器模式。

与电源适配器相似，在适配器模式中引入了一个被称为适配器(Adapter)的包装类，而它所包装的对象称为适配者(Adaptee)，即被适配的类。适配器的实现就是把客户类的请求转化为对适配者的相应接口的调用。也就是说：当客户类调用适配器的方法时，在适配器类的内部将调用适配者类的方法，而这个过程对客户类是透明的，客户类并不直接访问适配者类。因此，适配器让那些由于接口不兼容而不能交互的类可以一起工作。

### 1. 模式定义

> 适配器模式 (Adapter Pattern)：将一个接口转换成客户希望的另一个接口，使接口不兼容的那些类可以一起工作，其别名为包装器(Wrapper)。

适配器模式既可以作为**类结构型模式**，也可以作为**对象结构型模式**。

* 适配器模式可以将一个类的接口和另一个类的接口匹配起来，而无须修改原来的适配者接口和抽象目标类接口。
* 在适配器模式定义中所提及的接口是指广义的接口，它可以表示一个方法或者方法的集合。
* 在适配器模式中，我们通过增加一个新的适配器类来解决接口不兼容的问题，使得原本没有任何关系的类可以协同工作。根据适配器类与适配者类的关系不同，适配器模式可分为对象适配器和类适配器两种，在对象适配器模式中，适配器与适配者之间是关联关系；在类适配器模式中，适配器与适配者之间是继承（或实现）关系。在实际开发中，对象适配器的使用频率更高。

### 2. 对象适配器模式结构

对象适配器模式包含如下角色：

+ ``` Target ```：**目标抽象类**
    + 目标抽象类定义客户所需接口，可以是一个抽象类或接口，也可以是具体类。

+ ``` Adaptee ```：**适配者类**
    + 适配者即被适配的角色，它定义了一个已经存在的接口，这个接口需要适配，适配者类一般是一个具体的类，包含了客户希望使用的业务方法，在某些情况下可能没有适配者类的源代码。

+ ``` Adapter ```：**适配器类**
    + 适配器可以调用另一个接口，作为一个转换器，对Adaptee和Target进行适配，适配器类是适配器模式的核心，在对象适配器中，它通过继承Targe并关联一个Adaptee对象使二者产生联系。

![Adapter-Pattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/Adapter-Pattern-0.jpg)

根据对象适配器模式结构图，在对象适配器中，客户端需要调用request()方法，而适配者类Adaptee没有该方法，但是它所提供的specificRequest()方法却是客户端所需要的。为了使客户端能够使用适配者类，需要提供一个包装类Adapter，即适配器类。这个包装类包装了一个适配者的实例，从而将客户端与适配者衔接起来，在适配器的request()方法中调用适配者的specificRequest()方法。因为适配器类与适配者类是关联关系（也可称之为委派关系），所以这种适配器模式称为对象适配器模式。

### 3. 适配器模式应用实例

下面通过一个实例进一步学习和理解适配器模式。

### 实例说明

> Sunny软件公司在很久以前曾开发了一个算法库，里面包含了一些常用的算法，例如排序算法和查找算法，在进行各类软件开发时经常需要重用该算法库中的算法。在为某学校开发教务管理系统时，开发人员发现需要对学生成绩进行排序和查找，该系统的设计人员已经开发了一个成绩操作接口ScoreOperation，在该接口中声明了排序方法``` arraySort(array $list): array ```和查找方法``` arraySearch(int $needle, array $haystack): int ```，为了提高排序和查找的效率，开发人员决定重用算法库中的快速排序算法类QuickSort和二分查找算法类BinarySearch，其中QuickSort的``` qSort(array $list): array ```方法实现了快速排序，BinarySearch的``` binsearch(array $arr, int $start, int $end, int $needle): int ```方法实现了二分查找。

由于某些原因，现在Sunny公司开发人员已经找不到该算法库的源代码，无法直接通过复制和粘贴操作来重用其中的代码；部分开发人员已经针对ScoreOperation接口编程，如果再要求对该接口进行修改或要求大家直接使用QuickSort类和BinarySearch类将导致大量代码需要修改。

Sunny软件公司开发人员面对这个没有源码的算法库，遇到一个幸福而又烦恼的问题：如何在既不修改现有接口又不需要任何算法库代码的基础上能够实现算法库的重用？

通过分析，我们不难得知，现在Sunny软件公司面对的问题有点类似本章最开始所提到的电压问题，成绩操作接口ScoreOperation好比只支持20V电压的笔记本，而算法库好比220V的家庭用电，这两部分都没有办法再进行修改，而且它们原本是两个完全不相关的结构，如图2所示：

![algorithm](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/algorithm.jpg)

图2 需协调的两个系统的结构示意图

解决方案：我们需要ScoreOperation接口能够和已有算法库一起工作，让它们在同一个系统中能够兼容，最好的实现方法是增加一个类似电源适配器一样的适配器角色，通过适配器来协调这两个原本不兼容的结构。

### 实例代码

* ScoreOperation：``` 目标抽象类 ```

``` php
/**
 * 抽象成绩操作类：目标抽象类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-06-25
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

interface ScoreOperation
{
    public function arraySort(array $list): array;

    public function arraySearch(int $needle, array $haystack): int;
}
```

* QuickSort、BinarySearch： ``` 适配者类 ```

``` php
/**
 * 快速排序类：适配者
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-06-25
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class QuickSort
{
    public function qSort(array $list): array
    {
        if (count($list) <= 1) {
            return $list;
        }

        $baseValue = $list[0];

        $leftArray = $rightArray = [];

        array_shift($list);

        foreach ($list as $value) {
            if ($value < $baseValue) {
                $leftArray[] = $value;
            } else {
                $rightArray[] = $value;
            }
        }

        $leftArray = $this->qSort($leftArray);
        $rightArray = $this->qSort($rightArray);

        return array_merge($leftArray, array($baseValue), $rightArray);
    }
}

/**
 * 二分查找类：适配者
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-06-25
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class BinarySearch
{
    public function binsearch(array $arr, int $start, int $end, int $needle): int
    {
        if ($start > $end) {
            return -1;
        }

        $mid = ceil(($end + $start) / 2);
        if ($arr[$mid] > $needle) {
            return $this->binsearch($arr, $start, $end - 1, $needle);
        } else if ($arr[$mid] < $needle) {
            return $this->binsearch($arr, $start + 1, $end, $needle);
        } else {
            return $mid;
        }
    }
}
```

* OperationAdapter： ``` 适配器类 ```

``` php
/**
 * 操作适配器：适配器类
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-06-25
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class OperationAdapter implements ScoreOperation
{
    private $sortObj;

    private $searchObj;

    public function __construct()
    {
        $this->sortObj = new QuickSort();
        $this->searchObj = new BinarySearch();
    }

    public function arraySort(array $list): array
    {
        return $this->sortObj->qSort($list);
    }

    public function arraySearch(int $needle, array $haystack): int
    {
        return $this->searchObj->binsearch($haystack, 0, count($haystack) - 1, $needle);
    }
}
```

* 客户端测试代码

``` php
/* 定义成绩数组 */
$scores = [90, 84, 76, 50, 69, 90, 91, 88, 96];
$operation = new OperationAdapter();

$result = $operation->arraySort($scores);
echo "成绩排序结果：".implode(',', $result);   //output：成绩排序结果：50,69,76,84,88,90,90,91,96

$key1 = $operation->arraySearch(90, $result);
$key2 = $operation->arraySearch(84, $result);
$key3 = $operation->arraySearch(100, $result);
echo "查找成绩为90的索引位置：".$key1;   //output：查找成绩为90的索引位置：5
echo "查找成绩84的索引位置：".$key2;   //output：查查找成绩84的索引位置：3
echo "查找成绩100的索引位置：".$key3;  //output：查找成绩100的索引位置：-1
```

### 4. UML类图

示例代码UML图：

![AdapterPattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/AdapterPattern-1.png)

### 5. 适配器模式的优点

适配器模式的主要优点如下：

(1)将目标类和适配者类解耦，通过引入一个适配器类来重用现有的适配者类，无须修改原有结构。

(2)增加了类的透明性和复用性，将具体的业务实现过程封装在适配者类中，对于客户端类而言是透明的，而且提高了适配者的复用性，同一个适配者类可以在多个不同的系统中复用。

(3)一个对象适配器可以把多个不同的适配者适配到同一个目标。

### 6. 适配器模式的缺点

类适配器模式的主要缺点如下：

(1)PHP不支持多继承，一次最多只能适配一个适配者类，不能同时适配多个适配者；但可以通过``` Trait ```来减少单继承的限制。

(2)适配者类不能为最终类，如在PHP中不能为final类；

对象适配器模式的缺点如下：

与类适配器模式相比，要在适配器中置换适配者类的某些方法比较麻烦。如果一定要置换掉适配者类的一个或多个方法，可以先做一个适配者类的子类，将适配者类的方法置换掉，然后再把适配者类的子类当做真正的适配者进行适配，实现过程较为复杂。

### 7.适配器模式适用场景

在以下情况下可以考虑使用适配器模式：

(1)系统需要使用一些现有的类，而这些类的接口（如方法名）不符合系统的需要，甚至没有这些类的源代码。

(2) 想创建一个可以重复使用的类，用于与一些彼此之间没有太大关联的一些类，包括一些可能在将来引进的类一起工作。