# 一、反射概述

## 1.1 反射的出现背景

Java程序中，所有的对象都有两种类型：编译时类型和运行时类型，而很多时候对象的编译时类型和运行时类型不一致。

例如：某些变量或形参（如下例代码中的obj）的声明类型是Object类型，但是程序却需要调用该对象运行时类型`String`的方法`startWith()`，该方法不是Object中的方法：
```java
Object obj = new String("hello"); 
obj.startWith("h");
```

那么如何解决呢？

解决这个问题，有两种方案：

方案1：在编译和运行时都完全知道类型的具体信息，在这种情况下，我们可以直接先使用instanceof运算符进行判断，再利用强制类型转换符将其转换成运行时类型的变量即可。

方案2：编译时根本无法预知该对象和类的真实信息，程序只能依靠运行时信息来发现该对象和类的真实信息，这就必须使用==反射==。

## 1.2 反射过程简述

反射：即通过 Java提供的Reflection API 去操作已经运行在内存中的字节码中的的运行时类的内部属性和方法。

非反射时：通过`new`的方式创建运行时对象；
反射：通过Reflection API 获取无法事先（即编译时）准确获取、只能在运行时才能知道具体类型的运行时类型，此类型是`Classs`类的实例，再通过此类型去动态地按需生成所需类型的对象，以实现多变的需求。从这点上看，Java的反射有点像动态语言`Python`的动态特性。

## 1.3 反射的优缺点

1. 优点：
>  提高了Java程序的灵活性和扩展性，降低了耦合性，提高自适应能力；
>  允许程序创建和控制任何类的对象，无需提前硬编码目标类（即动态性）,反射机制主要应用在对灵活性和扩展性要求很高的系统框架上。

2. 缺点：
> 反射的性能较低。
> 反射会模糊程序内部逻辑，可读性较差。

# 二、Reflection API

## 2.1 获取Class类的实例

Class类的实例是反射进行的基础，常见的有以下四种：

1. 编译期间已知类型时，使用：
```java
Class clazz = String.class;
```

2. 获取对象的运行时类型：
```java
Class clazz = "String".getClass()
```

3. 获取编译期间未知的类型：
```java
// 必须填入类的包的全路径及类名
Class clazz = Class.forName("java.lang.String"); 
```

4. 使用系统加载器获取以运行时类型：
```java
ClassLoader c1 = this.getClass().getClassLoader();
// 必须填入类的包的全路径及类名
Class clazz = c1.loadClass("java.lang.String");
```
另外，关于类加载器的一个主要方法：`getResourceAsStream(String str)`:获取类路径下的指定文件的输入流，即：
```java
InputStream in = null;  
in = this.getClass().getClassLoader().getResourceAsStream("exer2\\test.properties");  
System.out.println(in);
```

## 2.2 Class类的适用范围

即，反射可对哪些类型的对象进行，Java中所有的对象均能进行反射，简言之，Java中所有的运行时对象都有Class实例，包括：
1. `class`：外部类，成员(成员内部类，静态内部类)，局部内部类，匿名内部类。
2. `interface`：接口。
3. `[]`：数组。
4. `enum`：枚举。
5. `annotation`：注解@interface。
6. `primitive type`：基本数据类型 。
7. `void`。

```java
// Class对象可反射、指向的结构对象（或数据类型）有哪些，
// Java中的一切数据类型都可指向，见以下举例
public static void classPointableStructure() {
    Class c1 = Object.class; // 一般引用类型，即对象（示例）
    Class c2 = Comparable.class; // 接口
    Class c3 = String[].class; // 对象数组
    Class c4 = int[][].class; // 基本数据类型的多维数据（此处为二维）
    Class c5 = ElementType.class; // 枚举
    Class c6 = Override.class; // 注解
    Class c7 = int.class; // 基本数据类型
    Class c8 = void.class; // "无返回值"
    Class c9 = Class.class; // Class
  
    int[] a = new int[10];
    int[] b = new int[100];
    int[][] c = new int[10][];
  
    Class ca = a.getClass();
    Class cb = b.getClass();
    Class cc = c.getClass();
  
    // 同一维度的同数据类型的对象的class实例引用相同，此处表现为 同是int型的一维数据对象的Class引用相同，即便a与b的长度不同
    System.out.println(ca == cb); //true,
    // 反之，不同维度的同数据类型的对象的class实例引用也不同，即便a与c的长度相同，都是10，但a是一维数组，c是二维数组
    System.out.println(ca == cc); // false
}
```

## 2.3 反射获取运行时类完整内部信息

### 2.3.1 本身完整内部信息的获取

