# 外观模式 (Facade Pattern)

> 作者：张权
>
> 创建日期：2018-05-05

---

### 0. 外观模式概述

不知道大家有没有比较过自己泡茶和去茶馆喝茶的区别，如果是自己泡茶需要自行准备茶叶、茶具和开水，如图(A)所示，而去茶馆喝茶，最简单的方式就是跟茶馆服务员说想要一杯什么样的茶，是铁观音、碧螺春还是西湖龙井？正因为茶馆有服务员，顾客无须直接和茶叶、茶具、开水等交互，整个泡茶过程由服务员来完成，顾客只需与服务员交互即可，整个过程非常简单省事，如图(B)所示。

![tea](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/tea.jpg)

在软件开发中，有时候为了完成一项较为复杂的功能，一个客户端类需要和多个业务类交互，而这些需要交互的业务类经常会作为一个整体出现，由于涉及到的类比较多，导致使用时代码较为复杂，此时，特别需要一个类似服务员一样的角色，由它来负责和多个业务类进行交互，而客户端类只需与该类交互。外观模式通过引入一个新的外观类(Facade)来实现该功能，外观类充当了软件系统中的“服务员”，它为多个业务类的调用提供了一个统一的入口，简化了类与类之间的交互。在外观模式中，那些需要交互的业务类被称为子系统(Subsystem)。如果没有外观类，那么每个客户端类需要和多个子系统之间进行复杂的交互，系统的耦合度将很大；而引入外观类之后，客户端类只需要直接与外观类交互，客户端类与子系统之间原有的复杂引用关系由外观类来实现，从而降低了系统的耦合度。


### 1. 模式定义

> 外观模式 (Facade Pattern)：为子系统中的一组接口提供一个统一的入口。外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

外观模式又称为门面模式，它是一种**对象结构型模式**。

外观模式是**迪米特法则**的一种具体实现，通过引入一个新的外观角色可以降低原有系统的复杂度，同时降低客户端类与子系统的耦合度。


### 2. 模式结构

外观模式包含如下角色：

+ ``` Facade ```：**外观**
    + 客户端可以调用它的方法，在外观角色中可以知道相关的（一个或者多个）子系统的功能和责任；在正常情况下，它将所有从客户端发来的请求委派到相应的子系统去，传递给相应的子系统对象处理。

+ ``` SubSystem ```：**子系统**
    + 在软件系统中可以有一个或者多个子系统角色，每一个子系统可以不是一个单独的类，而是一个类的集合，它实现子系统的功能；每一个子系统都可以被客户端直接调用，或者被外观角色调用，它处理由外观类传过来的请求；子系统并不知道外观的存在，对于子系统而言，外观角色仅仅是另外一个客户端而已。

![FacadePattern-0](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/FacadePattern-0.png)

### 3. 模式实现

* 外观模式的主要目的在于降低系统的复杂程度，在面向对象软件系统中，类与类之间的关系越多，不能表示系统设计得越好，反而表示系统中类之间的耦合度太大，这样的系统在维护和修改时都缺乏灵活性，因为一个类的改动会导致多个类发生变化，而外观模式的引入在很大程度上降低了类与类之间的耦合关系。引入外观模式之后，增加新的子系统或者移除子系统都非常方便，客户端类无须进行修改（或者极少的修改），只需要在外观类中增加或移除对子系统的引用即可。从这一点来说，外观模式在一定程度上并不符合开闭原则，增加新的子系统需要对原有系统进行一定的修改，虽然这个修改工作量不大。

* 外观模式中所指的子系统是一个广义的概念，它可以是一个类、一个功能模块、系统的一个组成部分或者一个完整的系统。子系统类通常是一些业务类，实现了一些具体的、独立的业务功能。

### 4. 外观模式应用实例

下面通过一个实例进一步学习和理解外观模式。

### 实例说明

现在需要开发一个文件加密功能，该功能可以对文件中的数据进行加密并将加密之后的数据存储在一个新文件中，具体的流程包括三个部分，分别是读取源文件、加密、保存加密之后的文件。这三个操作相对独立，为了实现代码的独立重用，让设计更符合**单一职责原则**，这三个操作的业务代码封装在三个不同的类中。

现使用外观模式设计该文件加密功能。

### 实例代码

* EncryptFacade：``` 外观类 ```

