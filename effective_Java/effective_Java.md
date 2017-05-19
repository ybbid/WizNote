主要涉及：java.lang、java.util、java.util.concurrent、java.io
[TOC]
##第二章 创建和销毁对象
#### 第一条：考虑用静态工厂方法代替构造器（静态工厂方法不对应与工厂设计模式）

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

##### 面向接口编程

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

#### 第二条：构造器的参数有多个时，考虑用构建器

静态工厂和构造器共同的局限：不能很好地扩展到大量的可选参数。
解决方法有，

*  重叠构造器（第一个构造器只要必要的参数，第二个有一个可选参数。。。。。）

   * 实例

   ```Java
   public class NutritionFacts {
       private final int servingSize; // (mL) required
       private final int servings; // (per container) required
       private final int calories; // optional
       private final int fat; // (g) optional

       public NutritionFacts(int servingSize, int servings) {
           this(servingSize, servings, 0);
       }

       public NutritionFacts(int servingSize, int servings, int calories) {
           this(servingSize, servings, calories, 0);
       }

       public NutritionFacts(int servingSize, int servings, int calories, int fat)       	{
           this.servingSize = servingSize;
           this.servings = servings;
           this.calories = calories;
           this.fat = fat;
       }
       
       public static void main(String[] args) {
           NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0);
       }
   }
   ```

   * 缺点
     * 随着参数的增加，构造方法失去控制
     * 代码难以阅读，无法直观地看出每个参数的含义

* JavaBean模式

   *  实例

      ```java
      public class NutritionFacts {
          // Parameters initialized to default values (if any)
          private int servingSize = -1; // Required; no default value
          private int servings = -1; // "     " "      "
          private int calories = 0;
          private int fat = 0;

          public NutritionFacts() {
          }

          // Setters
          public void setServingSize(int val) {
              servingSize = val;
          }

          public void setServings(int val) {
              servings = val;
          }

          public void setCalories(int val) {
              calories = val;
          }

          public void setFat(int val) {
              fat = val;
          }

          public static void main(String[] args) {
              NutritionFacts cocaCola = new NutritionFacts();
              cocaCola.setServingSize(240);
              cocaCola.setServings(8);
              cocaCola.setCalories(100);
          }
      }                           
      ```

   * 缺点

      * 构造过程中JavaBean处于不一致状态
      * 无法声明为final

* Builder模式（适用于参数多，且多个参数可选）

  * 实例

  ```java
  public class NutritionFacts {
    	//可以声明为final类型
      private final int servingSize;
      private final int servings;
      private final int calories;
      private final int fat;

      public static class Builder {
          // Required parameters
          private final int servingSize;
          private final int servings;

          // Optional parameters - initialized to default values
          private int calories = 0;
          private int fat = 0;

          public Builder(int servingSize, int servings) {
              this.servingSize = servingSize;
              this.servings = servings;
          }

          public Builder calories(int val) {
              calories = val;
              return this;
          }

          public Builder fat(int val) {
              fat = val;
              return this;
          }

          public NutritionFacts build() {
              return new NutritionFacts(this);
          }
      }

      private NutritionFacts(Builder builder) {
          servingSize = builder.servingSize;
          servings = builder.servings;
          calories = builder.calories;
          fat = builder.fat;
      }

      public static void main(String[] args) {
          NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                  .calories(100).build();
      }
  }
  ```

  * 优点
    * 可以对参数加约束条件，比如fat>=0，如果违反约束，可以抛出IllegalStateException
    * 一个Builder可以创建多个对象，比如增减序列号，用于计数
    * 与抽象工厂模式连用
    * 比JavaBean安全
  * 缺点
    * 创建对象，需要先创建Builder，增加开销

#### 第三条：私有构造器或枚举类型强化Singleton属性

