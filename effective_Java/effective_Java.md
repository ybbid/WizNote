主要涉及：java.lang、java.util、java.util.concurrent、java.io
[TOC]
##第二章 创建和销毁对象
###第一条：考虑用静态工厂方法代替构造器（静态工厂方法不对应与工厂设计模式）
常用的创建对象的方法：构造器、静态工厂方法

```java
public static Boolean valueOf(boolean b){
        return b?Boolean.TRUE:Boolean.FALSE;
}
```
和构造器相比的优势

1. 有名称，方便调用已生成需要的对象（构造方法只能有一个名称）
2. 不必每次都生成一个新对象（可以实现实例缓存，从而提高性能）
3. 可以返回原类型的任何子类型（适用于基于接口的框架）

####面向接口编程
通过面向接口编程降低程序的耦合
假设程序中有个Comupter类需要组合一个输出设备，现在有两个选择：直接让Comupter该类组合一个Printer属性，或者让Comupter组合一个Output属性，那么到底采用哪种方式更好呢？
假设让Computer组合一个Printer属性，如果有一天系统需要重构，需要使用BetterPrinter来代替Printer，于是我们需要打开Computer类源代码进行修改。
为了避免这个问题，我们让Comupter组合一个Output属性，将Comupter类与Printer类完全分离。Computer对象实际组合的是Printer对象，还是BetterPrinter对象，对Computer而言完全透明。

```java
public class Computer
{
    private Output out;
    public Computer(Output out)
    {
        this.out = out;
    }
    //定义一个模拟获取字符串输入的方法
    public void keyIn(String msg)
    {
        out.getData(msg);
    }
    //定义一个模拟打印的方法
    public void print()
    {
        out.out();
    }
}
```

这样只需要让BetterPrinter/Printer实现Output接口，然后在创建Computer对象时指定out，无需修改Computer类。

```java
public class OutputFactory
{
    public Output getOutput()
    {
        return new Printer();
    }
    public static void main(String[] args)
    {
        OutputFactory of = new OutputFactory();//工厂创建Printer对象
        Computer c = new Computer(of.getOutput());
        c.keyIn("疯狂Java讲义");
        c.keyIn("轻量级J2EE企业应用实战");
        c.print();
    }
}
```

4.创建参数类型实例，代码更加简洁

```java
//采用默认构造器创建实例，
Map<String,List<String>> m=new HashMap<String,List<String>>();
//静态工厂，不需要重复声明参数
Map<String,List<String>> m=HashMap.newInstance();
public static <K,V> HashMap<K,V> newInstance(){
        return new HashMap<K,V>();
}
```

####缺点
类如果不含公有的或者受保护的构造器，就不能被子类化。
API文档中，静态工厂方法不会像构造器那样被明确标识。
*静态工厂方法惯用的名称

1、 valueOf，更多用来类型转换，例如 

```java
Integer c =Integer.valueOf(“123”);
```

2、 of，在Enumset中使用并流行

```java
//先定义一个枚举 
public enum Seasons { 
    AUTMN,WINTER,SPRING,SUMMER 
} 
EnumSet<Seasons> coldSeasons = EnumSet.of(Seasons.AUTMN, Seasons.WINTER);  
```

3、getInstance，返回的实例是通过参数描述的（实例和参数的类型不相同）

4、newInstance，类似于getInstance，但是newInstance返回的实例与其他实例不同

5、getType，类似于getInstance，但是getType在不同的类中。例如（在A类中有一个getType方法返回B类的实例）

6、newType，类似于newInstance，但是newType在不同的类中。
第二条：构造器的参数有多个时，考虑用构建器
静态工厂和构造器共同的局限：不能很好地扩展到大量的可选参数。
解决方法有，

 * 重叠构造器（第一个构造器只要必要的参数，第二个有一个可选参数。。。。。）

      
第三章 对于所有对象都通用的方法

第四章 类和接口

第五章 泛型

第六章 枚举与注解

第七章 方法

第八章 通用程序设计

第九章 异常

第十章 并发

第十一章 序列化
