# 一、概述

泛型是指参数化类型的能力，使得代码在编译阶段（而不是运行代码时发现）发现类型不一致的问题，能让问题提前暴露；另外，泛型也让代码的写法更为简洁。

在JDK 1.5引入泛型后，以`java.lang.Comparable`接口的定义及使用为例：

```java
// JDK 1.5引入泛型后
package java.lang;

public interface Comparable<T>{
    public int compareTo(T t);
}
```

其中，`<T>`是形式泛型类型，随后可以用实际具体类型替换它，替换过程称为泛型实例化；另外，未使用泛型的类定义（JDK1.5之前的类）称为 原始类型。

对于使用泛型的优点，用以下代码进行说明：

```java
ArrayList<String> list = new ArrayList<String>();
list.add("Red");
list.add(1); // 由于使用泛型，使得在编译阶段（无需运行代码）就能发现此编译型错误，风险提前暴露，程序将更加可靠

String str = list.get(0); //  由于使用泛型，无需进行类型的强制转换，
// 未使用泛型时，需进行如下转换：
// String str = (String)(list.get(0));
```

泛型可用于定义 类、接口和方法中。

# 二、泛型类的定义

以下是一个示例：

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231029165512.png)

```java
import java.util.ArrayList;

/**泛型栈*/
public class GenericStack<E> {
    private ArrayList<E> list = new ArrayList<E>();
    
    public int getSize(){
    // 返回这个栈的元素个数
        return list.size();
    }

    public E peek(){
    // 返回栈顶元素
        return list.get(getSize() - 1);
    }

    public void push(E e){
    // 向栈中添加一个新元素
        list.add(e);
    }

    public E pop(){
    // 返回并删除栈顶元素
        E e = list.get(getSize() - 1);
        list.remove(getSize() - 1);
        return e;
    }

    public boolean isEmpty(){
    //  判断栈是否为空
        return list.isEmpty();
    }

    @Override
    public String toString() {
        return "GenericStack [list=" + list + "]";
    }
}
```

# 三、泛型方法与通配受限

## 3.1 泛型方法

以下是泛型方法的定义与使用示例：
```java
// 泛型方法
public class GenericMethodDemo {
    public static void main(String[] args) {
        Integer[] integers = {1, 2, 3, 4, 5};
        String[] strings = {"London", "Paris", "New York", "Austin"};

        GenericMethodDemo.<Integer>print(integers);
        GenericMethodDemo.<String>print(strings);
    }

    public static <E> void print(E[] list){
        for (int i = 0; i < list.length; i++)
            System.out.print(list[i] + " ");
        System.out.println();
    }
}
```

## 3.2 通配符（?）

泛型实例中，存在以下限制：

> 如果 A extends B（A是B的子类），则`ArrayList<A>` 不是 `ArrayList<B>`的子类。
> 原因：
> 泛型是使用一种类型消除的方法来实现的。编译器使用泛型类型信息来编译代码，但是随后会消除它。因此，泛型信息在运行时是不可用的。这种方法可以使代码向后兼容使用原始类型的遗留代码。由于运行时会消除泛型，尽管`ArrayList<A>` 和 `ArrayList<B>`是两种类型，但运行时只有一个ArrayList会被加载到JVM中，它们在运行时不会分别被加载成两个类，即`ArrayList<A>` 和 `ArrayList<B>`在运行时其实是一个ArrayList实例，故不存在所属关系。

以下代码会存在问题：
```java
public class WildCareDemo2 {
    public static void main(String[] args) {
        GenericStack<Integer> intStack = new GenericStack<Integer>();
        intStack.push(1);
        intStack.push(2);
        intStack.push(-2);
        print(intStack);
    }

    public static void print(GenericStack<Number> stack){
        while (!stack.isEmpty()){
            System.out.println(stack.pop() + " ");
        }
    }
}
```
即：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231029171723.png)

此时，则需用到通配符`?`，修改方式如下：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231029173924.png)

## 3.3 受限通配

当泛型的实际类型（即`<A>`中的A）之间存在一定的继承关系时，需要用到受限通配符，即用`<? extends A>`表示A的子类，用`<? super A>`表示A的父类。

如以下的代码：

```java
public class WildCareDemo3 {
    public static void main(String[] args) {
        GenericStack<String> stack1 = new GenericStack<String>();
        GenericStack<Object> stack2 = new GenericStack<Object>();
  
        stack1.push("stack1 of Java");
        stack2.push(2);
        stack2.push("Sun");

        System.out.println(stack2);
        // WildCareDemo3.<String>add(stack1, stack2);
        WildCareDemo3.<Object>add(stack1, stack2);
        System.out.println(stack2);
    }

    // public static <T> void add(GenericStack<T> stack1, GenericStack<? super T> stack2){
    public static <T> void add(GenericStack<? extends T> stack1, GenericStack<T> stack2){
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
    }
}
```

在上面的代码中，stack1的元素类型是String，stack2的元素类型是Object，String是Object的子类，因此：
- 以Object为参照作为T，则有:
`public static <T> void add(GenericStack<? extends T> stack1, GenericStack<T> stack2)`和`WildCareDemo3.<Object>add(stack1, stack2)` 的组合;
- 以String为参照作为T，则有：
`public static <T> void add(GenericStack<T> stack1, GenericStack<? super T> stack2)` 和 `WildCareDemo3.<String>add(stack1, stack2)`的组合；