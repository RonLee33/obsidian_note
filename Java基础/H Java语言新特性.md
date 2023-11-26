# 一、Lambda表达式

## 1.1 概述

lambda表达式是一种对象，应用于函数式接口（只有一个抽象方法的接口），通过省略函数式接口的声明和其唯一的抽象方法的方法名，用其唯一抽象方法的形参列表和方法体来表示该函数式接口，运行的实际内容和对象并未发生变化，只是书写方式更为简略，是一种==语法糖==。

以下是使用Lambda的简单比对：
```java
// Raw
Comparator<Integer> c1 = new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return Integer.compare(o1, o2);
    }
};

// Lambda
Comparator<Integer> c1 = (Integer o1, Integer o2) -> {
    return Integer.compare(o1, o2);
};
```

简记图：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231126162011.png)

## 1.2 Lambda表达式的几种形式

```java
public static void demo2(){
    // 有入参，有返回值，且方法体只有一句语句时，return 可省略，Lambda
    Comparator<Integer> c1 = new Comparator<Integer>() {
        @Override
        public int compare(Integer o1, Integer o2) {
            return Integer.compare(o1, o2);
        }
    };

    System.out.println("无Lambda：" + c1.compare(12, 21));

    Comparator<Integer> c11 = (Integer o1, Integer o2) -> {
        return Integer.compare(o1, o2);
    }
    // Lambda 不省略返回值的语句,
    // Lambda 入参只有一个时，圆括号与形参的类型声明可省略，
    // 借助Java类型推断，编译器可推断出对应的类型
    // 另外，当入参只有一个时，连形参列表的括号也可省略
    Comparator<Integer> c2 = (o1, o2) -> {
        return Integer.compare(o1, o2);
    };
    System.out.println("不省略return的Lambda：" + c2.compare(12, 21));

    // Lambda 省略返回值的语句
    Comparator<Integer> c3 = (o1, o2) -> Integer.compare(o1, o2);
    System.out.println("省略return的Lambda：" + c3.compare(12, 21));

    // Lambda的终极简写方式：方法引用
    Comparator<Integer> c4 = Integer::compare;
    System.out.println("Lambda的终极简写方式：方法引用：" + c4.compare(12, 21));
}
```

## 1.3 方法引用

使用方法引用的前提：函数式接口抽象方法（称为A）在实现时其实现体==只有一句==调用语句。
### 1.3.1 实例 :: 实例方法

函数式接口抽象方法（称为A）和其实现体中调用的方法（称为B），==当A和B的入参类型、个数及返回值类型完全一致==，且方法B是通过==实例==调用时，可使用 `实例 :: 实例方法`的应用方式，如：

```java
public static void quoteDemo(){
    // 方法引用(1):
    // 实例 :: 实例方法
    // 1.有入参，无返回值

    Consumer<String> c1 = s -> System.out.println(s);
    c1.accept("Hello, Lambda");
  
    // 实例 :: 实例方法
    Consumer<String> c2 = System.out :: println;
    c2.accept("Hello, (instance :: method) of MethodQuote");
}
```

### 1.3.2 类 :: 静态方法

函数式接口抽象方法（称为A）和其实现体中调用的方法（称为B），==当A和B的入参类型、个数及返回值类型完全一致==，且方法B是通过==类名==调用的==静态方法==时，可使用 `类 :: 静态方法`的应用方式，如：

```java
// Function
Function<Double, Long> f1 = new Function<Double,Long>() {
    @Override
    public Long apply(Double t) {
        return Math.round(t);
    }
};

Function<Double, Long> f2 = t -> Math.round(t);

Function<Double, Long> f3 = Math :: round;
```

### 1.3.3 类 :: 实例方法

函数式接口抽象方法（称为A）和其实现体中调用的方法（称为B），除了A、B的返回值类型相同外，还要满足：==当A的入参个数是B的入参个数加一，且A的第一个参数类型是B方法的调用者，A剩余的n-1个入参和B方法的入参个数及类型均相同==时，可使用 `类 :: 实例方法`的应用方式，简记图：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231126165649.png)

如：
```java
// 举例二：BiPredicate
// Raw
BiPredicate<String, String> b1 = new BiPredicate<>() {
    @Override
    public boolean test(String s1, String s2) {
        return s1.equals(s2);
    }
};

// Lambda
BiPredicate<String, String> b2 = (s1, s2) -> s1.equals(s2);

// Method Quote
BiPredicate<String, String> b3 = String :: equals;
```