#### 2.3.1.1 获取运行时对象
```java
public static void demo1() throws InstantiationException, IllegalAccessException{
    /** 创建运行时类的对象 */
    Class<Person> clazz = Person.class;
    // 无Person空参构造器时，报 InstantiationException
    // 有Person空参构造器但权限不够(被private/protected等修饰)时,报IllegalAccessException
    // newInstance()调用条件较多
    Person person = clazz.newInstance();
    System.out.println(person);
}
```

#### 2.3.1.2 获取运行时对象所有属性
```java
public static void demo2(){
    /** 获取运行时类的内部结构1：
     * 获取所有属性
     */
    Class<Person> clazz = Person.class;
  
    // getFields()：获取运行时类本身及其所有父类中声明为public权限的属性
    Field[] fields = clazz.getFields();
    for (Field field : fields) {
        System.out.println(field);
    }

    System.out.println("--------------getDeclaredFields()：获取运行时类本身所有的属性-----------------");
    // getDeclaredFields()：获取运行时类本身声明所有的属性(即便没有对应的权限也会获取),但不包括其父类的属性
    Field[] allFields = clazz.getDeclaredFields();
    for (Field field : allFields) {
        System.out.println(field);
    }
}
```

#### 2.3.1.3 获取属性的权限信息、数据类型、属性名
```java
public static void demo3(){
    /** 获取属性的权限信息、数据类型、属性名 */
    Class<Person> clazz = Person.class;
    Field[] fields = clazz.getDeclaredFields();

    for (Field field : fields) {
        /**
         * PUBLIC    1
         * PRIVATE   2
         * PROTECTED 4
         * STATIC    8
         * FINAL     16
         * 则10=8+2，即private static
         * ...
         */
        int modifier = field.getModifiers();
        System.out.print(modifier + " -> " + Modifier.toString(modifier) + "\t");

        // 数据类型
        Class<?> type = field.getType();
        System.out.print("Type(" + type.getName() + ")\t");

        // 变量名
        String fieldName = field.getName();
        System.out.println(fieldName);
    }
}
```

#### 2.3.1.4 获取运行时对象的方法
```java
public static void demo4(){
    // 获取运行时对象的方法
    Class<Person> clazz = Person.class;
    
    // getMethods()：获取运行时类本身及其所有父类中声明为public权限的方法
    Method[] methods = clazz.getMethods();
    for (Method method : methods) {
        System.out.println(method);
    }

    System.out.println("--------------getDeclaredMethods()：获取运行时类本身所有的方法-----------------");
    // getDeclaredMethods()：获取运行时类本身声明所有的方法(即便没有对应的权限也会获取),但不包括其父类的属性
    Method[] methods2 = clazz.getDeclaredMethods();
    for (Method method : methods2) {
        System.out.println(method);
    }
}
```

#### 2.3.1.5 获取运行时类的方法、注解相关的详细信息
```java
public static void demo5(){
    // 获取运行时类的方法、注解相关的详细信息

    Class<Person> clazz = Person.class;
    Method[] methods = clazz.getDeclaredMethods();
    for (Method method : methods) {
        // 1.获取方法声明的注解，方法可能被多个注解修饰，故此次用复数形式获取所有注解并打印
        Annotation[] annotations = method.getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println(annotation);
        }

        // 2.权限修饰符
        System.out.print(Modifier.toString(method.getModifiers()) + "\t");

        // 3.返回值类型
        System.out.print(method.getReturnType().getName() + "\t");
  
        // 4.方法名
        System.out.print(method.getName() + "(");

        // 5.形参列表
        Class<?>[] parameterTypes = method.getParameterTypes();
        if (!(parameterTypes == null && parameterTypes.length == 0)) {
            for (int i = 0; i < parameterTypes.length; i++) {
                if (i == parameterTypes.length - 1) {
                    System.out.print(parameterTypes[i].getName() + " args_" + i);
                    break;
                }

                System.out.println(parameterTypes[i].getName() + "args_" + i + ",");
            }
        }
        System.out.print(")");
  
        // 6.获取运行时类方法抛出的异常
        Class<?>[] exceptionTypes = method.getExceptionTypes();
        if (exceptionTypes.length > 0){
            System.out.print(" throws ");
            for (int i = 0; i < exceptionTypes.length; i++) {
                if (i == exceptionTypes.length - 1) {
                    System.out.print(exceptionTypes[i].getName());
                    break;
                }
                System.out.print(exceptionTypes[i].getName() + ",");
            }
        }
        System.out.println();
    }
}
```

### 2.3.2 调用指定的属性、方法和构造器