``` php
/**
 * EncryptFacade 加密外观类。
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-05-05
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class EncryptFacade
{
    /**
     * @var FileReader
     */
    private $fileReader;

    /**
     * @var CipherMachine
     */
    private $cipherMachine;

    /**
     * @var FileWriter
     */
    private $fileWriter;

    public function __construct()
    {
        $this->fileReader = new FileReader();
        $this->cipherMachine = CipherMachine::getInstance();
        $this->fileWriter = new FileWriter();
    }

    public function fileEncrypt(string $fileNameSrc, string $fileNameDes): void
    {
        /* 读取文件，获取明文 */
        $plainStr = $this->fileReader->read($fileNameSrc);
        /* 数据加密，将明文转换为密文 */
        $encryptStr = $this->cipherMachine->encrypt($plainStr);
        /* 保存密文，写入文件 */
        $this->fileWriter->write($encryptStr, $fileNameDes);
    }
}
```

* FileReader、CipherMachine、FileWriter： ``` 子系统类 ```

``` php
/**
 * FileReader 文件读取类，充当子系统类。
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-05-05
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class FileReader
{
    public function read(string $fileNameSrc): ?string
    {
        $str = null;
        try {
            if ($stream = fopen($fileNameSrc, 'r')) {
                $str = stream_get_contents($stream);
                fclose($stream);
            }
        } catch(Exception $ex) {
            $str = null;
        }
        return $str;
    }
}


/**
 * CipherMachine 数据加密类，充当子系统类。
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-05-05
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class CipherMachine
{
    private $iv = '';

    private static $instance;

    private function __construct()
    {
        $ivlen = openssl_cipher_iv_length('aes-256-ctr');
        $iv = openssl_random_pseudo_bytes($ivlen);
        $this->iv = $iv;
    }

    public static function getInstance(): self
    {
        if (!(self::$instance instanceof self)) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    public function encrypt(string $plainText): string
    {
        return openssl_encrypt($plainText, 'aes-256-ctr', 'secret', 0, $this->iv);
    }

    public function decrypt(string $ciphertext): string
    {
        return openssl_decrypt($ciphertext, 'aes-256-ctr', 'secret', 0, $this->iv);
    }
}


/**
 * FileWriter 文件保存类，充当子系统类。
 *
 * @author     <dendi875@163.com>
 * @createDate 2018-05-05
 * @copyright  Copyright (c) 2018 guanaitong.com
 */

class FileWriter
{
    public function write(string $ciphertext, string $fileNameDes): void
    {
        $handle = fopen($fileNameDes, 'wb');
        fwrite($handle, $ciphertext);
        fclose($handle);
    }
}
```

* 加密文件``` src.txt ```内容为

```ssh
[root@localhost Facade-Pattern]# echo 'hello,world!' >  src.txt
```

* 客户端测试

```php
$encryptFacade = new EncryptFacade();
$encryptFacade->fileEncrypt('src.txt', 'des.txt');
```

* 加密后的``` des.txt ```内容为

```ssh
[root@localhost Facade-Pattern]# cat des.txt
pw2x3UEeO8GVjum36A==
```

### 5. UML类图

示例代码UML图：

![FacadePattern-1](https://github.com/dendi875/images/blob/master/Design%20Patterns(%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)/FacadePattern-1.png)

### 6. 外观模式的优点

外观模式的主要优点如下：

(1)它对客户端屏蔽了子系统组件，减少了客户端所需处理的对象数目，并使得子系统使用起来更加容易。通过引入外观模式，客户端代码将变得很简单，与之关联的对象也很少。

(2)它实现了子系统与客户端之间的松耦合关系，这使得子系统的变化不会影响到调用它的客户端，只需要调整外观类即可。

(3)一个子系统的修改对其他子系统没有任何影响，而且子系统内部变化也不会影响到外观对象。

### 7. 外观模式的缺点

外观模式的主要缺点如下：

(1)不能很好地限制客户端直接使用子系统类，如果对客户端访问子系统类做太多的限制则减少了可变性和灵活性。

(2)增加新的子系统可能需要修改外观类的源代码，违背了**开闭原则**。

### 8. 外观模式适用场景

在以下情况下可以考虑使用外观模式：

(1)当要为访问一系列复杂的子系统提供一个简单入口时可以使用外观模式。

(2)客户端程序与多个子系统之间存在很大的依赖性。引入外观类可以将子系统与客户端解耦，从而提高子系统的独立性和可移植性。

(3)在层次化结构中，可以使用外观模式定义系统中每一层的入口，层与层之间不直接产生联系，而通过外观类建立联系，降低层之间的耦合度。