## 1.4 构造器引用

形式 `类名::new`，抽象接口方法实现时，其返回值是调用构造器生成的对象时可使用，入参的规则和方法引用一致。

1. 举例一：Supplier的get()，调用空参构造器
```java
public static void conDemo(){
    // 举例一：Supplier的get(),调用空参构造器
    // Raw
    Supplier<Employee> s1 = new Supplier<Employee>() {
        @Override
        public Employee get() {
            return new Employee();// 调用构造器生成对象返回
        }
    };

    // Lambda
    Supplier<Employee> s2 = () -> new Employee();

    // Constructor Quote
    Supplier<Employee> s3 = Employee :: new; //构造器引用,调用空参构造器
  
    System.out.println(s1.get());
    System.out.println(s2.get());
    System.out.println(s3.get());
}
```

2. 举例二：Function的apply()，调用有参构造器
```java
public static void conDemo1(){
    // 举例二：Function的apply(),调用有参构造器
    Function<Integer, Employee> f1 = new Function<Integer,Employee>() {
        @Override
        public Employee apply(Integer id) {
            return new Employee(id);
        }
    };

    Function<Integer, Employee> f2 = (id) -> new Employee(id);
    /* 虽然形式上和无参构造器的引用一样，
     * 但实际因为 【类型推断】的原因，编译器会依据Function<Integer, Employee>的
     * 入参 Integer会自动匹配Employee中入参为Integer/int(自动装箱)的构造器（即，public Employee(int age)）生成对象
     */
    Function<Integer, Employee> f3 = Employee :: new;

    System.out.println(f1.apply(11).getId());
    System.out.println(f2.apply(12).getId());
    System.out.println(f3.apply(13).getId());
}
```

3. 练习
```java
public static void conExer(){
    /* 练习：
     * 通过二次函数BiFunction<T, U, R>的apply(T,U)，
     * 调用 构造器Employee(int id, String name)
     */

    // Raw
    BiFunction<Integer, String, Employee> b1 = new BiFunction<Integer,String,Employee>() {
        @Override
        public Employee apply(Integer id, String name) {
            return new Employee(id, name);
        }
    };
  
    // Lambda
    BiFunction<Integer, String, Employee> b2 = (id, name) -> new Employee(id, name);

    // Constructor Quote
    BiFunction<Integer, String, Employee> b3 = Employee :: new;

    System.out.println(b1.apply(12, "ZhangSan"));
    System.out.println(b2.apply(13, "LiSi"));
    System.out.println(b3.apply(14, "WangWu"));
}
```

## 1.5 数组引用

形式 `数组名[]::new`，抽象接口方法实现时，其返回值是数组，且其入参是指定数组长度的整数类型时可使用，如：
```java
public static void arrayDemo(){
    /* 通过Function的apply() 返回指定长度的数组
     * 注意区分本例与构造器引用中举例的区别（形式上和含义上的）
     */
     
    // raw
    Function<Integer, Employee[]> f1 = new Function<Integer,Employee[]>() {
        @Override
        public Employee[] apply(Integer length) {
            return new Employee[length];
        }
    };
  
    // lambda
    Function<Integer, Employee[]> f2 = (length) -> new Employee[length];

    // array quote
    Function<Integer, Employee[]> f3 = Employee[]::new;

    System.out.println(f1.apply(10).length);
    System.out.println(f2.apply(20).length);
    System.out.println(f3.apply(30).length);
}
```

> 注意，方法引用、构造器引用和数组引用使用的大前提是抽象方法的实现体只有一句调用语句，是Lambda表达式的最简写法，需满足的条件较为“严苛”，虽然书写简洁但可读性变差了。

以下是Java类库常用的四类函数式接口：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231126173729.png)


# 二、StreamAPI

StreamAPI用于操作集合（即，集合框架中的List、Set、HashMap等）的一种“语言”，作用类似于关系型数据中的SQL，StreamAPI只操作管理数据，但并不会对源数据进行任何的改动。

