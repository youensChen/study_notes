# Netty概述

## 使用背景

![image-20210403142634705](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403142634.png)

## 课程内容

![image-20210403142918934](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403142919.png)

## 学习收货

![image-20210403143130907](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403143131.png)

# NIO基础

==nio即为non-blocking io，非阻塞io==



## 三大组件

### Channel 

![image-20210403144248058](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403144248.png)



###  Buffer

==Channel用于传输数据，而写出读入需要有一个内存缓冲区，那就是Buffer==

![image-20210403144330817](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403144330.png)

### Selector

==服务器设计==

> 多线程版

![image-20210403145138141](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403145138.png)

> 线程池版设计

![image-20210403145729522](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403145729.png)

> selector版设计

![image-20210403150530995](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403150531.png)

## ByteBuffer

### ==入门案例==

```java
@Slf4j
public class TestByteBuffer {
    public static void main(String[] args) {
        // 1、通过输入输出流获得FileChannel   2、RandomAccessFile
        try (FileChannel channel = new FileInputStream("data.txt").getChannel()) { //try-with-resources
            //准备缓冲区
            ByteBuffer byteBuffer = ByteBuffer.allocate(10);
            while (true) {
                // 从 channel 读数据，即向 Buffer 写入
                int read = channel.read(byteBuffer);
                log.debug("读取到的字节数{}", read);
                if (read == -1) { //没有更多内容了
                    break;
                }
                //打印Buffer的内容
                byteBuffer.flip(); // 切换到读模式
                while (byteBuffer.hasRemaining()) { //是否还有剩余
                    byte b = byteBuffer.get(); //get()一直读一个字节
                    log.debug("实际字节{}", (char)b);
                }
                byteBuffer.clear(); //切换到写模式
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
// 日志
15:59:16.827 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 读取到的字节数10
15:59:16.831 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节1
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节2
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节3
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节4
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节5
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节6
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节7
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节8
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节9
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节0
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 读取到的字节数6
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节a
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节b
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节c
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节d
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节r
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 实际字节f
15:59:16.832 [main] DEBUG com.youens.netty.c1.TestByteBuffer - 读取到的字节数-1
```

###  ByteBuffer 正确使用姿势

1. 向 buffer 写入数据，例如调用 channel.read(buffer)
2. 调用 flip() 切换至**读模式**
3. 从 buffer 读取数据，例如调用 buffer.get()
4. 调用 clear() 或 compact() 切换至**写模式**
5. 重复 1~4 步骤

###  ByteBuffer 结构

ByteBuffer 有以下重要属性

* capacity
* position
* limit

一开始，刚刚创建

![](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403161038.png)

写模式下，position 是写入位置，limit 等于容量，下图表示写入了 4 个字节后的状态

![](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403161042.png)

==flip==()动作发生后，position 切换为读取位置，limit 切换为读取限制

![](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403161048.png)

读取（调用get() ） 4 个字节后，状态

![](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403161050.png)

==clear()== 动作发生后，状态恢复为刚创建时的状态

![](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403161052.png)

==compact()== 方法，是把未读完的部分向前压缩，然后切换至写模式，==即删除已读的，保留未读的==

![](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403161055.png)

#### 💡 调试工具类

