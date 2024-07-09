# I/O流

## 简介
IO 即 Input/Output，输入和输出。

输入：数据输入到计算机内存
输出：数据输出到外部存储（比如数据库，文件，远程主机）

按数据处理方式分类：
- 字节流：音频、视频等数据
- 字符流：字符类型，如中文乱码问题

Java IO 流的 40 多个类都是从如下 4 个抽象类基类中派生出来的。
- InputStream/Reader： 所有的输入流的基类，前者是字节输入流，后者是字符输入流。
- OutputStream/Writer: 所有输出流的基类，前者是字节输出流，后者是字符输出流。
# 字节流
## InputStream字节输入流
常用方法：
```java
read() //返回输入流中的下一字节的数据，返回的值介于 0 到 255 之间。如果未读取任何字节，则代码返回 -1 ，表示文件结束。
read(byte b[]) // 从输入流中读取一些字节存储到数组 b 中。如果数组 b 的长度为零，则不读取。如果没有可用字节读取，返回 -1。如果有可用字节读取，则最多读取的字节数最多等于 b.length ， 返回读取的字节数。这个方法等价于 read(b, 0, b.length)。
read(byte b[],int off,int len) //在read(byte b[ ]) 方法的基础上增加了 off 参数（偏移量）和 len 参数（要读取的最大字节数）。
skip(long n) //忽略输入流中的 n 个字节 ,返回实际忽略的字节数。
available() //返回输入流中可以读取的字节数。
close() // 闭输入流释放相关的系统资源。

/**
 * Java 9之后新增
 */
readAllBytes() //读取输入流中的所有字节，返回字节数组。
readNBytes(byte[] b, int off, int len) //阻塞直到读取 len 个字节。
transferTo(OutputStream out) //将所有字节从一个输入流传递到一个输出流。
```

