# 一、File类

File类的作用类似与C语言中的文件描述符，用于表示一个可操作的文件/目录对象，它是进行文件/目录操作的基础和直接对象，其常用的构造器如下：

1. 构造器
> `File(String pathname)`：pathname可以是目录或文件
> `File(String parent, String child)`：在parent目录下创建child，child是目录或文件
> `File(File parent, String child)`：在parent目录的File实例下创建child，child是目录或文件

2. 常用方法
> `public boolean exists()`：对应的文件/目录存在则返回true，否则返回false
> `public boolean isFile()`：File对象是文件则true
> `public boolean isDirectory()`：File对象是目录则true
> `public boolean isHidden()`：File对象是隐藏的则true
> `public String getAbsolutePath()`：获取File对象的绝对路径
> `public String getName()`：获取File对象的文件名
> `public String getPath()`：获取File对象完整的路径名（路径名包含文件名）
> `public Sting getParent()`：获取File对象所在的目录（不包含文件名自身）
> `public long length()`：返回文件的大小，单位是Byte
> `public File[] listFile()`：返回目录对象File下的文件列表
> `public boolean delete()`：删除该文件/目录，删除成功则返回true
> `public renameTo(File dest)`：将文件重命名为dest所表示的File对象的文件名

# 二、IO流的原理及分类

## 2.1 IO流的原理

Java中将操作中的文件看成“流动的二进制数据”，“流动”分为流入（Input）和流出（Output），合称`I/O`流。

## 2.2 IO流的分类

按不同的用途和标准的分类如下图：