```java
public class ByteBufferUtil {
    private static final char[] BYTE2CHAR = new char[256];
    private static final char[] HEXDUMP_TABLE = new char[256 * 4];
    private static final String[] HEXPADDING = new String[16];
    private static final String[] HEXDUMP_ROWPREFIXES = new String[65536 >>> 4];
    private static final String[] BYTE2HEX = new String[256];
    private static final String[] BYTEPADDING = new String[16];

    static {
        final char[] DIGITS = "0123456789abcdef".toCharArray();
        for (int i = 0; i < 256; i++) {
            HEXDUMP_TABLE[i << 1] = DIGITS[i >>> 4 & 0x0F];
            HEXDUMP_TABLE[(i << 1) + 1] = DIGITS[i & 0x0F];
        }

        int i;

        // Generate the lookup table for hex dump paddings
        for (i = 0; i < HEXPADDING.length; i++) {
            int padding = HEXPADDING.length - i;
            StringBuilder buf = new StringBuilder(padding * 3);
            for (int j = 0; j < padding; j++) {
                buf.append("   ");
            }
            HEXPADDING[i] = buf.toString();
        }

        // Generate the lookup table for the start-offset header in each row (up to 64KiB).
        for (i = 0; i < HEXDUMP_ROWPREFIXES.length; i++) {
            StringBuilder buf = new StringBuilder(12);
            buf.append(NEWLINE);
            buf.append(Long.toHexString(i << 4 & 0xFFFFFFFFL | 0x100000000L));
            buf.setCharAt(buf.length() - 9, '|');
            buf.append('|');
            HEXDUMP_ROWPREFIXES[i] = buf.toString();
        }

        // Generate the lookup table for byte-to-hex-dump conversion
        for (i = 0; i < BYTE2HEX.length; i++) {
            BYTE2HEX[i] = ' ' + StringUtil.byteToHexStringPadded(i);
        }

        // Generate the lookup table for byte dump paddings
        for (i = 0; i < BYTEPADDING.length; i++) {
            int padding = BYTEPADDING.length - i;
            StringBuilder buf = new StringBuilder(padding);
            for (int j = 0; j < padding; j++) {
                buf.append(' ');
            }
            BYTEPADDING[i] = buf.toString();
        }

        // Generate the lookup table for byte-to-char conversion
        for (i = 0; i < BYTE2CHAR.length; i++) {
            if (i <= 0x1f || i >= 0x7f) {
                BYTE2CHAR[i] = '.';
            } else {
                BYTE2CHAR[i] = (char) i;
            }
        }
    }

    /**
     * 打印所有内容
     * @param buffer
     */
    public static void debugAll(ByteBuffer buffer) {
        int oldlimit = buffer.limit();
        buffer.limit(buffer.capacity());
        StringBuilder origin = new StringBuilder(256);
        appendPrettyHexDump(origin, buffer, 0, buffer.capacity());
        System.out.println("+--------+-------------------- all ------------------------+----------------+");
        System.out.printf("position: [%d], limit: [%d]\n", buffer.position(), oldlimit);
        System.out.println(origin);
        buffer.limit(oldlimit);
    }

    /**
     * 打印可读取内容
     * @param buffer
     */
    public static void debugRead(ByteBuffer buffer) {
        StringBuilder builder = new StringBuilder(256);
        appendPrettyHexDump(builder, buffer, buffer.position(), buffer.limit() - buffer.position());
        System.out.println("+--------+-------------------- read -----------------------+----------------+");
        System.out.printf("position: [%d], limit: [%d]\n", buffer.position(), buffer.limit());
        System.out.println(builder);
    }

    private static void appendPrettyHexDump(StringBuilder dump, ByteBuffer buf, int offset, int length) {
        if (isOutOfBounds(offset, length, buf.capacity())) {
            throw new IndexOutOfBoundsException(
                    "expected: " + "0 <= offset(" + offset + ") <= offset + length(" + length
                            + ") <= " + "buf.capacity(" + buf.capacity() + ')');
        }
        if (length == 0) {
            return;
        }
        dump.append(
                "         +-------------------------------------------------+" +
                        NEWLINE + "         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |" +
                        NEWLINE + "+--------+-------------------------------------------------+----------------+");

        final int startIndex = offset;
        final int fullRows = length >>> 4;
        final int remainder = length & 0xF;

        // Dump the rows which have 16 bytes.
        for (int row = 0; row < fullRows; row++) {
            int rowStartIndex = (row << 4) + startIndex;

            // Per-row prefix.
            appendHexDumpRowPrefix(dump, row, rowStartIndex);

            // Hex dump
            int rowEndIndex = rowStartIndex + 16;
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2HEX[getUnsignedByte(buf, j)]);
            }
            dump.append(" |");

            // ASCII dump
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2CHAR[getUnsignedByte(buf, j)]);
            }
            dump.append('|');
        }

        // Dump the last row which has less than 16 bytes.
        if (remainder != 0) {
            int rowStartIndex = (fullRows << 4) + startIndex;
            appendHexDumpRowPrefix(dump, fullRows, rowStartIndex);

            // Hex dump
            int rowEndIndex = rowStartIndex + remainder;
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2HEX[getUnsignedByte(buf, j)]);
            }
            dump.append(HEXPADDING[remainder]);
            dump.append(" |");

            // Ascii dump
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2CHAR[getUnsignedByte(buf, j)]);
            }
            dump.append(BYTEPADDING[remainder]);
            dump.append('|');
        }

        dump.append(NEWLINE +
                "+--------+-------------------------------------------------+----------------+");
    }

    private static void appendHexDumpRowPrefix(StringBuilder dump, int row, int rowStartIndex) {
        if (row < HEXDUMP_ROWPREFIXES.length) {
            dump.append(HEXDUMP_ROWPREFIXES[row]);
        } else {
            dump.append(NEWLINE);
            dump.append(Long.toHexString(rowStartIndex & 0xFFFFFFFFL | 0x100000000L));
            dump.setCharAt(dump.length() - 9, '|');
            dump.append('|');
        }
    }

    public static short getUnsignedByte(ByteBuffer buffer, int index) {
        return (short) (buffer.get(index) & 0xFF);
    }
}
```

