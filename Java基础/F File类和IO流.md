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


# 三、字符节点流(Reader/Writer)

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