![](https://gitee.com/litan33/image-host/raw/master/img/20231107201904.png)
## 2.3 I/O流的API

| 分类       | 字节输入流           | 字节输出流            | 字符输入流        | 字符输出流         |
| ---------- | -------------------- | --------------------- | ----------------- | ------------------ |
| 抽象基类   | InputStream          | OutputStream          | Reader            | Writer             |
| 访问文件   | FileInputStream      | FileOutputStream      | FileReader        | FileWriter         |
| 访问数组   | ByteArrayInputStream | ByteArrayOutputStream | CharArrayReader   | CharArrayWriter    |
| 访问管道   | PipedInputStream     | PipedOutputStream     | PipedReader       | PipedWriter        |
| 访问字符串 |                      |                       | StringReader      | StringWriter       |
| 缓冲流     | BufferedInputStream  | BufferedOutputStream  | BufferedReader    | BufferedWriter     |
| 转换流     |                      |                       | InputStreamReader | OutputStreamWriter |
| 对象流     | ObjectInputStream    | ObjectOutputStream    |                   |                    |
|            | FilterInputStream    | FilterOutputStream    | FilterReader      | FilterWriter       |
| 打印流     |                      | PrintStream           |                   | PrintWriter        |
| 推回输入流 | PushbackInputStream  |                       | PushbackReader    |                    |
| 特殊流     | DataInputStream      | DataOutputStream      |                   |                    |


# 三、节点流

Java提供一些字符流类，以字符为单位读写数据，专门用于处理文本文件。不能操作图片，视频等非文本文件。
## 3.1 字符流 Reader/Writer

`Reader`常用方法：

1. `public int read()`： 从输入流读取一个字符。 虽然读取了一个字符，但是会自动提升为int类型。返回该字符的Unicode编码值。如果已经到达流末尾了，则返回-1。
2. `public int read(char[] cbuf)`： 从输入流中读取一些字符，并将它们存储到字符数组 cbuf中 。每次最多读取cbuf.length个字符。返回实际读取的字符个数。如果已经到达流末尾，没有数据可读，则返回-1。
3. `public int read(char[] cbuf,int off,int len)`：从输入流中读取一些字符，并将它们存储到字符数组 cbuf中，从cbuf[off]开始的位置存储。每次最多读取len个字符。返回实际读取的字符个数。如果已经到达流末尾，没有数据可读，则返回-1。
4. `public void close() `：关闭此流并释放与此流相关联的任何系统资源。

`Writer`常用方法：

1. `public void write(int c) `：写出单个字符。
2. `public void write(char[] cbuf)`：写出字符数组。
3. `public void write(char[] cbuf, int off, int len)`：写出字符数组的某一部分。off：数组的开始索引；len：写出的字符个数。
4. `public void write(String str)`：写出字符串。
5. `public void write(String str, int off, int len)` ：写出字符串的某一部分。off：字符串的开始索引；len：写出的字符个数。
6. `public void flush()`：刷新该流的缓冲。
7. `public void close()` ：关闭此流。

> 注意：当完成流的操作时，必须调用close()方法，释放系统资源，否则会造成内存泄漏。另外，字符流常用方法的入参都是与字符相关的`char`和`String`类，更体现了字符流的字符。`Reader/Writer`常用方法`read()/write()`与`InputStream/OutputStream`常用方法`read()/write()`最大的不同就是入参，字符流的入参是字符相关的`char`和`String`类，而字节流的入参是`byte`。

## 3.2 文件字符流 FileReader/FileWriter

文件字符流是字符流`Reader/Writer`的常用实现类，常用方法同`Reader/Writer`，以下是其构造器

`FileReader`构造器：

1. `FileReader(File file)`： 创建一个新的 FileReader ，给定要读取的File对象。
2. `FileReader(String fileName)`： 创建一个新的 FileReader ，给定要读取的文件的名称。

`FileWriter`构造器：

1. `FileWriter(File file)`： 创建一个新的 FileWriter，给定要读取的File对象。
2. `FileWriter(String fileName)`： 创建一个新的 FileWriter，给定要读取的文件的名称。
3. `FileWriter(File file,boolean append)`： 创建一个新的 FileWriter，指明是否在现有文件末尾追加内容。

> 注意：构造时使用系统默认的字符编码（字符编码不同可能会导致乱码，可用转换流转换用指定的编码集为之解码，解决乱码问题）和默认字节缓冲区。

`FileReader`使用示例：

```java
public static void readFileAChar() throws FileNotFoundException, IOException {
    // 读取文件：每次一个字符
    File file = new File(CHAPTER_15_ROOT_PATH + "\\hello.txt");
    FileReader fileReader = new FileReader(file);

    int data;
    // 单次读取一个字符
    while ((data = fileReader.read()) != -1) {
        System.out.println((char) data);
    }
    fileReader.close();
}

public static void readFileChars() throws FileNotFoundException, IOException{
    // 读取文件：每次多个字符,提高读取效率
    File file = new File(CHAPTER_15_ROOT_PATH + "\\hello.txt");
    FileReader fileReader = new FileReader(file);

    // 记录每次实际读入的字符的个数
    int len;
    // 设置每次读取5个字符，注意 len 不一定等于 5
    char[] buffs = new char[5];
    while ((len = fileReader.read(buffs)) != -1) {
        System.out.println(new String(buffs, 0, len));
    }
    fileReader.close();
}
```

`FileWriter`使用示例：

```java
public static void writeFile() throws IOException{
    // 写入新文件，若文件已存在且有内容，则原文件的内容会被覆盖
    FileWriter fileWriter = new FileWriter(new File(CHAPTER_15_ROOT_PATH + "\\file_write.txt"));

    fileWriter.write(97); // 写入ASCII 的 a
    fileWriter.write("\n");

    String text = "writechar";
    fileWriter.write(text.toCharArray()); //写入字符数组
    fileWriter.write("\n");

    // 写入字符数组指定子串，从下标0开始，写入5个字符，即“write”
    fileWriter.write(text.toCharArray(), 0, 5);
    fileWriter.write("\n");

    // 写入字符串
    fileWriter.write(text);
    fileWriter.write("\n");

    // 写入字符串子串, 即“char”
    fileWriter.write(text, 5, 4);
    fileWriter.write("\n");

    fileWriter.close();
}

public static void writeAttach() throws IOException{
    // 附件文件内容，不会覆盖原文件的内容
    FileWriter fileWriter = new FileWriter(new File(CHAPTER_15_ROOT_PATH + "\\hello.txt"), true);
    fileWriter.write("\n");
    fileWriter.write("fileWriter.write(): " + System.currentTimeMillis());
    fileWriter.close();
}
```

## 3.3 字节流(InputStream/OutputStream)

如果我们读取或写出的数据是非文本文件，则Reader、Writer就无能为力了，必须使用字节流。

`InputStream`常用方法：

1. `public int read()`： 从输入流读取一个字节。返回读取的字节值。虽然读取了一个字节，但是会自动提升为int类型。如果已经到达流末尾，没有数据可读，则返回-1。
2. `public int read(byte[] b)`： 从输入流中读取一些字节数，并将它们存储到字节数组 b中 。每次最多读取b.length个字节。返回实际读取的字节个数。如果已经到达流末尾，没有数据可读，则返回-1。
3. `public int read(byte[] b,int off,int len)`：从输入流中读取一些字节数，并将它们存储到字节数组 b中，从`b[off]`开始存储，每次最多读取len个字节 。返回实际读取的字节个数。如果已经到达流末尾，没有数据可读，则返回-1。
4. `public void close() `：关闭此输入流并释放与此流相关联的任何系统资源。

`OutputStream`常用方法：

1. `public void write(int b) `：将指定的字节输出流。虽然参数为int类型四个字节，但是只会保留一个字节的信息写出。
2. `public void write(byte[] b)`：将 b.length字节从指定的字节数组写入此输出流。
3. `public void write(byte[] b, int off, int len) `：从指定的字节数组写入 len字节，从偏移量 off开始输出到此输出流。
4. `public void flush()` ：刷新此输出流并强制任何缓冲的输出字节被写出。
5. `public void close() `：关闭此输出流并释放与此流相关联的任何系统资源。

## 3.4 文件字节流(FileInputStream/FileOutputStream)

`FileInputStream`用于读取文件，常用构造器如下：

1. `FileInputStream(File file)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的 File对象 file命名。
2. `FileInputStream(String name)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的路径名 name命名。

`FileOutputStream`用于写入文件，常用构造器如下：

1. `public FileOutputStream(File file)`：创建文件输出流，写出由指定的 File对象表示的文件。
2. `public FileOutputStream(String name)`： 创建文件输出流，指定的名称为写出文件。
3. `public FileOutputStream(File file, boolean append)`： 创建文件输出流，指明是否在现有文件末尾追加内容。

`FileInputStream`使用示例：

```java
public static void readFileStream() throws IOException{
    // 读单个字节
    FileInputStream fileInputStream = new FileInputStream(
        new File(FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\hello.txt"));
        
    int data;
    while ((data = fileInputStream.read()) != -1) {
        System.out.println((char) data);
    }
    fileInputStream.close();
}

public static void readFileStreamChars() throws IOException{
    // 读多个字节
    FileInputStream fileInputStream = new FileInputStream(
        new File(FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\hello.txt"));
        
    int len;
    byte[] buffs = new byte[5];
    while ((len = fileInputStream.read(buffs)) != -1) {
        System.out.println(new String(buffs, 0, len));
    }
    fileInputStream.close();
}
```

`FileOutputStream`使用示例：

```java
public static void writeFileStream() throws IOException{
    FileOutputStream fileOutputStream = new FileOutputStream(
        new File(FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\write_stream.txt"));

    String text = "stream write";
    fileOutputStream.write(text.getBytes());
    fileOutputStream.write("\n".getBytes());

    fileOutputStream.write(text.getBytes(), 0, 6);
    fileOutputStream.write("\n".getBytes());

    fileOutputStream.write(97); // 写入ASCII中的‘a’
    fileOutputStream.close();
}

public static void writeFileStreamAttach() throws IOException{

    FileOutputStream fileOutputStream = new FileOutputStream(
        new File(FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\write_stream.txt"),
        true);// 附加模式，在原内容之后追加内容
    fileOutputStream.write("\\nAttach\\n".getBytes());
    fileOutputStream.close();
}
```

# 四、缓冲流(Buffered开头的)

为了提高数据读写的速度和效率，Java API提供了带缓冲功能的流类：缓冲流，其常用方法和节点流的基本抽象类一样，只是构造器入参有所不同，缓冲流是处理流的一种，因此其入参类型是节点流，这是处理流不直接读写初始源文件的分类特征一致。

## 4.1 字节缓冲流

`public BufferedInputStream(InputStream in) `：创建一个 新的字节型的缓冲输入流。
`public BufferedOutputStream(OutputStream out)`： 创建一个新的字节型的缓冲输出流。

以下是缓冲流与基本节点流效率的对比测试，示例代码及比对结果如下：

```java
/* 基本节点流（非缓冲流）复制文件 */
public static void copyNotTextFile(String source, String target){
    // 复制非文本文件，如图片
    File sourceFile = new File(
        FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\" + source);
        
    File targetFile = new File(
        FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\" + target);

    FileInputStream fileInputStream = null;
    FileOutputStream fileOutputStream = null;

    int len;
    byte[] buffs = new byte[1024];

    try {
        fileInputStream = new FileInputStream(sourceFile);
        fileOutputStream = new FileOutputStream(targetFile);

        while ((len = fileInputStream.read(buffs)) != -1) {
            fileOutputStream.write(buffs, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fileInputStream != null)
                fileInputStream.close();
        } catch (Exception e){
            e.printStackTrace();
        }
        
        try {
            if (fileOutputStream != null)
                fileOutputStream.close();
        } catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

```java
/* 基本缓冲流复制文件 */
public static void copyByBuffer(String source, String target){

    File sourceFile = new File(
        FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\" + source);

    File targetFile = new File(
        FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\" + target);

    FileInputStream fileInputStream = null;
    FileOutputStream fileOutputStream = null;

    BufferedInputStream bufferedInputStream = null;
    BufferedOutputStream bufferedOutputStream = null;

    int len;
    byte[] buff = new byte[1024];

    try {
        fileInputStream = new FileInputStream(sourceFile);
        fileOutputStream = new FileOutputStream(targetFile);

        bufferedInputStream = new BufferedInputStream(fileInputStream);
        bufferedOutputStream = new BufferedOutputStream(fileOutputStream);

        while ((len = bufferedInputStream.read(buff)) != -1) {
            bufferedOutputStream.write(buff, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (bufferedInputStream != null)
                bufferedInputStream.close();
        } catch (Exception e){
            e.printStackTrace();
        }

        try {
            if (bufferedOutputStream != null)
                bufferedOutputStream.close();
        } catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

```java
/* 结果比对 */
public static void main(String[] args) {
    long start = System.currentTimeMillis();
    CopyFile.copyNotTextFile("girls.png", "girls_copy_" + System.currentTimeMillis() + ".png");
    long end = System.currentTimeMillis();
    System.out.println("节点流复制耗时：" + (end - start));

    start = System.currentTimeMillis();
    CopyFileByBuffer.copyByBuffer("girls.png", "girls_copy_" + System.currentTimeMillis() + ".png");
    end = System.currentTimeMillis();
    System.out.println("缓冲流复制耗时：" + (end - start));
}
```

运行结果：

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231110204224.png)

## 4.2 字符缓冲流

`public BufferedReader(Reader in) `：创建一个 新的字符型的缓冲输入流。
`public BufferedWriter(Writer out)`： 创建一个新的字符型的缓冲输出流。

字符缓冲流特有的方法：

1. 读取数据：`public String readLine()`: 读一行文字。
2. 写入数据：`public void newLine()`: 写一行行分隔符,由系统属性定义符号。

以下是代码示例：

```java

char surname;
String line;
int count;

// 注意  (line = bufferedReader.readLine()) != null 的写法，
// readLine()读到文件末尾(EOF)时，会返回null而不是返回String,
// 因此可通过这一点来判断文件是否读取完毕
while ((line = bufferedReader.readLine()) != null) {
    surname = line.charAt(0);
    if (map.containsKey(surname)){
        count = map.get(surname);
        map.put(surname, count + 1);
    } else {
        map.put(surname, 1);
    }
}
```

# 五、转换流

当读取文本文件时，由于程序读取字符文件到内存时的编码集与磁盘存储该文本文件的编码集可能不一致或不相兼容，会使读入程序的文本或写出到磁盘的文本会出现乱码，为解决此问题，需在程序读写文件时指定编码集，使文本内容一致。

也可简单理解为：转换流是字节与字符间的桥梁，图示如下：

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231111151249.png)

输入流的构造器：

1. `InputStreamReader(InputStream in)`: 创建一个使用默认字符集的字符流。
2. `InputStreamReader(InputStream in, String charsetName)`: 创建一个指定字符集的字符流。

输出流的构造器：

1. `OutputStreamWriter(OutputStream in)`: 创建一个使用默认字符集的字符流。
2. `OutputStreamWriter(OutputStream in,String charsetName)`: 创建一个指定字符集的字符流。

使用示例：

```java
// 把编码集为UTF-8的《s.txt》变为GBK编码的《d.txt》
public static void transfer(String source, String target){

    BufferedReader bufferedReader = null;
    BufferedWriter bufferedWriter = null;

    try {
        bufferedReader = new BufferedReader(
            new InputStreamReader(
                new FileInputStream(FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\" + source),
                    "UTF-8"
                )
            );

        bufferedWriter = new BufferedWriter(
            new OutputStreamWriter(
                new FileOutputStream(FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\" +target),
                "GBK"
            )
        );

        String line;
        while ((line = bufferedReader.readLine()) != null) {
            bufferedWriter.write(line);
            bufferedWriter.newLine();
        }
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        try {
            if (bufferedReader != null)
                bufferedReader.close();
        } catch (Exception e) {
            e.printStackTrace();
        }

        try {
            if (bufferedWriter != null)
                bufferedWriter.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

# 六、数据流与对象流

前面的小结都是将文本文件或二进制文件写入磁盘中，其实Java也支持将基本数据类型（如`int`、`double`等）和对象(Object)写入磁盘中或从磁盘中读取对应的Java基本数据类型和对象。其中：

Java读写基本数据类型（如`int`、`double`等）到文件的类为 `DataInputStream/DataOutputStream`。
Java读写引用数据类型（如`String`等）到文件的类为 `ObjectInputStream/ObjecOutputStream`。

## 6.1 DataInputStream/DataOutputStream

`DataInputStream`常用方法：

>  `byte readByte()`                `short readShort()`  
>  `int readInt()`                  `long readLong()`  
>  `float readFloat()`              `double readDouble()`  
>  `char readChar() `          `boolean readBoolean()`                
>  `String readUTF() `              `void readFully(byte[] b)`

`DataOutputStream`常用方法与`DataInputStream`一样，只需将read改为write即可。另外，需注意的是==读写基本数据类型的顺序要一致，基本数据以什么顺序写入的，就得以什么顺序读出==。

## 6.2 ObjectInputStream/ObjecOutputStream

ObjectInputStream除了具有DataInputStream所拥有的方法外，还有一个方法用于读取引用类型，即`Object readObject()`，类似地，ObjecOutputStream也有方法`writeObject(Object o)`。只是，能被`readObject()/writeObject(Object o)`操作地对象需要能够被序列化和反序列化。

## 6.3 序列化(Serializable)

序列化地过程及原理简易视图如下：

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20231111154742.png)

就使用上而言，被写入文件或从文件被读取的对象须是`Serializable`接口的实现类，即`class ObjSer implements Serializable`，则`ObjSer`能被读写到文件。

> 关于序列化，有以下几点需要注意：

1. 如果对象的某个属性也是引用数据类型，那么如果该属性也要序列化的话，也要实现Serializable 接口。
2. 该类的所有属性必须是可序列化的。如果有一个属性不需要可序列化的，则该属性必须注明是瞬态的，使用transient 关键字修饰。另外，基本数据类型属性是能被序列化的，但如果该属性被关键字`transient`修饰，则不会对其进行序列化，基本数据类型的值会被对应类型的默认值代替（如`int`默认为零，`boolean`默认为`false`等）。
3. 静态（static）变量的值不会序列化。因为静态变量的值不属于某个对象。
4. 需用`static final long serialVersionUID = 27182834235L`指定对应的版本号，以防反序列化时因源码中修改前的类有改变，导致修改源码后Java新生成的版本号`serialVersionUID`与原来的对不上，而报`InvalidClassException`的异常。

代码示例：

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;
import java.util.ArrayList;
import java.util.Scanner;

import chapter15.node03.FileReaderWriterDemo;


/**网上购物时某用户填写订单，订单内容为产品列表，保存在“save.bin”中,
 * 运行时，如果不存在“save.bin”，则进行新订单录入，如果存在，则显示并计算客户所需付款。
 * 编写Save()方法保存对象到 save.bin
 * 编写Load()方法获得对象，计算客户所需付款
*/

public class OrderExer {
    public static void main(String[] args) throws IOException, ClassNotFoundException{
    
        File file = new File(FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\save.bin");
        if (file.exists()) {
            load();
        } else {
            save();
        }
    }

  
    public static void save() throws IOException{
        ArrayList<Product> list = new ArrayList<Product>();
  
        ObjectOutputStream oos = new ObjectOutputStream(
            new FileOutputStream(FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\save.bin"));
        Scanner scanner = new Scanner(System.in);
        
        String name;
        double price;
        int num;
        boolean isContinue = true;
        
        do {
            System.out.print("请输入产品名：");
            name = scanner.next();
            
            System.out.print("请输入单价：");
            price = scanner.nextDouble();

            System.out.print("请输入数量：");
            num = scanner.nextInt();

            System.out.print("是否继续（y/n）：");
            isContinue = scanner.next().equals("y");

            list.add(new Product(name, price, num));
        } while (isContinue);

        oos.writeObject(list);
        scanner.close();
        oos.close();
        System.out.println("订单已保存");
    }

    public static void load() throws IOException, ClassNotFoundException{
    
        ObjectInputStream ois = new ObjectInputStream(
            new FileInputStream(FileReaderWriterDemo.CHAPTER_15_ROOT_PATH + "\\save.bin")
        );

        ArrayList<Product> list = (ArrayList<Product>) ois.readObject();
        double total = 0.0;

        System.out.println("产品名\t单价\t数量");
        for (Product product : list) {
            total += product.price * product.num;
            System.out.println(product.productName + "\t" + product.price + "\t" + product.num);
        }
        ois.close();

        System.out.println("订单总价：" + total);
    }
}
 

class Product implements Serializable{
    static final long serialVersionUID = 27182834235L;
    
    String productName;
    double price;
    int num;

    public Product(String productName, double price, int num){
        this.productName = productName;
        this.price = price;
        this.num = num;
    }

    @Override
    public String toString() {
        return "Product [productName=" + productName + ", price=" + price + ", num=" + num + "]";
    }
}
```


