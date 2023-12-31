# 一、字符串相关类
#String

## 1.1 不可变字符-String

### 1.1.1 内存结构

`String`是**不可变字符序列**。
- 在JDK8及之前
```java
public final class String  
    implements java.io.Serializable, Comparable<String>, CharSequence {  
    /** String对象的字符内容是存储在value数组中。 */  
    private final char value[]; 
```

- 在JDK9及之后
```java
public final class String  
    implements java.io.Serializable, Comparable<String>, CharSequence {  
    /** String对象的字符内容是存储在value数组中。 */
    @Stable
    private final byte value[]; 
```

### 1.1.2 示例说明

```java
public class StringDemo {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "world";
        String s3 = "hello" + "world";
        String s4 = s1 + "world";
        String s5 = s1 + s2;
        String s6 = (s1 + s2).intern();

        System.out.println(s3 == s1); // false
        System.out.println(s3 == s4); // true 实际 false
        System.out.println(s3 == s5); // true 实际 false
        System.out.println(s4 == s5); // true 实际 false
        System.out.println(s3 == s6); // true
    }
}
```
解析如下：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230920192756.png)

总结：
> 常量+常量：结果是常量池。且常量池中不会存在相同内容的常量。
> 常量+变量 或 变量+变量：结果在堆中。
> 拼接后调用`intern()`方法：返回值在常量池中。

### 1.1.3 String与其他类型的转换
#String/String与其他类型的转换 

- String->Integer/int
>`public static int parseInt(String s)`，即`Integer.parseInt()`，其他Byte、Short、Long、Float、Double等与之类似；

- Integer/int->String
> `public String valueOf(int n)`，其他Byte、Short、Long、Float、Double等与之类似；

- String -> 字符数组：
> 1）`public char[] toCharArray()`：将字符串中的全部字符存放在一个字符数组中的方法。
> 2）`public void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)`：提供了将指定索引范围内的字符串存放到数组中的方法。

- String -> 字节数组（编码）
> 1）`public byte[] getBytes()` ：使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。
> 2）`public byte[] getBytes(String charsetName) `：使用指定的字符集将此 String 编码到 byte 序列，并将结果存储到新的 byte 数组。

- 字节数组 -> String（解码）
> 1）`String(byte[])`：通过使用平台的默认字符集解码指定的 byte 数组，构造一个新的 String。
> 2）`String(byte[]，int offset，int length)` ：用指定的字节数组的一部分，即从数组起始位置offset开始取length个字节构造一个字符串对象。
> 3）`String(byte[], String charsetName )` 或  `new String(byte[], int offset, int length, String charsetName)`：解码，按照指定的编码方式进行解码。

字符串编解码示例：
```java
import java.io.UnsupportedEncodingException;
/**String 的编码与解码*/
public class StringDemo2 {
    public static void main(String[] args) throws UnsupportedEncodingException{
        String str = "中国";
        //编码
        // ISO8859-1把所有的字符都当做一个byte处理，处理不了多个字节, 2
        System.out.println(str.getBytes("ISO8859-1").length);
        // 4 每一个中文都是对应2个字节
        System.out.println(str.getBytes("GBK").length);
        // 6 常规的中文都是3个字
        System.out.println(str.getBytes("UTF-8").length);


        // 解码
        // 不支持中文的字符集，即便字符集一致也会显示乱码
        System.out.println(new String(str.getBytes("ISO8859-1"), "ISO8859-1"));
        // 编/解码 字符集不一致时，会显示乱码
        System.out.println(new String(str.getBytes("GBK"), "ISO8859-1"));
        // 支持中文时，编/解码字符集一致，显示正确
        System.out.println(new String(str.getBytes("GBK"), "GBK"));
        System.out.println(new String(str.getBytes("UTF-8"), "UTF-8"));

        byte[] encode = str.getBytes("GBK");
        System.out.println(encode.length);
        // 只对部分byte[]数组进行解码
        System.out.println(new String(encode, 0, 2));
    }
}
```

