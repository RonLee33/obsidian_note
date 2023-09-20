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
![](Pasted%20image%2020230920114937.png)
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