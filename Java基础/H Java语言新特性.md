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