#### 调试

```java
public static void main(String[] args) {
        ByteBuffer byteBuffer = ByteBuffer.allocate(10);
        byteBuffer.put((byte) 0x61); // 'a'
        byteBuffer.put(new byte[]{0x62, 0x63, 0x64}); //b c d
        debugAll(byteBuffer); // 打印byteBuffer
        // System.out.println(byteBuffer.get());不切换模式直接读将读到0
        byteBuffer.flip();
        System.out.println((char)byteBuffer.get());
        debugAll(byteBuffer);

        byteBuffer.compact();
        debugAll(byteBuffer);
        byteBuffer.put(new byte[]{0x65, 0x66, 0x67}); //e f g
        debugAll(byteBuffer);
    }
// 输出
com.youens.netty.c1.TestBufferReadWrite
16:49:02.513 [main] DEBUG io.netty.util.internal.logging.InternalLoggerFactory - Using SLF4J as the default logging framework
+--------+-------------------- all ------------------------+----------------+
position: [4], limit: [10]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 61 62 63 64 00 00 00 00 00 00                   |abcd......      |
+--------+-------------------------------------------------+----------------+


a
+--------+-------------------- all ------------------------+----------------+
position: [1], limit: [4]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 61 62 63 64 00 00 00 00 00 00                   |abcd......      |
+--------+-------------------------------------------------+----------------+


+--------+-------------------- all ------------------------+----------------+
position: [3], limit: [10]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 62 63 64 64 00 00 00 00 00 00                   |bcdd......      |
+--------+-------------------------------------------------+----------------+


+--------+-------------------- all ------------------------+----------------+
position: [6], limit: [10]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 62 63 64 65 66 67 00 00 00 00                   |bcdefg....      |
+--------+-------------------------------------------------+----------------+



Process finished with exit code 0
```



### ByteBuffer 常见方法

#### 分配空间

可以使用 allocate 方法为 ByteBuffer 分配空间，其它 buffer 类也有该方法

```java
// 分配后的容量是固定的，不可再动态分配
//Netty对byteBuffer进行了增强，可动态分配容量
ByteBuffer buf = ByteBuffer.allocate(16);
	class java.nio.HeapByteBuffer	
// 使用堆内存，读写效率较低，受到 GC 的影响
        
        
ByteBuffer.allocateDirect(10)
     class java.nio.DirectByteBuffer
// 使用直接内存，效率较高（少一次拷贝），系统内存，不受 GC 的影响，分配较慢，需要合理的释放，否则可能造成内存泄漏
```



#### 向 buffer 写入数据

有两种办法

* 调用 channel 的 read 方法
* 调用 buffer 自己的 put 方法

```java
int readBytes = channel.read(buf);
```

和

```java
buf.put((byte)127);
```



#### 从 buffer 读取数据