### 1.1.4 String常用方法
#String/String的基本方法
1. `boolean isEmpty()`：字符串是否为空。
2. `String concat(String s)`：拼接。
3. `int length()`：返回字符串长度。
4. `boolean equals(Object obj)`：比较字符串是否相等（区分大小写）。
5. `boolean equalsIgnoreCase(Object obj)`：比较字符串是否相等（不区分大小写）。
6. `int compareTo(String other)`：比较字符串大小，区分大小写，按照Unicode编码值比较大小。
7. `int compareToIgnoreCase(String other)`：比较字符串大小，不区分大小写。
8. `String toLowerCase()`：将字符串中大写字母转为小写。
9. `String toUpperCase()`：将字符串中小写字母转为大写。
10. `String trim()`：去掉字符串前后空白符。
11. `public String intern()`：将字符串结果在常量池中共享

---
#String/String的查找
12. `public boolean contains(CharSequence s)`：是否包含s。
13. `int indexOf(xx)`：从前往后找当前字符串中xx，即如果有返回第一次出现的下标，要是没有返回-1。
14. `int indexOf(String str, int fromIndex)`：返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始。
15. `int lastIndexOf(xx)`：从后往前找当前字符串中xx，即如果有返回最后一次出现的下标，要是没有返回-1。
16. `lastIndexOf(String str, int fromIndex)`：返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索。

---
#String/String的截取

17. `String substring(int beginIndex)`：返回一个新的字符串，它是此字符串的从beginIndex开始截取到最后的一个子字符串。
18. `String substring(int beginIndex, int endIndex)`：返回一个新字符串，它是此字符串从beginIndex开始截取到`endIndex`(不包含)的一个子字符串。

---
#String/与字符_字符数组相关 

19. `char charAt(index)`：返回下标等于index的字符。
20. `static String copyValueOf(char[] data)`：返回指定数组中表示该字符序列的 String。
21. `static String copyValueOf(char[] data, int offset, int count)`：返回指定数组中表示该字符序列的String。

---
#String/String前后缀判断 

22. `boolean startsWith(xx)`：测试此字符串是否以指定的前缀开始。
23. `boolean startsWith(String prefix, int toffset)`：测试此字符串从指定索引开始的子字符串是否以指定前缀开始。
24. `boolean endsWith(xx)`：测试此字符串是否以指定的后缀结束。

---
#String/String的替换 

> 一般性的位置：`replace(被替换的字串, 新字串)`：被替换的字串可以是满足正则的。

25. `String replace(char oldChar, char newChar)`：返回一个替换完成后的新字符串， 不支持正则。
26. `String replace(CharSequence target, CharSequence replacement)`：使用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串。
27. `String replaceAll(String regex, String replacement)`：使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。
28. `String replaceFirst(String regex, String replacement)`：使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串。

## 1.2 可变字符序列

因为String对象是不可变对象，虽然可以共享常量对象，但是对于频繁字符串的修改和拼接操作，效率极低，空间消耗也比较高。因此，JDK又在`java.lang`包提供了可变字符序列`StringBuffer`和`StringBuilder`类型。

### 1.2.1 StringBuilder/StringBuffer异同
#String/可变字符串 

- 相同点：
> `StringBuilder` 和 `StringBuffer` 非常类似，均代表可变的字符序列，**而且提供相关功能的方法也一样**。
- 不同点：
> `StringBuffer`:可变的字符序列；**线程安全**（方法有`synchronized`修饰），**效率低**；底层使用`char[]`数组存储 (JDK8.0中)；
> `StringBuilder`:可变的字符序列； jdk1.5引入，**线程不安全**的，**效率高**；底层使用`char[]`数组存储(JDK8.0中)

### 1.2.2 常用API
#String/可变字符串/常用API  

1. `StringBuffer append(xx)`：提供了很多的`append()`方法，用于进行字符串**追加的方式拼接**。
2. `StringBuffer delete(int start, int end)`：删除`[start,end)`之间字符。
3. `StringBuffer deleteCharAt(int index)`：删除`[index]`位置字符。
4. `StringBuffer replace(int start, int end, String str)`：替换`[start,end)`范围的字符序列为`str`。
5. `void setCharAt(int index, char c)`：替换`[index]`位置字符。
6. `char charAt(int index)`：查找指定`index`位置上的字符。
7. `StringBuffer insert(int index, xx)`：在`[index]`位置插入xx。
8. `int length()`：返回存储的**字符数据的长度**。
9. `StringBuffer reverse()`：反转。