* Java 1.5之前有两种方法实现单例模式

  * final静态成员

    * 实例

      ```Java
      public class Elvis {
          public static final Elvis INSTANCE = new Elvis();

          private Elvis() {
          }

          // This code would normally appear outside the class!
          public static void main(String[] args) {
              Elvis elvis = Elvis.INSTANCE;
          }
      }
      ```

  * 静态工厂方法

    * 实例

      ```Java
      public class Elvis {
          private static final Elvis INSTANCE = new Elvis();

          private Elvis() {
          }

          public static Elvis getInstance() {
              return INSTANCE;
          }

          public static void main(String[] args) {
              Elvis elvis = Elvis.getInstance();
          }
      }
      ```

    * 优点

      * 不改变API，可以改变为非Singleton

    * 序列化

      通常每次反序列化，都会创建一个新的实例

      正确的做法是：声明实例域为transient，并在类中加入readResolve方法

      ```java
      private Elvis readResolve(){
              return INSTANCE;
      }
      ```


* 第三种方法-单元素枚举（最佳）

  ```Java
  public enum Elvis {
      INSTANCE;

      // This code would normally appear outside the class!
      public static void main(String[] args) {
          Elvis elvis = Elvis.INSTANCE;
      }
  }
  ```

  * 优点
    * 简介，单个元素的枚举类型
    * 无偿提供序列化机制，防止多次实例化

#### 第四条：强化不可实例化的能力

* 静态工具类的作用（只包含静态方法和静态域）
  * 可以把某类特定数据上的方法组织起来，如Math类或者Arrays类
  * 实现某个接口的类的方法组织起来，如Collections类集合了很多List操作（List为接口）
  * final类的方法组织起来
* 不希望被实例化

声明构造器为私有，并在构造器内抛出异常，并加以注释

```java
// Noninstantiable utility class
public class UtilityClass {
	// Suppress default constructor for noninstantiability
	private UtilityClass() {
		throw new AssertionError();
	}
}
```

#### 第五条：避免创建不必要的对象

* 重用不可变的对象

  * 静态工厂方法（如Boolean.valueOf(String) ）常常优于构造器(Boolean(String) )

  ```java
  //每次都会创建新的实例
  String s1 = new String("stringgetter");
  //常量池重用
  String s2 = "stringgetter";
  ```

* 重用已知不会被修改的可变对象

  ```java
  //初始版本
  public class Person {
      private final Date birthDate;

      public Person(Date birthDate) {
          // Defensive copy - see Item 39
          this.birthDate = new Date(birthDate.getTime());
      }
      
      public boolean isBabyBoomer() {
          // 每次调用该方法都会创建boomStart/boomEnd实例
          Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
          gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
          Date boomStart = gmtCal.getTime();
          gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
          Date boomEnd = gmtCal.getTime();
          return birthDate.compareTo(boomStart) >= 0
                  && birthDate.compareTo(boomEnd) < 0;
      }
  }

  //改进版本，声明为final类型，在静态块中初始化
  class Person {
      private final Date birthDate;

      public Person(Date birthDate) {
          // Defensive copy - see Item 39
          this.birthDate = new Date(birthDate.getTime());
      }
      
      private static final Date BOOM_START;
      private static final Date BOOM_END;

      static {
          Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
          gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
          BOOM_START = gmtCal.getTime();
          gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
          BOOM_END = gmtCal.getTime();
      }

      public boolean isBabyBoomer() {
          return birthDate.compareTo(BOOM_START) >= 0
                  && birthDate.compareTo(BOOM_END) < 0;
      }
  }
  ```

* 自动装箱

  ```java
  public static void main(String []args)
  {
      Long sum = 0L;
    	//每次循环都会创建一个对象，因为Long为final
      for(long i = 0; i < Integer.MAX_VALUE ; i++){
          sum += i;
      }
  }
  ```


#### 第六条：清除过期的对象引用

* 内存泄漏问题

  某个类中保存了对象的引用（而不是对象本身），当对象inActive时，该类却没有及时清空对象引用。由于垃圾回收器并不知道该对象没用，导致其不会被回收。但是清空对象引用，应该被当成是一种例外情况（也就是说一般情况下，还是交由垃圾回收机制来做）。

* 缓存带来的内存泄漏

  * 由Key决定是否清除缓存

    WeakHashMap实现，WeakHashMap中保存了key的weakReference，在创建时传入ReferenceQueue，当key被回收后，key的weakReference加入队列。然后在Entry<K,V>的table中删除value值。

  * ​



第三章 对于所有对象都通用的方法

第四章 类和接口

第五章 泛型

第六章 枚举与注解

第七章 方法

第八章 通用程序设计

第九章 异常

第十章 并发

第十一章 序列化
