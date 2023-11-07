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


