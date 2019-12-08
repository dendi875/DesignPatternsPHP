# 组合模式 (Composite Pattern)

> 作者：张权
>
> 创建日期：2018-07-22

---

### 0. 前言

树形结构在软件中随处可见，例如操作系统中的目录结构、应用软件中的菜单、办公系统中的公司组织结构等等，如何运用面向对象的方式来处理这种树形结构是组合模式需要解决的问题，组合模式通过一种巧妙的设计方案使得用户可以一致性地处理整个树形结构或者树形结构的一部分，也可以一致性地处理树形结构中的叶子节点（不包含子节点的节点）和树枝节点（包含子节点的节点）。

![tree](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/tree.png)

### 1. 模式定义

> 组合模式 (Composite Pattern)：组合多个对象形成树形结构以表示具有“整体—部分”关系的层次结构。组合模式对单个对象（即叶子对象）和组合对象（即树枝对象）的使用具有一致性，组合模式又可以称为“整体—部分”(Part-Whole)模式。

组合模式是一种**对象结构型模式**。

### 2. 组合模式结构

组合模式包含如下角色：

+ ``` Component ```：**抽象构件**
    + 它可以是接口或抽象类，为树叶构件和树枝构件对象声明接口，在该角色中可以包含所有子类共有行为的声明和实现。在抽象构件中定义了访问及管理它的子构件的方法，如增加子构件、删除子构件、获取子构件等。

+ ``` Leaf ```：**树叶构件**
    + 它在组合结构中表示叶子节点对象，叶子节点没有子节点，它实现了在抽象构件中定义的行为。对于那些访问及管理子构件的方法，可以通过异常等方式进行处理。

+ ``` Composite ```：**树枝构件**
    + 它在组合结构中表示树枝节点对象，树枝节点包含子节点，其子节点可以是叶子节点，也可以是树枝节点，它提供一个集合用于存储子节点，实现了在抽象构件中定义的行为，包括那些访问及管理子构件的方法，在其业务方法中可以递归调用其子节点的业务方法。

![Composite-Pattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/Composite-Pattern-0.jpg)

### 3. 组合模式应用实例

下面通过一个实例进一步学习和理解组合模式。

### 实例说明

在Dendi软件公司的内部办公系统OA系统中，有一个与公司组织结构对应的树形菜单，行政人员可以给各级单位下发通知，这些单位可以是总公司的一个部门，也可以是一个分公司，还可以是分公司的一个部门。用户只需要选择一个根节点即可实现通知的下发操作，而无须关心具体的实现细节。

```ssh
└── Dendi公司北京总部
    ├── 上海分公司
    │   ├── 上海分公司财务部
    │   ├── 上海分公司人力资源部
    │   └── 上海分公司研发部
    ├── 深圳分公司
    │   ├── 深圳分公司财务部
    │   ├── 深圳分公司人力资源部
    │   └── 深圳分公司研发部
    ├── 总公司财务部
    ├── 总公司人力资源部
    └── 总公司研发部
```

### 实例代码

* Units：``` 抽象构件 ```

``` php
/**
 * 抽象单位类：抽象构件
 * 这里单位即可以是某个部门，也可以某个公司
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class Units
{
    protected $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }

    /* 下发通知 */
    abstract public function sendNotify();
}
```

* Company： ``` 树枝构件角色 ```

``` php
/**
 * 公司类：树枝构件角色
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Company extends Units
{
    /**
     * 单位的集合，用于存储Units类型的成员
     *
     * @var array
     */
    private $unitList = [];

    public function add(Units $u)
    {
        $this->unitList[] = $u;
    }

    public function remove(Units $u)
    {
        $key = array_search($u, $this->unitList);
        if ($key !== false) {
            unset($this->unitList[$key]);
        }
    }

    public function getChild(int $i)
    {
        return $this->unitList[$i];
    }

    public function sendNotify()
    {
        printf("*****%s收到通知*****\n", $this->name);

        /* 递归调用子构件的sendNotify()方法 */
        foreach ($this->unitList as $unit) {
            $unit->sendNotify();
        }
    }
}
```

* ResearchDepartment、FinanceDepartment、HRDepartment： ``` 树叶构件角色 ```

``` php
/**
 * 研发部：树叶构件角色
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class ResearchDepartment extends Units
{
    public function sendNotify()
    {
        printf("-----%s收到通知-----\n", $this->name);
    }
}

/**
 * 财务部：树叶构件角色
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class FinanceDepartment extends Units
{
    public function sendNotify()
    {
        printf("-----%s收到通知-----\n", $this->name);
    }
}

/**
 * 人力资源部：树叶构件角色
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class HRDepartment extends Units
{
    public function sendNotify()
    {
        printf("-----%s收到通知-----\n", $this->name);
    }
}
```

* 客户端测试代码

