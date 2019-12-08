# 代理模式 (Proxy Pattern)

> 作者：张权
>
> 创建日期：2018-04-14 15:32:28

---

### 0、模式动机

    在某些情况下，客户端不想或者不能直接引用一个对象，此时可以通过一个称之为“代理”的第三者来实现间接引用。代理对象可以在客户端和目标对象之间起到中介的作用，并且可以通过代理对象去掉客户不能看到的内容和服务或者添加客户需要的额外服务。

### 1. 模式定义

代理模式 (Proxy Pattern)：它属于对象**结构型模式**。给某一个对象提供一个代理，并由代理对象控制对这个对象的访问。

### 2. 模式结构

代理模式包含如下角色：

+ ``` Subject ```：**抽象主题角色**
    + 定义了RealSubject和Proxy的共用接口，这样就可以在任何使用RealSubject的地方使用Proxy。

+ ``` RealSubject ```：**真实主题角色**
    + 定义了代理角色所代表的真实对象，在真实主题角色中实现了真实的业务操作，客户端可以通过代理主题角色间接调用真实主题角色中定义的方法。

+ ``` Proxy ```: **代理主题角色**
    + 代理主题角色内部包含对真实主题的引用，从而可以在任何时候操作真实主题对象。

![ProxyPartten-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/ProxyPartten-0.png)

### 3. 代理模式的种类

* 虚拟代理：把一些开销很大的对象延迟到需要它的时候才创建。当还没使用一个类的实例时，先实例化的是代理类，然后在真正需要使用的时候才去实例化真实的类；把一些开销很大的对象延迟到需要它的时候才创建。

* 动态代理：动态代理是指不必为每一个真实的服务写一个代理类，而是只写一个类，通常可以通过魔术方法``` __call ```或``` __callStatic ```来实现动态代理。

### 4. 代码示例

下面以一个延迟加载图片数据的例子来说明下虚拟代理。

* Image：抽象主题类

``` php
/**
 * 抽象图片类，定义了RealImage和ProxyImage的共用接口。
 * 客户端只依赖于这个抽象类。
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-04-14 14:03:41
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

abstract class Image
{
    protected $width;
    protected $height;
    protected $path;

    abstract public function getWidth();

    abstract public function getHeight();

    abstract public function getPath();

    abstract public function dump();
}
```

* RealImage：真实主题类

``` php
/**
 *  真实图片类，它总是加载图片数据，即使不需要图片数据时也加载
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-04-14 16:06:55
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class RealImage extends Image
{
    protected $data;

    public function __construct($path)
    {
        $this->path = $path;
        list($this->width, $this->height) = getimagesize($path);
        $this->data = file_get_contents($path);
    }

    public function getWidth()
    {
        return $this->width;
    }

    public function getHeight()
    {
        return $this->height;
    }

    public function getPath()
    {
        return $this->path;
    }

    public function dump()
    {
        return $this->data;
    }
}
```

* ProxyImage：代理主题类

``` php
/**
 * 代理图片类，它延迟了图片数据的加载，真正需要图片数据时候才加载
 *
 * @author     <dendi875@163.com>
 * @createDate DataTime
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class ProxyImage extends Image
{
    private $realImage = null;

    public function __construct($path)
    {
        $this->path = $path;
        list($this->width, $this->height) = getimagesize($path);
    }

    public function getWidth()
    {
        return $this->width;
    }

    public function getHeight()
    {
        return $this->height;
    }

    public function getPath()
    {
        return $this->path;
    }

    public function dump()
    {
        $this->lazyLoad();
        return $this->realImage->dump();
    }

    private function lazyLoad()
    {
        if ($this->realImage === null) {
            $this->realImage = new RealImage($this->path);
        }
    }
}
```

### 5. 客户端的使用

```php
$path = 'https://www.testvm.dev/php/oop/design-pattern/Proxy-Pattern/zq.jpg';
$client = new Client();

$image = new RealImage($path);
echo $client->tag($image), "\n";
```
> 使用真实图片类时的响应时间

![time-real](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/time-real.png)

```php
$path = 'https://www.testvm.dev/php/oop/design-pattern/Proxy-Pattern/zq.jpg';
$client = new Client();

$proxy = new ProxyImage($path);
echo $client->tag($proxy), "\n";
```
> 使用代理图片类时的响应时间

![time-proxy](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/time-proxy.png)

> 分析

因为初始化``` RealImage ```类时每次都要通过``` file_get_contents ```去加载图片的数据，但我们客户端只需要图片的``` width ```、``` height  ```、``` path ```，而使用``` ProxyImage ```时不必每次耗费很多时间去初始化``` RealImage ```当真正需要图片数据时才去初始化``` RealImage```并``` dump ``` ，这样将耗费资源多的方法使用代理进行分离，可以加快系统的启动速度，减少用户等待的时间。


### 6. UML类图

代理模式实现的图片数据延迟加载示例代码UML图：

![ProxyPartten-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/ProxyPartten-1.png)

### 7. 动态代理

PHP中一般通过魔术方法``` __call ```或```__callStatic```来实现动态代理

核心代码如下：

```php
<?php
class GenericProxy
{
    protected $subject;

    public function __construct($subject)
    {
        $this->subject = $subject;
    }

    public function __call($method, $args)
    {
        return call_user_func_array(array($this->subject, $method), $args);
    }
}
```