----
#String/可变字符串/其他API

10. `int indexOf(String str)`：在当前字符序列中查询str的第一次出现下标。
11. `int indexOf(String str, int fromIndex)`：在当前字符序列`[fromIndex,最后]`中查询str的第一次出现下标。
12. `int lastIndexOf(String str)`：在当前字符序列中查询str的最后一次出现下标。
13. `String substring(int start)`：截取当前字符序列`[start,最后]`。
14. `String substring(int start, int end)`：截取当前字符序列`[start,end)`。
15. `String toString()`：返回此序列中数据的字符串表示形式。
16. `void setLength(int newLength)` ：设置当前字符序列长度为`newLength`。

### 1.2.3 练习

- String、StringBuffer(线程安全)、StringBuilder效率比较：
```java
public class EfficiencyTest {
    public static void main(String[] args) {
        long startTime = 0L;
        long endTime = 0L;

        String text = "";
        StringBuffer buffer = new StringBuffer("");
        StringBuilder builder = new StringBuilder("");

        startTime = System.currentTimeMillis();
        for (int i = 0; i < 20000; i++) {
            buffer.append(String.valueOf(i));
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuffer执行时间：" + (endTime - startTime));

        startTime = System.currentTimeMillis();
        for (int i = 0; i < 20000; i++) {
            builder.append(String.valueOf(i));
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuilder执行时间：" + (endTime - startTime));

        startTime = System.currentTimeMillis();
        for (int i = 0; i < 20000; i++) {
            text = text + i;
        }
        endTime = System.currentTimeMillis();
        System.out.println("String执行时间：" + (endTime - startTime));
    }
}
```
- 可变字符的构造器、长度
```java
public class StringExer {
    public static void main(String[] args) {
        String str = null;
        StringBuffer sb = new StringBuffer();
        sb.append(str);
        System.out.println(sb.length());//16 --实际是4

        System.out.println(sb);//null

		// 实际会报Exception in thread "main" java.lang.NullPointerException
        StringBuffer sb1 = new StringBuffer(str);
        System.out.println(sb1);//null 

    }

}

```

# 二、日期时间

## 2.1 java.util.Date
#旧时间

- 构造器
```java
public Date(long date){
	// date是毫秒数
	fastTime = date;
}

public Date() {
	// 当前时间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差。
	this(System.currentTimeMillis());
}
```

- 常用API
> 1）`getTime()`: 返回自 1970 年 1 月 1 日 00:00:00 GMT 以来此 Date 对象表示的毫秒数。
> 2）`toString()`: 把此 Date 对象转换为以下形式的 String： dow mon dd hh:mm:ss zzz yyyy
>  (如：Fri Sep 22 17:06:15 CST 2023)；
> 	 其中： dow 是一周中的某一天 (Sun, Mon, Tue, Wed, Thu, Fri, Sat)，zzz是时间标准。

## 2.2 java.text.SimpleDateFormat
#旧时间

java.text.SimpleDateFormat类是一个不与语言环境有关的方式来格式化和解析日期的具体类。
可以进行格式化：`Date` --> `String`（即，`simpleDateFormat.format(Date date)`）
可以进行解析：`String` --> `Date`（即，`simpleDateFormat.parse(String text)`）

- 构造器
> `public SimpleDateFormat()`：默认的模式和语言环境创建对象。
> `public SimpleDateFormat(String pattern)`：该构造方法可以用参数pattern指定的格式创建一个对象。
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230924165344.png)


- 格式化
> `public String format(Date date)`：方法格式化时间对象date，即 `Date` -> `String`。

- 解析
> `public Date parse(String source)`：从给定字符串的开始解析文本，以生成一个日期，即`String` -> `Date`。

- 示例
```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;


public class SDFDemo {
    public static void main(String[] args) throws ParseException {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS E Z");
        Date date = new Date();
        
        // Date -> String
        String dateStr = sdf.format(date);
        System.out.println(dateStr);
  
        // String -> Date
        Date date2 = sdf.parse(dateStr);
        System.out.println(date2);
    }
}
```

