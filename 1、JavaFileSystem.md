# Java File System

###### 1、File类

（1）File & IO

一谈起文件我们心里就会浮现出两个概念"文件(file)"和"文件夹(directory)"，在java中这二者统称文件，使用File类来描述。

举个栗子~ 在我电脑上有个"文件夹"和"文件" 我们就可以使用File类来描述二者。

![](https://gitee.com/sunnnydaydev/my-pictures/raw/master/github/file/file_dir.png)
![](https://gitee.com/sunnnydaydev/my-pictures/raw/master/github/file/file_file.png)

```kotlin
fun main(){
    val fileDir = File("/Users/zennioptical/JavaFilePractice")
    println("name:${fileDir.name}")
    val fileFile = File("/Users/zennioptical/JavaFilePractice/1.txt")
    println("name:${fileFile.name}")
    /**
     * log:
     * name:JavaFilePractice
     * name:1.txt
     * */
}
```
可见当传入的文件路径是文件夹的路径时这个File就代表是一个文件夹文件，当传入的路径是一个普通的文件时这个File就代表是一个
普通的文件。

那么啥叫IO呢？IO是Input和OutPut的简称，意思就是输入输出。 在计算机中，IO就是"程序"与"磁盘"进行交互的操作。程序很好理
解就是跑在内存中的，"磁盘"可以是本计算机的硬盘、外部存储设备、其他电脑磁盘。

IO就像水流一样具有"流向"，输出就是流出，输入就是流入。输入和输出相对于内存，从内存往磁盘写数据就是输出，从磁盘往内存读数
据就是输入。先看张图结合理解下，具体的IO操作就要涉及到下章节"字符流与字节流"的概念了。

![](https://gitee.com/sunnnydaydev/my-pictures/raw/master/github/file/file_io.png)

(2)File api

java 使用File类来描述文件，那么我们首先看下File类常见的api。

- public File(String pathname):给定文件路径生成一个file类。
- public File(String parent, String child):给定文件的parent路径、child路径，生成一个File类。
- public File(File parent, String child):给定一个parent File、child路径，生成一个File类。

看下构造第一个很好理解，直接指明文件所在磁盘的绝对路径即可，这样直接通过构造就可以获取一个file对象，然后就可以使用file
对象来描述这个文件了。

后两个构造乍一看有点迷惑。其实简单看下源码就会知道："后两个构造和第一个构造类似，第一个构造直接把获取到的path赋值给自己
的path成员变量，后两个构造会把拿到的参数通过一些列操作转化为path赋值给自己的path成员变量"

接下来看下这三个构造如何使用的~

```kotlin

        const val path = "/Users/zennioptical/JavaFilePractice/1.txt"

        const val parent = "/Users/zennioptical"
        const val child = "JavaFilePractice/1.txt"

        fun main() {
        // 构造1，直接传参数完整path
        val file1 = File(path)
        println("name:${file1.name}")

        // 构造2，传递父、子 path。
        val file2 = File(parent, child)
        println("name:${file2.name}")

        // 构造3，构造一个父文件传递过来，然后传递一个子路径。
        val file3 = File(File(parent), child)
        println("name:${file3.name}")

        /**
         log：
         name:1.txt
         name:1.txt
         name:1.txt
         * */
        }
```

- public boolean exists()文件是否存在
- boolean isFile()是否是个普通的文件
- public boolean isDirectory()是否是目录文件
- public String getName()获取文件名（带后缀）
- public String getAbsolutePath()获取文件绝对路径名
- public long length()获取文件大小（单位字节）
- public boolean createNewFile()创建文件（创建失败报错）
- public boolean delete()删除文件
- public boolean mkdir()创建文件夹（父文件夹不存在时创建失败返回false）
- public boolean mkdirs()创建文件夹（父文件夹不存在时会同时创建父文件夹）
- public File[] listFiles()返回一个文件数组
- public boolean isHidden()是否是隐藏文件
- public boolean renameTo(File dest)重命名文件

看下文件的重命名操作,JavaFilePractice目录下原有的1.txt会被重命名为2.txt
```kotlin
fun main() {
    val file = File( "/Users/zennioptical/JavaFilePractice/1.txt")
    val desc = File( "/Users/zennioptical/JavaFilePractice/2.txt")
    file.renameTo(desc)
}
```
在看下这个，可以发现重命名操作也可以当做"移动"操作来使用。原本JavaFilePractice下的1.txt被移动到了
JavaFilePractice/Test下且重命名了。
```kotlin
fun main() {
    val file = File( "/Users/zennioptical/JavaFilePractice/1.txt")
    val desc = File( "/Users/zennioptical/JavaFilePractice/Test/2.txt")
    file.renameTo(desc)
}
```

###### 2、IO流

（1）流的分类

IO流按照流向分类可以分为输入流，输出流。

- 往内存中读的叫做输入流
- 从内存中往外写的叫做输出流
- 所有输入流都是InputStream或Reader的子类
- 所有输出流都是OutputStream或Writer的子类

IO流按照数据流的编码格式上可分为字符流，字节流。

- InputStream、OutputStream及其子类为字节流
- 字节流主要处理二进制数据，如读写音频、图片等比较常用，处理单元为1字节。
- 字符流主要处理字符数据。如配置文件、json等字符相关的读取。

（2）既然有了字节流，为什么还要有字符流呢？

二者处理的对象不同，字节流更倾向于处理字节数据，字符流更倾向处理文本内容。

其实直接使用字节流读取二进制文件（音频、视频等）文本文件（普通文本、配置文件、json等）都是可以的，但是文本文件内的字符就
涉及到了编码，使用字节流读取时需要手动指定编码方式，否则一不留意就会出现读取内容乱码问题。因此为了方便java又提供了字符流
用于方便专门读取字符。

同时java为了满足一些情况还提供了字节流转字符流桥梁：

- public InputStreamReader(InputStream in)//默认utf-8 编码，也可以使用两参数构造指定编码
- public OutputStreamWriter(OutputStream out)//默认utf-8 编码，也可以使用两参数构造指定编码


（2）常见核心类

![](![](https://gitee.com/sunnnydaydev/my-pictures/raw/master/github/file/file_io_chart.png))


###### 3、IO流的常见使用

(1) 字符流FileWriter

```kotlin
/**
 * FileWriter栗子：往磁盘写文本。
 * */
fun fileWriter() {
    val file = File("/Users/zennioptical/JavaFilePractice/1.txt")
    if (!file.exists()) {
        file.createNewFile()
    }
    val fw = FileWriter(file)
    fw.write("落霞与孤鹜齐飞，秋水与长天一色。")
    fw.flush()//把数据从内存缓冲区刷到磁盘
    fw.close()// 关闭输出流
}
```
- 一般在IO流中数据是先被读写到内存缓冲区的，因此当我们读完或写完并不代表真正的读写完，当缓冲区的数据被刷出去才代表真正的完成。
- 上述例子把flush、close注释了本地文件无数据。
- 上述例子把flush注释了本地文件有数据，因为close时会刷下。
- 上述例子把close注释了本地有数据，流未关闭可能会引起内存泄漏。


（2）字符流FileReader

```kotlin
/**
 * FileReader栗子：
 * */
fun fileReader() {
    val file = File("/Users/zennioptical/JavaFilePractice/1.txt")
    val fr = FileReader(file)
    var index = fr.read() //读取一个字符，流结束时这个api返回-1
    while (index != -1) {
        print(index.toChar())
        index= fr.read()
    }
    fr.close()
}
```
可见上述的读取是一个字符一个字符去循环读取的，读取效率很低下。其实read()方法提供了几个常见的重载，最终都是调用三个参数的
重载方法：

```java
    public int read() throws IOException {
        char cb[] = new char[1];
        if (read(cb, 0, 1) == -1)
            return -1;
        else
            return cb[0];
    }

public int read(char cbuf[]) throws IOException {
    return read(cbuf, 0, cbuf.length);
}

abstract public int read(char cbuf[], int off, int len) throws IOException;

```
直接看三个参数的重载，read方法会把字符读取到一个cbuf中。

off指的是读取偏移量，表示从目标文本的第几个字符开始读取，一般这个参数都穿0表示从第一个字符开始读。

len指的是读取字符的最大数量，这个还真不好定义，一般我们都会申请一个足够大的数组，然后使用数组的长度read(char cbuf[])
就是这样做的。


```kotlin
fun fileReader1(){
    val file = File("/Users/zennioptical/JavaFilePractice/1.txt")
    val fr = FileReader(file)
    val buffer = CharArray(30)
    fr.read(buffer)
    buffer.forEach {
       print("$it")
    }
    fr.close()
}
//落霞与孤鹜齐飞，秋水与长天一色。              
```
可见使用数组的读法可能我们控制不好字符的总数，导致申请多余的数组空间、读取多余的数据。

但是这个方法是对三个参数的封装，当需求一致时可以使用这个方法来精简代码。

（3）使用BufferedReader来提高字符读的效率

```kotlin
/**
 * 使用BufferedReader来提升读取效率，这玩意提供了readLine方法每次可读一行。
 * */
fun bufferedReader(){
    val file = File("/Users/zennioptical/JavaFilePractice/1.txt")
    val fr = FileReader(file)
    val bf = BufferedReader(fr)
    var str = bf.readLine()
    while (str!=null){
        print(str)
        str = bf.readLine()
    }
    fr.close()
}
```
- FileReader的read方法每次只能读取一个字符，效率不是很高，可以结合BufferedReader来使用，这个类提供了每次读取一行文本
的方法，极大提升了读取效率。
- readLine方法读取一行文本返回String,读完时流结束就返回null。
- 同样可以使用BufferedWriter来提升写的效率。

（4）FileOutputStream

```kotlin
fun fileOutputStream(){
    val file = File("/Users/zennioptical/JavaFilePractice/2.txt")
    if (!file.exists()){
        file.createNewFile()
    }
    val fos = FileOutputStream(file)
    fos.write("滕王阁序".toByteArray(Charsets.UTF_8))
    fos.close()
}
```
- write提供了几个重载，但一般都是把内容转为字节数组。
- 字符串可指定编码方式，默认是UTF_8。这个是方便读取时解码不至于乱码。

（5）FileInputStream

```kotlin
fun fileInputStream() {
    val file = File("/Users/zennioptical/JavaFilePractice/2.txt")
    val fis = FileInputStream(file)
    //注意这里申请的大小可为文件的大小，因为文件的大小就是字节总数
    val byteArray = ByteArray(file.length().toInt())
    fis.read(byteArray)
    //通过String构造来转为字符串,并通过UTF_8方式来解码
    val string = String(byteArray, Charsets.UTF_8)
    println(string)
    fis.close()
}
```
```kotlin
    // FileInputStream#read
    public int read(byte b[], int off, int len) throws IOException {
        // Android-added: close() check before I/O.
        if (closed && len > 0) {
            throw new IOException("Stream Closed");
        }
        // Android-added: Tracking of unbuffered I/O.
        tracker.trackIo(len);
        // Android-changed: Use IoBridge instead of calling native method.
        //底层还是调用了4个参数的重载
        return IoBridge.read(fd, b, off, len);
    }
```

- 字节流与字符流很类似，字符流一般使用char数组读，字节流则使用byte数组读。
- read的重载方法与字符流类似，若是直接使用read()则需要手动循环读每个字节然后强转为char
- read的重载方法与字符流类似，若是直接使用4个参数的重载则需要一个缓冲数组，然后手写实现细节。
- 这里直接使用了一个参数的重载，传递字节数组。底层还是三个参数重载的实现。

此方式有个弊端，一次申请的内存过大，我们可以申请小一点字节数组来循环读取

```kotlin
fun fileInputStream1() {
    val file = File("/Users/zennioptical/JavaFilePractice/2.txt")
    val fis = FileInputStream(file)
    val byteArray = ByteArray(1024)//读取字节个数由我们控制
    //读取byteArray size个字节到字节数组中。流中小于size时则以真实个数为主。
    // 返回值为读取个数
    var data = fis.read(byteArray) 
    while (data != -1) {
        println("data:$data")
        val string = String(byteArray, 0, data)
        println(string)
        data = fis.read(byteArray)
    }
    fis.close()
}
```

（5）使用BufferedInputStream来提高字节读的效率




###### 4、文件的压缩解压操作

- 压缩
- 解压缩

###### 5、kotlin对io的扩展

参考文章：https://juejin.cn/post/6844903704680726535


###### 6、文件工具类

可把常见的操作抽成工具，这里主要是基础工具类。可能有些安卓不适用，到时候需要看情况分开总结。

###### 参考

[JAVA中使用字节输入流读取中文数据中文乱码问题解决方案](https://www.freesion.com/article/95201134507/)

[编码方式有哪些_简述常用的编码方式](https://m.elecfans.com/article/1050823.html)
