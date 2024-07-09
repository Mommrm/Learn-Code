## 测试方法

```java
ChannelInboundHandlerAdapter h1 = channelRead(ctx, msg) -> {
    log.debug('1');
    super.channelRead(ctx, msg);
}
ChannelInboundHandlerAdapter h2 = channelRead(ctx, msg) -> {
    log.debug('2');
    super.channelRead(ctx, msg);
}
ChannelOutboundHandlerAdapter h3 = write(ctx, msg, promise) -> {
    log.debug('3');
    super.write(ctx, msg, promise);
}
ChannelOutboundHandlerAdapter h4 = write(ctx, msg, promise) -> {
    log.debug('4');
    super.write(ctx, msg, promise);
}

EmbeddedChannel channel = new EmbeddedChannel();

// 模拟入栈 先打印1再打印2
channel.writeInbound(ByteBufAllocator.DEFAULT.buffer().writeBytes("hello".getBytes()));
// 模拟出栈 先打印4再打印3
channel.writeOutbound(ByteBufAllocator.DEFAULT.buffer().writeBytes("world".getBytes()));

```


## ChannelFuture

```java
// 1. 启动类
Channel channel = new Bootstrap()
        // 2. 添加EventLoop
        .group(new NioEventLoopGroup())
        // 3. 选择客户端 channel实现
        .channel(NioSocketChannel.class)
        // 4. 添加处理器
        .handler(new ChannelInitializer<NioSocketChannel>() {
            @Override
            protected void initChannel(NioSocketChannel ch) throws Exception {
                ch.pipeline().addLast(new StringEncoder());
            }
        })
        // 5.连接到服务器
        .connect(new InetSocketAddress("localhost", 8080))
        .sync()
        .channel();
```

两种方法保证连接后才获取Channel对象
1. 同步ChannelFuture.sync()方法，阻塞当前线程，直到连接成功，获取Channel对象
2. 异步ChannelFuture.addListener()方法，注册一个回调函数，在连接成功后调用，获取Channel对象
```java
public static void main(String[] args) throws InterruptedException {

    ChannelFuture channelFuture = new Bootstrap()
            .group(new NioEventLoopGroup())
            .channel(NioSocketChannel.class)
            .handler(new ChannelInitializer<NioSocketChannel>() {
                @Override
                protected void initChannel(NioSocketChannel ch) throws Exception {
                    ch.pipeline().addLast(new StringEncoder());
                }
            })
            // 异步非阻塞，main发起调用，开启其他线程进行NIO连接 如果不调用sync()方法，main线程会直接向下执行，获取到的是没有建立连接的channel
            .connect(new InetSocketAddress("localhost", 8080));

            //2.1 调用sync()同步处理结果(阻塞main线程，直到NIO线程连接完毕
//                channelFuture.sync();
//                Channel channel = channelFuture.channel();
//                channel.writeAndFlush("hello, world!");

            // 2.2 使用addListener 方法异步处理结果
    channelFuture.addListener(new ChannelFutureListener() {

        // 等待NIO线程处理完毕(连接成功)后调用此函数(回调函数)
        @Override
        public void operationComplete(ChannelFuture channelFuture) throws Exception {
            Channel channel = channelFuture.channel();
            channel.writeAndFlush("hello world");
        }
    });
}
```

两种方法保证关闭后才执行关闭后才执行的操作
1. 同步阻塞方法,等待处理channel关闭的线程处理完毕，后main线程才开始执行sync()后面的方法
```java
// 开启线程去不断输出数据
new Thread(() -> {
    Scanner scanner = new Scanner(System.in);
    String line = scanner.nextLine();
    while(!line.equals("q")){
        channel.writeAndFlush(line);
        line = scanner.nextLine();
    }
    channel.close();
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }
},"input").start();
ChannelFuture closeFuture = channel.closeFuture();
closeFuture.sync();
System.out.println("连接已经完毕,正在处理后续操作...");
```
2. 