
枚举是实例个数有限的类，不能像其他普通类那样随意生成任意数量的实例。

## 一、JDK5.0之前的枚举定义步骤和思路

枚举实例只能是有限的几个，不能随意生成对象。

（1）要达到限制某个类只能生成有限的对象这一目的，可以限制其构造器的访问权限，即不能被外部其他类访问，即使用 `private`修饰构造器（示例代码6~9行）；

（2）另外，枚举实例是有限的固定对象，其成员变量也不应该被任何对象更改，因此成员变量使用`private`（防止外部对象修改）、`final`（赋值后防止被枚举内部修改）修饰（示例代码3~4行）；

（3）虽不能修改，但其属性应当可以读取，故提供相应属性对应的`getter`方法（示例代码11~17行）；

（4）因`private`修饰构造器的原因，只能在类的内部预先定义好对应的实例（示例代19~22行）；又因为在内部生成的枚举实例需供外部使用，所以生成的实例用`public`修饰；因使用 `private`修饰构造器导致只能通过类名访问枚举实例（因无法实例化，也只能通过类名访问），所以用`static`修饰；又为了防止外部通过`Season.SPRING = null`使枚举实例为空，所有用`final`修饰，综上，就有了24~27行的 `public static final Season SPRING`。

```java
// JDK 5.0 之前枚举定义方式
class SeasonBefore5{
    private final String seasonName; // 季节名称
    private final String seasonDesc; // 季节描述

    private SeasonBefore5(String seasonName, String seasonDesc){
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }

    @Override
    public String toString() {
        return "Season [seasonName=" + seasonName + ", seasonDesc=" + seasonDesc + "]";
    }

    public static final SeasonBefore5 SPRING = new SeasonBefore5("春天", "春暖花开");
    public static final SeasonBefore5 SUMMER = new SeasonBefore5("夏天", "夏日炎炎");
    public static final SeasonBefore5 AUTUMN = new SeasonBefore5("秋天", "秋高气爽");
    public static final SeasonBefore5 WINTER = new SeasonBefore5("冬天", "白雪皑皑");
}
```

运行结果：

![image-20230818183050932](https://gitee.com/litan33/image-host/raw/master/img/image-20230818183050932.png)

## 二、JDK 5.0 中枚举定义步骤

使用`enum`关键字定义，如下：

```java
enum SeasonIn5{

    SPRING("春天", "春暖花开"),// 注意枚举实例之间是用逗号相隔
    SUMMER("夏天", "夏日炎炎"),
    AUTUMN("秋天", "秋高气爽"),
    WINTER("冬天", "白雪皑皑");

    private final String seasonName; // 季节名称
    private final String seasonDesc; // 季节描述

    private SeasonIn5(String seasonName, String seasonDesc){
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }

    @Override
    public String toString() {
        return "SeasonIn5 [seasonName=" + seasonName + ", seasonDesc=" + seasonDesc + "]";
    }
}
```

使用`enum`定义的枚举类继承于java内部类`Enum`，如：

![image-20230818185935715](https://gitee.com/litan33/image-host/raw/master/img/image-20230818185935715.png)

## 三、Enum常用方法

### 3.1 toString()

`String toString()`: 默认返回的是常量名（对象名），可以继续手动重写该方法。

### 3.2 values()

`static Enum[] values()`：返回枚举类型的对象数组。该方法可以很方便地遍历所有的枚举值，是一个静态方法。

![image-20230818190244580](https://gitee.com/litan33/image-host/raw/master/img/image-20230818190244580.png)

### 3.3 valueOf()

`static Enum valueOf(String name)`：可以把一个字符串转为对应的枚举类对象。要求字符串必须是枚举类对象的“名字”。如不是，会有运行时异常：`IllegalArgumentException`。

![image-20230818190647214](https://gitee.com/litan33/image-host/raw/master/img/image-20230818190647214.png)

### 3.4 name()

`String name()`:得到当前枚举常量的名称。建议优先使用toString()。

###  3.5 ordinal()

`int ordinal()`:返回当前枚举常量的次序号，默认从0开始。