### FileInputStream（字节输入流）
FileInputStream 是一个比较常用的字节输入流对象，可直接指定文件路径，可以直接读取单字节数据，也可以读取至字节数组中。
```java
try (InputStream fis = new FileInputStream("input.txt")) {
    System.out.println("Number of remaining bytes:"
            + fis.available());
    int content;
    long skip = fis.skip(2);
    System.out.println("The actual number of bytes skipped:" + skip);
    System.out.print("The content read from file:");
    while ((content = fis.read()) != -1) {
        System.out.print((char) content);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

输出：
> Number of remaining bytes:11
The actual number of bytes skipped:2
The content read from file:JavaGuide

###### 但是通常不会单独使用FileInputStream
配合以下：
- BufferedInputStream：缓冲
- DataInputStream：读取指定类型数据
- ObjectInputStream：读取Java对象（反序列化）

### OutputStream（字节输出流）
OutputStream用于将数据（字节信息）写入到目的地（通常是文件），java.io.OutputStream抽象类是所有字节输出流的父类。

常用方法：
```java
write(int b) //将特点字节写入到输出流
write(byte b[]) //将数组b写入到输出流 等价于write(b,0,b.length)
write(byte b[],int off,int len) // 增加偏移量和最大字节数
flush() //刷新此输出流并强制写出所有缓冲的输出字节
close()
```

### FileOutputStream()
```java
try(FileOutputStream output = new FileOutputStream("output.txt")){
    byte[] array = "JavaGuide".getBytes();
    output.write(array);
} catch(IOException e) {
    e.printStackTrace();
}
```
会创建一个output.txt的文件，里面内容是JavaGuide

###### 但是通常不会单独使用FileOutputStream
配合以下：
- BufferedOutputStream
- DataOutputStream：读取指定类型数据
    ```java
    // 输出流
    FileOutputStream fileOutputStream = new FileOutputStream("out.txt");
    DataOutputStream dataOutputStream = new DataOutputStream(fileOutputStream);
    // 输出任意数据类型
    dataOutputStream.writeBoolean(true);
    dataOutputStream.writeByte(1);
    ```
- ObjectOutputStream：读取Java对象（反序列化）
    ```java
    ObjectOutputStream output = new ObjectOutputStream(new FileOutputStream("file.txt")
    Person person = new Person("Guide哥", "JavaGuide作者");
    output.writeObject(person);
    ```


# 字符流
不管是文件读写还是网络发送接收，信息的最小存储单元都是字节。 那为什么 I/O 流操作要分为字节流操作和字符流操作呢？
原因：
- 字符流是由 Java 虚拟机将字节转换得到的，这个过程还算是比较耗时。
- 如果我们不知道编码类型就很容易出现乱码问题。

字符流默认采用的是 Unicode 编码，我们可以通过构造方法自定义编码。顺便分享一下之前遇到的笔试题：常用字符编码所占字节数？
- utf8 :英文占 1 字节，中文占 3 字节
- unicode：任何字符都占 2 个字节
- gbk：英文占 1 字节，中文占 2 字节。

## Reader（字符输入流）
Reader用于从源头（通常是文件）读取数据（字符信息）到内存中
Reader 用于读取文本， InputStream 用于读取原始字节。
常用方法：
```java
read() //从输入流读取一个字符
read(char[] cbuf) //从输入流读取字符，并存储到字符数组里面
read(char[] cbuf,int off, int len) // 偏移量off和len读取的最大字符数
skip(long n) //忽略输入流中n个字符，返回实际忽略的字符数
close()
``` 

### InputStreamReader
```java
// 字节流转换为字符流的桥梁
public class InputStreamReader extends Reader {
}
// 用于读取字符文件
public class FileReader extends InputStreamReader {
}
```
试例：
```java
try (FileReader fileReader = new FileReader("input.txt");) {
    int content;
    long skip = fileReader.skip(3);
    System.out.println("The actual number of bytes skipped:" + skip);
    System.out.print("The content read from file:");
    while ((content = fileReader.read()) != -1) {
        System.out.print((char) content);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

## Writer（字符输出流）
Writer用于将数据（字符信息）写入到目的地（通常是文件），java.io.Writer抽象类是所有字符输出流的父类。

常用方法：
```java
write(int c) // 写入单个字符。
write(char[] cbuf) //写入字符数组 cbuf，等价于write(cbuf, 0, cbuf.length)。
write(char[] cbuf, int off, int len)//在write(char[] cbuf) 方法的基础上增加了 off 参数（偏移量）和 len 参数（要读取的最大字符数）。
write(String str) //写入字符串，等价于 write(str, 0, str.length()) 。
write(String str, int off, int len) //在write(String str) 方法的基础上增加了 off 参数（偏移量）和 len 参数（要读取的最大字符数）。
append(CharSequence csq) //将指定的字符序列附加到指定的 Writer 对象并返回该 Writer 对象。
append(char c) //将指定的字符附加到指定的 Writer 对象并返回该 Writer 对象。
flush() //刷新此输出流并强制写出所有缓冲的输出字符。
close() //关闭输出流释放相关的系统资源。
```

### OutputStreamWriter
```java
// 字符流转换为字节流的桥梁
public class OutputStreamWriter extends Writer {
}
// 用于写入字符到文件
public class FileWriter extends OutputStreamWriter {
}
```

示例：
```java
try (Writer output = new FileWriter("output.txt")) {
    output.write("你好，我是Guide。");
} catch (IOException e) {
    e.printStackTrace();
}
```

# 字节缓冲流
IO 操作是很消耗性能的，缓冲流将数据加载至缓冲区，一次性读取/写入多个字节，从而避免频繁的 IO 操作，提高流的传输效率。

字节缓冲流这里采用了装饰器模式来增强 InputStream 和OutputStream子类对象的功能。

### BufferedInputStream（字节缓冲输入流）
BufferedInputStream 从源头（通常是文件）读取数据（字节信息）到内存的过程中不会一个字节一个字节的读取，而是会先将读取到的字节存放在缓存区，并从内部缓冲区中单独读取字节。这样大幅减少了 IO 次数，提高了读取效率。
源码：
```java
public
class BufferedInputStream extends FilterInputStream {
    // 内部缓冲区数组
    protected volatile byte buf[];
    // 缓冲区的默认大小
    private static int DEFAULT_BUFFER_SIZE = 8192;
    // 使用默认的缓冲区大小
    public BufferedInputStream(InputStream in) {
        this(in, DEFAULT_BUFFER_SIZE);
    }
    // 自定义缓冲区大小
    public BufferedInputStream(InputStream in, int size) {
        super(in);
        if (size <= 0) {
            throw new IllegalArgumentException("Buffer size <= 0");
        }
        buf = new byte[size];
    }
}
```

### BufferedOutputStream（字节缓冲输出流）
BufferedOutputStream 将数据（字节信息）写入到目的地（通常是文件）的过程中不会一个字节一个字节的写入，而是会先将要写入的字节存放在缓存区，并从内部缓冲区中单独写入字节。这样大幅减少了 IO 次数，提高了读取效率
示例：
```java
try (BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("output.txt"))) {
    byte[] array = "JavaGuide".getBytes();
    bos.write(array);
} catch (IOException e) {
    e.printStackTrace();
}
```
类似于 BufferedInputStream ，BufferedOutputStream 内部也维护了一个缓冲区，并且，这个缓存区的大小也是 8192 字节。