有以下数据源：
```java
public class EmployeeData {
    public static List<Employee> getEmployees(){
        List<Employee> list = new ArrayList<>();
        list.add(new Employee(1001, "马化腾", 34, 6000.38));
        list.add(new Employee(1002, "马云", 2, 19876.12));
        list.add(new Employee(1003, "刘强东", 33, 3000.82));
        list.add(new Employee(1004, "雷军", 26, 7657.37));
        list.add(new Employee(1005, "李彦宏", 65, 5555.32));
        list.add(new Employee(1006, "比尔盖茨", 42, 9500.43));
        list.add(new Employee(1007, "任正非", 26, 4333.32));
        list.add(new Employee(1008, "扎克伯格", 25, 2500.32));
        return list;
    }
}
```

## 2.1 Stream的创建

```java
public static void newStream(){
    /* 创建Stream的三种方式 */
    List<Employee> list = EmployeeData.getEmployees();
    
    // 方式一（集合的Stream）：stream()/parallelStream()
    // default Stream<E> stream()：返回一个顺序流
    Stream<Employee> stream = list.stream();

    // default Stream<E> parallelStream()：返回一个并行流
    Stream<Employee> parallelStream = list.parallelStream();

  
    // 方式二（数组的Stream）：public static <T> Stream<T> stream(T[] array)
    Integer[] arr = new Integer[]{1, 2, 3, 4, 5};
    Stream<Integer> stream2 = Arrays.stream(arr);

    int[] arr1 = new int[]{1, 2, 3, 4, 5};
    IntStream stream3 = Arrays.stream(arr1);

    // 方式三（非集合/数组的Stream）：通过Stream的of()方法
    Stream<String> stream4 = Stream.of("AA", "BB", "CC");
}
```

## 2.2 Stream的中间操作

```java
public static void filterMap(){
    // 筛选
    // 查找年龄大于34岁的所有员工
    list.stream().filter(employee -> employee.getAge() > 34).forEach(System.out::println);
    
    System.out.println("---------------------");
    // 映射
    // 打印所有的员工的年龄
    list.stream().map(Employee::getAge).forEach(System.out::println);
}
```

除了筛选、映射外，Stream还有其他的中间操作，如：
> `distinct()`：筛选，通过流所生成元素的 `hashCode()` 和 `equals() `去除重复元素；
> `limit(long maxSize)`：截断流，使其元素不超过给定数量；
> `skip(long n)`：跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补。

## 2.3 Stream的终止操作

一旦执行终止操作，Stream将会开始执行filter、map之类的中间操作（终止操作前这些中间操作也不会执行），终止操作结束后，该Stream实例就不能再次调用中间操作了，此时只能通过创建新的Stream实例进行操作。

### 2.3.1 匹配与查找

```java
public static void matchLook(){
    // StreamApi的终止操作

    // 1-匹配与查找,逻辑上类似于MySQL的select
    // allMatch(Predicate p)--检查是否匹配所有元素
    // 所有员工是否都已成年
    System.out.println(list.stream().allMatch(emp -> emp.getAge() >= 18));

    // anyMatch(Predicate p)--至少有一个元素满足条件
    // 是否有员工月薪一万
    System.out.println(list.stream().anyMatch(emp -> emp.getSalary() >= 10000));

    // findFirst()--返回第一个元素
    System.out.println(list.stream().findFirst().get());
}
```

### 2.3.2 聚合操作

> 聚合操作，逻辑上类似于MySQL中group by 后的聚合函数。

```java
public static void aggregateQuerry(){
    // StreamApi的终止操作
    // 2-聚合操作，逻辑上类似于MySQL中group by 后的聚合函数

    // count()--返回流中元素总个数
    System.out.println(list.stream().count());

    // max(Comparator c) -- 返回流中最大值
    // 返回最高的工资
    // 思路一：找到工资最高的员工，再取其工资
    System.out.print("思路一：");
    System.out.println(list.stream().max((emp1, emp2) -> (int)(emp1.getSalary()-emp2.getSalary())).get().getSalary());

    // 思路二：先取出所有员工的工资（即映射，map()）,再取最高工资
    System.out.print("思路二：");
    System.out.println(list.stream().map(Employee::getSalary).max(Double::compare).get());


    // min(Comparator c) -- 返回流中最小值
    // 返回工资最低的员工,此处也可用和求最大值是用map()的思路
    System.out.println(list.stream().min((emp1, emp2) -> (int)(emp1.getSalary() - emp2.getSalary())).get());

    // forEach(Comsumer c) --内部迭代
    list.stream().forEach(System.out::println);
    System.out.println("list.forEach(): ");
    // 从JDK8起：集合自身也实现了forEach()方法，入参同上，因此有
    list.forEach(System.out::println);

}
```