效果：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230924165640.png)


## 2.3 java.util.Calendar
#旧时间

Date类的API大部分被废弃了，替换为Calendar，Calendar 类是一个抽象类，主用用于完成日期字段之间相互操作的功能。

### 2.3.1 常用方法

1.  `Calendar.getInstance()`: 获取Calender实例。
2. `public int get(int field)`：返回给定日历字段的值。
3. `public void set(int field,int value) `：将给定的日历字段设置为指定的值。
4. `public void add(int field,int amount)`：根据日历的规则，为给定的日历字段添加或者减去指定的时间量。
5. `public final Date getTime()`：将Calendar转成Date对象。
6. `public final void setTime(Date date)`：使用指定的Date对象重置Calendar的时间。
### 2.3.2 示例及运行效果

```java
import java.util.Calendar;
import java.util.Date;
import java.util.TimeZone;


public class CalendarDemo {
    public static void main(String[] args) {
        Calendar c = Calendar.getInstance();
        
        int year = c.get(Calendar.YEAR);
        System.out.println(year);
        
        // 获取星期时：1是星期日，2是星期一，以此类推，7是星期六
        // 获取月份时：一月是0，二月是1，以此类推，12月是11
        c.get(Calendar.MONTH);
        System.out.println("修改前的月份：" + c.getTime());
        System.out.println("修改前的c.MONTH=" + c.get(Calendar.MONTH));
        // 设置属性
        c.set(Calendar.MONTH, 0);
        System.out.println("修改后的月份：" + c.getTime());
        System.out.println("修改后的c.MONTH=" + c.get(Calendar.MONTH));

        // 为属性进行加减,两个月前
        c.add(Calendar.MONTH, -2);
        System.out.println("两个月：" + c.getTime());
        System.out.println("c.add(Calendar.MONTH, -2)后：" + c.get(Calendar.MONTH));
        System.out.println(c.get(Calendar.YEAR));

  
        TimeZone timeZone = TimeZone.getTimeZone("America/Los_Angeles");
        Calendar c1 = Calendar.getInstance(timeZone);
        // Calender -> Date
        Date date = c1.getTime();
        System.out.println(date);
    }
}
```

运行效果：
![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230924180748.png)


## 2.4 综合练习：打印某年某月的日历

```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.Scanner;

/**输入年份和月份，输出该月日历。
 *
闰年计算公式：
年份可以被4整除但不能被100整除，
或者可以被400整除。
*/
  
public class CalenderExer {
    public static void main(String[] args) throws ParseException {
        CalenderExer exer = new CalenderExer();
        
        Scanner input = new Scanner(System.in);
        System.out.print("请输入年份：");
        int year = input.nextInt();

        System.out.print("请输入月份：");
        int month = input.nextInt();

        exer.showCalender(year, month);

        input.close();
    }

    private String getHeader(){
        String head = "日\t一\t二\t三\t四\t五\t六\t";
        return head;
    }

    public boolean isLeapYear(int year){
        boolean condition1 = year % 4 == 0 && year % 100 != 0;
        boolean condition2 = year % 400 != 0;
        return condition1 && condition2;
    }

    private int GetNumberOfDay(int year, int month){
        int[] dayOfMonths = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
        if (isLeapYear(year)){
            dayOfMonths[1] = 29;
        }
        return dayOfMonths[month - 1];
    }

    private String getBody(int year, int month) throws ParseException{
        String body = "";

        // int -> Date
        String dateStr = String.format("%d-%02d-01", year, month);
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
        Date date = sdf.parse(dateStr);

        // Date -> Calender
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(date);
        int weekday = calendar.get(Calendar.DAY_OF_WEEK);
  
        String space = "";
        for (int i = 1; i < weekday; i++) {
            space = space + " \t";
        }

        body = body + space;
        for (int i = 1; i <= GetNumberOfDay(year, month); i++){
            body = body + i + '\t';
            if ((i + weekday - 1) % 7 == 0){
                body = body + "\n";
            }
        }
        return body;
    }

    public void showCalender(int year, int month) throws ParseException{
        String head = getHeader();
        System.out.println(head);
  
        String body = getBody(year, month);
        System.out.println(body);
    }
}
```