``` php
/**
 * 客户端测试代码
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-07-22
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class Client
{
    public function run(): void
    {
        $rootCompany = new Company("Dendi公司北京总部");

        $shangHaiCompany = new Company("上海分公司");
        $shenZhenCompany = new Company("深圳分公司");
        $rootRD = new ResearchDepartment("总公司研发部");
        $rootFD = new FinanceDepartment("总公司财务部");
        $rootHRD = new HRDepartment("总公司人力资源部");

        $shangHaiRD = new ResearchDepartment("上海分公司研发部");
        $shangHaiFD = new FinanceDepartment("上海分公司财务部");
        $shangHaiHRD = new HRDepartment("上海分公司人力资源部");

        $shenZhenRD = new ResearchDepartment("深圳分公司研发部");
        $shenZhenFD = new FinanceDepartment("深圳分公司财务部");
        $shenZhenHRD = new HRDepartment("深圳分公司人力资源部");

        $shangHaiCompany->add($shangHaiRD);
        $shangHaiCompany->add($shangHaiFD);
        $shangHaiCompany->add($shangHaiHRD);

        $shenZhenCompany->add($shenZhenRD);
        $shenZhenCompany->add($shenZhenFD);
        $shenZhenCompany->add($shenZhenHRD);

        $rootCompany->add($rootRD);
        $rootCompany->add($rootFD);
        $rootCompany->add($rootHRD);
        $rootCompany->add($shangHaiCompany);
        $rootCompany->add($shenZhenCompany);

        //从"北京总部"节点开始下发通知
        $rootCompany->sendNotify();
    }
}

$c = new Client();
$c->run();

以上例程会输出：

*****Dendi公司北京总部收到通知*****<br/>
-----总公司研发部收到通知-----<br/>
-----总公司财务部收到通知-----<br/>
-----总公司人力资源部收到通知-----<br/>
*****上海分公司收到通知*****<br/>
-----上海分公司研发部收到通知-----<br/>
-----上海分公司财务部收到通知-----<br/>
-----上海分公司人力资源部收到通知-----<br/>
*****深圳分公司收到通知*****<br/>
-----深圳分公司研发部收到通知-----<br/>
-----深圳分公司财务部收到通知-----<br/>
-----深圳分公司人力资源部收到通知-----<br/>
```

### 4. UML类图

示例代码UML图：

![Composite-Pattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/Composite-Pattern-1.png)

### 5. 组合模式总结

* 组合模式的关键是定义了一个抽象构件类，它既可以代表叶子，又可以代表树枝，而客户端针对该抽象构件类进行编程，无须知道它到底表示的是叶子还是树枝，可以对其进行统一处理。同时树枝对象与抽象构件类之间还建立一个聚合关联关系，在树枝对象中既可以包含叶子，也可以包含树枝，以此实现递归组合，形成一个树形结构。

* 在使用组合模式时，根据抽象构件类的定义形式，我们可将组合模式分为透明组合模式和安全组合模式两种形式：

(1)透明组合模式
透明组合模式中，抽象构件Component中声明了所有用于管理成员对象的方法，包括add()、remove()以及getChild()等方法，这样做的好处是确保所有的构件类都有相同的接口。在客户端看来，叶子对象与树枝对象所提供的方法是一致的，客户端可以相同地对待所有的对象。透明组合模式也是组合模式的标准形式，因为在叶子构件中需要实现在抽象构件类中声明的所有方法，包括业务方法以及管理和访问子构件的方法，但是叶子构件不能再包含子构件，因此在叶子构件中实现子构件管理和访问方法时需要提供异常处理或错误提示。

(2)安全组合模式
安全组合模式中，在抽象构件Component中没有声明任何用于管理成员对象的方法，而是在Composite类中声明并实现这些方法。这种做法是安全的，因为根本不向叶子对象提供这些管理成员对象的方法，对于叶子对象，客户端不可能调用到这些方法。

安全组合模式的缺点是不够透明，因为叶子构件和树枝构件具有不同的方法，且树枝构件中那些用于管理成员对象的方法没有在抽象构件类中定义，因此客户端不能完全针对抽象编程，必须有区别地对待叶子构件和树枝构件。在实际应用中，安全组合模式的使用频率也非常高。

### 6. 组合模式的优点

组合模式的主要优点如下：

(1)组合模式可以清楚地定义分层次的复杂对象，表示对象的全部或部分层次，它让客户端忽略了层次的差异，方便对整个层次结构进行控制。

(2)客户端可以一致地使用一个组合结构或其中单个对象，不必关心处理的是单个对象还是整个组合结构，简化了客户端代码。

(3)在组合模式中增加新的树枝构件和叶子构件都很方便，无须对现有类库进行任何修改，符合“开闭原则”。

(4)组合模式为树形结构的面向对象实现提供了一种灵活的解决方案，通过叶子对象和树枝对象的递归组合，可以形成复杂的树形结构，但对树形结构的控制却非常简单。

### 7. 组合模式的缺点

组合模式的主要缺点如下：

在增加新构件时很难对树枝构件中的构件类型进行限制。有时候我们希望一个树枝构件中只能有某些特定类型的对象，例如在某个文件夹中只能包含文本文件，使用组合模式时，不能依赖类型系统来施加这些约束，因为它们都来自于相同的抽象层，在这种情况下，必须通过在运行时进行类型检查来实现，这个实现过程较为复杂。


### 8.组合模式适用场景

在以下情况下可以考虑使用组合模式：

(1)在具有整体和部分的层次结构中，希望通过一种方式忽略整体与部分的差异，客户端可以一致地对待它们。

(2)在一个使用面向对象语言开发的系统中需要处理一个树形结构。

(3)在一个系统中能够分离出叶子对象和树枝对象，而且它们的类型不固定，需要增加一些新的类型。
