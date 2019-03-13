## Drools(BRMS) 速成教程(上）

大家在日常开发中，肯定遇到过一些业务规则变来变去的需求，比如：会员积分系统（今天要新注册会员送10积分，明天要改成注册送优惠券，后天搞活动要改成注册自动变成高级会员...），此类需求，一般都是通过写if分支来实现的，参考下面：

```java
if (规则条件1){
   //处理1
}
else if (规则条件2){
   //处理2
}
else if (规则条件3){
   //处理3
}
...
```

这种代码毫无营养，而且很枯燥，有没有办法，将业务规则从代码中抽离出来，以后规则变了，不用改代码，只改规则配置就行？

今天要介绍的[Drools](http://drools.org/)，可以很好的解决此类问题，Drools是一个业务规则管理的开源框架，现在归到[jboss](http://www.jboss.org/)旗下，本文将介绍一些基本的用法，方便大家快速上手。

### 1.添加依赖项

```xml
<properties>
    <drools.version>6.5.0.Final</drools.version>
    <lombok.version>1.18.2</lombok.version>
</properties>
 
<dependencies>
    <dependency>
        <groupId>org.drools</groupId>
        <artifactId>drools-compiler</artifactId>
        <version>${drools.version}</version>
    </dependency>
 
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
 
    </dependency>
</dependencies>
```

注：不同版本的drools，api有较大差异，本文采用6.5.0.Final版本，其它版本的用法请自行参考官方文档。(lombok是可选的，建议加上，简化java代码书写）

### 2.新建一个演示用的pojo类Message

```java
package com.cnblogs.yjmyzz.drools.demo.model;
 
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.Setter;
 
@Setter
@Getter
@AllArgsConstructor
public class Message {
 
    public enum MessageType {
        HI,
        GOODBYE,
        CHAT
    }
 
    private MessageType messageType;
    private String target;
 
}　　
```

很简单，不用多说，我们要模拟的场景是针对不同的messageType及target（也就是不同的业务规则 ），代码能做出不同的处理。

### 3.编写业务规则drl文件

drl 是drools rule的缩写，大概长这个样子：（规则文件一般放在resources资源目录或下面的子目录中），将下面的内容保存在hello.drl中

```java
package com.cnblogs.yjmyzz.drools;
 
import com.cnblogs.yjmyzz.drools.demo.model.Message;
import java.util.concurrent.atomic.AtomicInteger;
 
global String temp;
global AtomicInteger count;
 
//函数示例
function void print(String messgae){
    System.out.println(messgae);
}
 
//规则1
rule "say-hi"
    when
        $message: Message(Message.MessageType.HI.equals(messageType) && target!=null)
    then
        print("hi," + $message.getTarget() + ", welcome to drools\n");
    end

```

这里面可以分成几个部分：

#### 3.1 package部分

这个是用来管理包的，跟java的package概念类似，多个rule文件时，可以按包来管理rule代码。

#### 3.2 import

drl 规则文件中，可以直接使用java定义好的类，只需要import进来就好。

#### 3.3 global

相当于全局变量声明，多个规则文件中可共享该变量（后面会演示这一用法），要注意的是：共享全局变量建议不要用Integer这种"简单"类型，这样无法在规则文件中修改变量的"值"，建议用复杂类型（比如上面的AtomicInteger）

#### 3.4 function

即：函数，可以定义一些共用函数，在本drl文件被其它规则共用。

#### 3.5 rule ... when ... then ... end

这个就是真正的规则了，rule后面的"say-hi"为规则名称，when后面的相当于判断条件（注：声明条件的同时，还能声明所谓fact"变量"-[不太准确，暂且这样叫吧]，message:Message(...)这里就相当于把后面一串东西，保存在message:Message(...)这里就相当于把后面一串东西，保存在message这个fact"变量中）

**小结一下：上面这个规则，相当于，如果Message的实例，其messageType为HI，且target值不为空，就打印输出一句话。**

很简单吧，我们再加点难度，多加几个规则 ：

```java
package com.cnblogs.yjmyzz.drools;
 
import com.cnblogs.yjmyzz.drools.demo.model.Message;
import java.util.concurrent.atomic.AtomicInteger;
 
global String temp;
global AtomicInteger count;
 
//函数示例
function void print(String messgae){
    System.out.println(messgae);
}
 
//规则1
rule "say-hi"
    when
        $message: Message(Message.MessageType.HI.equals(messageType) && target!=null)
    then
        print("hi," + $message.getTarget() + ", welcome to drools\n");
    end
 
rule "say-goodbye"
    when
        $message: Message(Message.MessageType.GOODBYE.equals(messageType) && target!=null)
    then
        print("bye bye ," + $message.getTarget() + "\n");
    end
 
rule "chat-and-goodbye"
    when
        $message: Message(Message.MessageType.CHAT.equals(messageType) && target!=null)
    then
        print($message.getTarget() +  ", nice to meet you. But I have to go.");
        //将MessageType设置成GOODBYE
        $message.setMessageType(Message.MessageType.GOODBYE);
        //更新fact,以便触发规则"say-goodbye"
        update($message);
    end
 
rule "give-me-money"
    salience -1 //规则触发的优先级，值越大，越先触发
    when
        $message: Message(target.equals("beggar"))
    then
        print("5毛拿好");
    end
 
rule "give-me-rice"
    salience 1
    when
        $message: Message(target.equals("beggar"))
    then
        print("给你个包子吧");
    end
 
//本规则的效果：如果target="loop"，会循环触发，真到10次后停下
rule "loop"
//    no-loop  //加上这行后，将禁止循环触发
    when
        $message: Message(target.equals("loop") && count.get()<10)
    then
        print("\n我会每隔1秒触发，10次后停止！" + count.addAndGet(1));
        Thread.sleep(1000);
        update($message)
    end
```

解释下：

a: "chat-and-goodbye" 这条规则，如果messageType=CHAT，会修改message.messageType为GOODBYE，然后update(message.messageType为GOODBYE，然后update(mesage)，相当于修改了Message实例后，会重新匹配say-goodbye规则

b："give-me-money"、"give-me-rice" 这二个规则设置了salience，其实就是优先级，值越大，该规则越优先匹配。

c: "loop" 最后一条规则，这里用到了一个全局变量count，每次该规则且匹配到以后，计数器+1，然后再update，又匹配到本条规则，最终规则就是循环触发10次。

 

一个项目里，可以同时有多个规则文件，还可以再加一个hello2.drl，演示共享变量

```java
package com.cnblogs.yjmyzz.drools;
 
import com.cnblogs.yjmyzz.drools.demo.model.Message;
 
rule "global-demo"
    salience -99
    when
        $message: Message(target.equals("beggar"))
    then
        System.out.println(temp);
    end
```

这里打印了共享变量temp(前提是target="begger")

### 4.resource/META-INF里放置kmodule.xml文件

内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kmodule xmlns="http://jboss.org/kie/6.0.0/kmodule">
    <kbase name="hello" packages="hello">
        <ksession name="ksession-hello"/>
    </kbase>
</kmodule>
```

这个文件的主要作用之一，是在运行时，让drools知道加载哪些drl文件。注意：这里packages="hello"，就表示加载classpath:resources/hello下的drl文件。

最后项目的文件结构类似这样：

![img](https://images2018.cnblogs.com/blog/27612/201808/27612-20180826222821347-717664051.png) 

### 5.运行

HelloApp内容如下：

```java
package com.cnblogs.yjmyzz.drools.demo;
 
import com.cnblogs.yjmyzz.drools.demo.model.Message;
import org.kie.api.KieServices;
import org.kie.api.runtime.KieContainer;
import org.kie.api.runtime.KieSession;
 
import java.util.concurrent.atomic.AtomicInteger;
 
public class HelloApp {
 
 
    public static void main(String[] args) {
        KieContainer kContainer = null;
        try {
            KieServices ks = KieServices.Factory.get();
            kContainer = ks.getKieClasspathContainer();
            KieSession kSession = kContainer.newKieSession("ksession-hello");
 
            Message message1 = new Message(Message.MessageType.HI, "杨过");
            kSession.insert(message1);
            kSession.fireAllRules();
 
            Message message2 = new Message(Message.MessageType.GOODBYE, "姑姑");
            kSession.insert(message2);
            kSession.fireAllRules();
 
            Message message3 = new Message(Message.MessageType.CHAT, "美羊羊");
            kSession.insert(message3);
            kSession.fireAllRules();
 
            Message message4 = new Message(null, "beggar");
            kSession.setGlobal("temp", "我是谁？我在哪？我要干什么？");
            kSession.insert(message4);
            kSession.fireAllRules();
 
            Message message5 = new Message(null, "loop");
            kSession.setGlobal("count", new AtomicInteger(0));
            kSession.insert(message5);
            kSession.fireAllRules();
 
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (kContainer != null) {
                kContainer.dispose();
            }
        }
 
    }
}
```

注意下共享变量，即：message4,message5部分，一般是在规则触发前提前把共享变量先设置好初始值，最终输出如下：

```java
hi,杨过, welcome to drools //规则：say-hi
 
bye bye ,姑姑 //规则：say-goodbye
 
美羊羊, nice to meet you. But I have to go. //规则：chat-and-goodbye
bye bye ,美羊羊 //规则: say-goodbye(2次匹配成功）
 
给你个包子吧 //规则：give-me-rice
5毛拿好 //规则：give-me-money
我是谁？我在哪？我要干什么？//hello2.drl中的规则"global-demo"
 
我会每隔1秒触发，10次后停止！1 //规则:loop循环10次
 
我会每隔1秒触发，10次后停止！2
 
我会每隔1秒触发，10次后停止！3
 
我会每隔1秒触发，10次后停止！4
 
我会每隔1秒触发，10次后停止！5
 
我会每隔1秒触发，10次后停止！6
 
我会每隔1秒触发，10次后停止！7
 
我会每隔1秒触发，10次后停止！8
 
我会每隔1秒触发，10次后停止！9
 
我会每隔1秒触发，10次后停止！10
```

## 参考文章

[小明历险记：规则引擎drools教程一](https://www.cnblogs.com/ityouknow/archive/2017/08/07/7297524.html) 
[drools 6.5.0官方文档](http://docs.jboss.org/drools/release/6.5.0.Final/drools-docs/html_single/) 
[Learn Drools: Part I ](https://dzone.com/articles/learn-drools-part-1-1)
[Learn Drools: Part II (Cross Product) ](https://dzone.com/articles/learn-drools-part-ii-cross-product)
[Learn Drools: Part III (Filter Facts)](https://dzone.com/articles/learn-drools-part-iii-filter-facts) 
[Learn Drools (Part 4): Inferences](https://dzone.com/articles/learn-drools-part-4-inferences) 
[Learn Drools (Part 5): Truth Maintenance](https://dzone.com/articles/learn-drools-part-5-truth-maintenance) 
[Learn Drools (Part 6): Rules and Statistics](https://dzone.com/articles/learn-drools-part-vi-rules-and-statistics) 
[Learn Drools (Part 7): Salience](https://dzone.com/articles/learn-drools-part-7-salience-1) 
[Drools语法篇之Global全局变量](https://blog.csdn.net/u013115157/article/details/52538370)
[《Drools7.0.0.Final规则引擎教程》第4章 global全局变量](https://blog.csdn.net/wo541075754/article/details/77096193)

附：文件[示例代码](https://github.com/yjmyzz/drools-helloworld)drools-helloworld可从github下载

> 作者：[菩提树下的杨过](http://yjmyzz.cnblogs.com/)
> 出处：[http://yjmyzz.cnblogs.com](http://yjmyzz.cnblogs.com/) 
> 本文版权归作者和博客园共有，欢迎转载，但未经作者同意必须保留此段声明，且在文章页面明显位置给出原文连接，否则保留追究法律责任的权利。