同样有两种办法

* 调用 channel 的 write 方法
* 调用 buffer 自己的 get 方法

```java
int writeBytes = channel.write(buf);
```

和

```java
byte b = buf.get();
```

get 方法会让 position 读指针向后走，如果想重复读取数据

* 可以调用 ==rewind== 方法将 position 重新置为 0
* 或者调用 get(int i) 方法获取索引 i 的内容，它不会移动读指针



#### mark 和 reset

mark 是在读取时，做一个标记(即记录当前的position)，即使 position 改变，只要调用 reset 就能回到 mark 的位置

> **注意**
>
> rewind 和 flip 都会清除 mark 位置



#### 字符串与 ByteBuffer 互转

```java
// 字符串转ByteBuffer
// 1、先转为字符数组然后写到ByteBuffer
ByteBuffer buffer = ByteBuffer.allocate(16);
buffer.put("hello".getBytes());
debugAll(buffer);

//2、下面两种方法都会申请一个容量为字符串长度的byteBuffer，且自动切换为读模式	
ByteBuffer buffer1 = StandardCharsets.UTF_8.encode("你好");
//3、
ByteBuffer buffer2 = Charset.forName("utf-8").encode("你好");
//4、
ByteBuffer buffer3 = ByteBuffer.wrap("hello".getBytes());
        
debug(buffer1);
debug(buffer2);
// ByteBuffer转字符串
CharBuffer charBuffer = StandardCharsets.UTF_8.decode(buffer1); //在decode之前要确保byteBuffer是读模式
System.out.println(charBuffer.getClass());
System.out.println(charBuffer.toString());
```

输出

```
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| e4 bd a0 e5 a5 bd                               |......          |
+--------+-------------------------------------------------+----------------+
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| e4 bd a0 e5 a5 bd                               |......          |
+--------+-------------------------------------------------+----------------+
class java.nio.HeapCharBuffer
你好
```

#### ⚠️ Buffer 的线程安全

> Buffer 是**非线程安全的**



### Scattering Reads

分散读取，有一个文本文件 3parts.txt

```
onetwothree
```

使用如下方式读取，可以将数据填充至多个 buffer

```java
try (RandomAccessFile file = new RandomAccessFile("helloword/3parts.txt", "rw")) {
    FileChannel channel = file.getChannel();
    ByteBuffer a = ByteBuffer.allocate(3);
    ByteBuffer b = ByteBuffer.allocate(3);
    ByteBuffer c = ByteBuffer.allocate(5);
    channel.read(new ByteBuffer[]{a, b, c});
    a.flip();
    b.flip();
    c.flip();
    debug(a);
    debug(b);
    debug(c);
} catch (IOException e) {
    e.printStackTrace();
}
```

结果

```
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 6f 6e 65                                        |one             |
+--------+-------------------------------------------------+----------------+
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 74 77 6f                                        |two             |
+--------+-------------------------------------------------+----------------+
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 74 68 72 65 65                                  |three           |
+--------+-------------------------------------------------+----------------+
```





### Gathering Writes

使用如下方式写入，可以将多个 buffer 的数据填充至 channel

```java
try (RandomAccessFile file = new RandomAccessFile("helloword/3parts.txt", "rw")) {
    FileChannel channel = file.getChannel();
    ByteBuffer d = ByteBuffer.allocate(4);
    ByteBuffer e = ByteBuffer.allocate(4);
    channel.position(11);

    d.put(new byte[]{'f', 'o', 'u', 'r'});
    e.put(new byte[]{'f', 'i', 'v', 'e'});
    d.flip();
    e.flip();
    debug(d);
    debug(e);
    channel.write(new ByteBuffer[]{d, e});
} catch (IOException e) {
    e.printStackTrace();
}
```

输出

```
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 66 6f 75 72                                     |four            |
+--------+-------------------------------------------------+----------------+
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 66 69 76 65                                     |five            |
+--------+-------------------------------------------------+----------------+
```

文件内容

```
onetwothreefourfive
```





### 练习





## 文件编程



## 网络编程



## BIO VS BIO