#### 2.3.2.1 获取及设置属性的值
```java
public static void getRefField() throws Exception{
    /* 获取及设置属性的值 */
    Class<Person> clazz = Person.class;

    Person person = clazz.newInstance();
    System.out.println("-------------获取运行时类 有权限 的 特定名称的属性------------");
    // 获取运行时类 有权限 的 特定名称的属性
    Field ageField = clazz.getField("age");
    
    // 获取实例person的属性 age, 相当于 person.age
    System.out.println(ageField.get(person));

    // 设置实例person的属性 age，相当于 person.age = 22
    ageField.set(person, 22);
    System.out.println(person.age);

    System.out.println("-------------暴力访问无权限的【实例】属性------------");
    // 2. 暴力访问无权限的实例属性
    Field nameField = clazz.getDeclaredField("name");
    nameField.setAccessible(true); // 暴力获权

    // get name
    System.out.println(nameField.get(person));

    // set name
    nameField.set(person, "Cribug");
    System.out.println(nameField.get(person));
  
    System.out.println("-------------暴力访问无权限的【静态】属性------------");
    // 3. 暴力访问无权限的静态属性 Person的 private static String descrition = "nothing";
    Field descriptionField = clazz.getDeclaredField("description");
    descriptionField.setAccessible(true); // 暴力获权

    // get description
    // 因为是静态变量，所以可以传入null,表示与具体的实例无关，当然也可将null 换为 具体的实例person
    System.out.println(descriptionField.get(null));
    // set description
    descriptionField.set(null, "Static Field");
    System.out.println(descriptionField.get(null));
}
```

#### 2.3.2.2 调用运行时类指定的方法
```java
public static void invokeRefMethod() throws Exception{
    /* 调用运行时类指定的方法 */
    Class<Person> clazz = Person.class;

    Person person = clazz.newInstance();
    /* 调用 private String showNation(String nation, int age)时：
        * 注意 int.class 不能写成 Integer.class！
        * 虽然 int 型数据 能被装箱成 Integer型数据，
        * 但某数的数据类型是不同的，即，int是基本数据类型，Integer是引用数据类型
        * “类型”不一样，因此不能混淆。
        */
    Method showNationMethod = clazz.getDeclaredMethod("showNation", String.class, int.class);
    showNationMethod.setAccessible(true);

    // invoke()返回值与运行时类对应方法声明的返回值一样， showNation()返回String类型的，因此可强转成 String类型
    System.out.println("showNation()返回类型是：" + showNationMethod.getReturnType().getName());

    // 与静态属性类似，若方法是静态方法，则可把person换为null，表示与具体的类无关
    String nation = (String) showNationMethod.invoke(person, "中国", 16);
    System.out.println(nation);
}
```

#### 2.3.2.3 调用运行时类指定的构造器
```java
public static void invokeRefConstructor() throws Exception{
    /* 调用运行时类指定的构造器 */
    Class<Person> clazz = Person.class;

    // 调用 private Person(String name, int age)
    Constructor<Person> constructor = clazz.getDeclaredConstructor(String.class, int.class);
    constructor.setAccessible(true);// 暴力获权

    Person person = constructor.newInstance("Cribug", 25);
    System.out.println(person);
}
```

### 2.3.3 父类完整内部信息的获取
```java
public static void func1() throws ClassNotFoundException{
    Class<?> clazz = Class.forName("chapter17.node01.Person");
    
    // 1.获取运行时类的父类
    Class<?> superClass = clazz.getSuperclass();
    System.out.println(superClass);

    // 2.获取运行时类的带泛型的父类
    Type genericSuperClass = clazz.getGenericSuperclass();
    System.out.println(genericSuperClass);

    // 3.获取运行时类实现的接口
    Class<?>[] interfaces = clazz.getInterfaces();
    for (Class<?> class1 : interfaces) {
        System.out.println(class1);
    }

    // 4.获取运行时所在的包
    Package package1 = clazz.getPackage();
    System.out.println(package1);

    // 5.获取运行时类的父类的泛型(不易) 与 2是有区别的
    // 需要通过 2 的带泛型的父类来获取 泛型实参
    ParameterizedType parameterizedType = (ParameterizedType) genericSuperClass;
    Type[] arguments = parameterizedType.getActualTypeArguments(); // 获取父类泛型实参，可能有多个，因此是数组
    System.out.println(((Class<?>) arguments[0]).getName());// 得到Creature<String>中的String
}
```

# 三、反射的应用

## 3.1 反射读取注解的示例

> 一个完整的注解应该包含三个部分:
> （1）声明
   （2）使用
   （3）读取