运行结果如下：

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230924194024.png)


## 2.5 JDK8中新的日期时间API
#新时间

简单理解：`LocalDateTime` = `LocalDate` + `LocalTime`，即，日期时间 = 日期 + 时间。

### 2.5.1 日期时间的实例获取（of/now()）

```java
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;

public class LocalDateTimeDemo {
    public static void main(String[] args) {
	    LocalDate nowLD = LocalDate.now();
        System.out.println("nowLD=" + nowLD);
        
        LocalTime nowLT = LocalTime.now();
        System.out.println("nowLT=" + nowLT);

        LocalDateTime nowLDT = LocalDateTime.now();
        System.out.println("nowLDT=" + nowLDT);

        LocalDate localDate = LocalDate.of(2023, 9, 25);
        System.out.println("localDate=" + localDate);

        LocalTime localTime = LocalTime.of(16, 4, 5);
        System.out.println("localTime=" + localTime);

        LocalDateTime localDateTime = LocalDateTime.of(2023, 9, 25, 16, 4, 5);
        System.out.println("localDateTime=" + localDateTime);
    }
}    
```

### 2.5.2 常用API

- `LocalDateTime`：
> 1. get相关：
> ![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230925190339.png)

> 2. set之加时间：
>![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230925190443.png)

> 3. set之减时间：
> ![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230925190522.png)

-  `LocalDate` 、 `LocalTime`的set、get方法
> 与`LocalDateTime`类似，在此略去，实际使用时，按IDE提示及需要使用即可。特别地，`LocalDate`有个实用的方法——判断闰年：
> ![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230925190614.png)


## 2.6 旧/新时间小结

`simpleDateFormat.parse:string` 即`simpleDateFormat.parse(string)`。
旧时间的转换关系：

```mermaid
flowchart LR
    Calender -->|calender.getTime| Date

    Date -->|date.getTime| long:毫秒数

    Date -->|simpleDateFormat.format:date| String -->|simpleDateFormat.parse:string| Date
```

新时间的转换关系：

```mermaid
flowchart TD
    LocalDateTime --> |dateTimeFormatter.format:localDateTime| String
    String --> | dateTimeFormatter.parse:string| TemporalAccessor
    TemporalAccessor --> |LocalDateTime.from:temporalAccessor| LocalDateTime
```
# 三、Java比较器
#Java排序比较器

## 3.1 自然排序：java.lang.Comparable

Comparable接口强行对实现它的每个类的对象进行整体排序。这种排序被称为类的自然排序。
实现 Comparable 的类必须实现 compareTo(Object obj)方法，两个对象即通过 compareTo(Object obj) 方法的返回值来比较大小。如果当前对象this大于形参对象obj，则返回正整数，如果当前对象this小于形参对象obj，则返回负整数，如果当前对象this等于形参对象obj，则返回零。

示例如下：
```java
public class Person implements Comparable<Object>{
    // Comparable 自然排序
    private String name;
    private int age;

    public Person(String name, int age){
        this.name = name;
        this.age = age;
    }
  
    @Override
    public int compareTo(Object o) {
        // 按年龄升序排序
        if (o instanceof Person){
            Person other = (Person) o;
            return Integer.compare(age, other.getAge());
        }
        throw new RuntimeException("o 不是Person的子类，无法比较");
    }
}    
```


## 3.2 定制排序：java.util.Comparator

能更自由地定制对象的排序规则，甚至覆盖掉java.lang.Comparable实现中的compareTo()方法，示例代码如下：

```java
Arrays.sort(persons, new Comparator<Object>() {
        // Comparator， 定制排序
        @Override
        public int compare(Object o1, Object o2) {
            if (o1 instanceof Person && o2 instanceof Person){
                // 按姓名升序排序
                Person p1 = (Person) o1;
                Person p2 = (Person) o2;
                int result = p1.getName().compareTo(p2.getName());
                return result;
            }
            throw new RuntimeException("入参之一 不是Person的子类，无法比较");
        }
    });

    for (Person person : persons) {
        // 按姓名升序排序输出,即 Comparator会覆盖掉 Person从Comparable中重写的compareTo方法中的比较准则
        System.out.println(person);
    }
```