### 2.3.3 归约

```java
public static void statute(){
    // StreamApi的终止操作
    // 3-归约

    // reduce(T identity, BinaryOperator bo) --有初始值identity的reduce(), 逻辑类似于Python中的reduce()
    // 求 1-10的整数之和
    List<Integer> list1 = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

    // System.out.println(list1.stream().reduce(0, (i1, i2) -> i1 + i2));
    // System.out.println(list1.stream().reduce(0, (i1, i2) -> Integer.sum(i1, i2)));
    System.out.println(list1.stream().reduce(0, Integer::sum));

    // reduce(BinaryOperator bo) --无初始值identity的reduce()
    // 计算公司所有员工工资总和
    System.out.println(list.stream().map(Employee::getSalary).reduce(Double::sum).get());
}
```

### 2.3.4 收集

> `StreamAPI`的操作并不会改变源数据，因此可使用`collect(Collector c)`将`StreamAPI`操作结果保留到 `c` 中。

```java
public static void collectResult(){
    // 收集
  
    // 查找工资大于6000的员工，结果返回为一个List或Set
    List<Employee> resultList = list.stream().filter(emp -> emp.getSalary() > 6000).collect(Collectors.toList());

    resultList.forEach(System.out::println);
    System.out.println("-------------------");

    // 将员工按年龄排序，结果返回到一个新的List中
    List<Employee> resultList2 = list.stream().sorted().collect(Collectors.toList());
    resultList2.forEach(System.out::println);
}
```

# 三、Optional

Optional类，避免空指针异常。

```java
import java.util.Optional;

// Optional类，避免空指针异常

public class OptionalDemo {
    public static void main(String[] args) {
        demo1();
    }

    static void demo1(){
        String star = "李小龙";
        star = null;

        // Optional.ofNullable(T) 创建optional，T可能为null，可能非null
        Optional<String> optional = Optional.ofNullable(star);
        System.out.println(optional.isPresent()); // false则为空
        
        String otherStar = "成龙";
        // optional.orElse(otherStar), 当star=null时，返回otherStar的值，否则返回star
        // optional.orElse(otherStar) 与 Optional.ofNullable(star) 组合使用
        String finalStar = optional.orElse(otherStar);
        System.out.println(finalStar);
    }
}
```

# 四、record关键字

开发人员想要创建纯数据载体类（plain data carriers）通常都必须编写大量低价值、重复的、容易出错的代码。如：构造函数、getter/setter、equals()、hashCode()以及toString()等。JDK14开始，有record提供这些样板代码的默认实现，简化源程序的书写。

举例如下，1和2中的代码效果完全是一样的，但使用`record`的代码更为简洁：

1. 未使用record类时：
```java
public class Order {
    private final int orderId;
    private final String orderName;

    public Order(int orderId, String orderName){
        this.orderId = orderId;
        this.orderName = orderName;
    }

    public int orderId() {
        return orderId;
    }

    public String orderName() {
        return orderName;
    }

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + orderId;
        result = prime * result + ((orderName == null) ? 0 : orderName.hashCode());
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Order other = (Order) obj;
        if (orderId != other.orderId)
            return false;
        if (orderName == null) {
            if (other.orderName != null)
                return false;
        } else if (!orderName.equals(other.orderName))
            return false;
        return true;
    }
  
    @Override
    public String toString() {
        return "Order [orderId=" + orderId + ", orderName=" + orderName + "]";
    }
}
```

2. 使用record类：
```java
// 此类的内部结构完全等价于Order类的实现，可能hashCode()和equals()的实现会略有不同
// record不能有显示额父类，即，不能写成public record OrderRecord(int orderId, String orderName) extends Object
// 因为record已隐式地声明extends Record

public record OrderRecord(int orderId, String orderName) {
    // 还可以在record声明的类中定义静态字段、静态方法、构造器或实例方法
    static String info = "record Order";

    // 不能在record声明的类中定义实例字段；类不能声明为abstract；不能声明显式的父类等。
    public static void show(){
        System.out.println("订单");
    }

    public OrderRecord(){
        this(0, "盘古");
    }

    public void price(){
        System.out.println("价格");
    }
}
```

