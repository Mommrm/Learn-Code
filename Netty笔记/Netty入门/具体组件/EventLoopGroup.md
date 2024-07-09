## EventLoopGroup

### 普通任务和定时任务的用法
```java
//1. 创建事件循环组
EventLoopGroup eventLoopGroup = new NioEventLoopGroup(); //处理IO，普通任务，定时任务
EventLoopGroup defaultLoopGroup = new DefaultEventLoopGroup(); //普通任务，定时任务

// 2.获取事件循环对象
EventLoop eventLoop = eventLoopGroup.next();

// 执行普通任务
eventLoop.execute(() -> {
    System.out.println("普通任务");
});

eventLoop.next().scheduleAtFixedRate( () -> {
    System.out.println("定时任务: 立马执行，间隔5秒");
},0, 5, TimeUnit.SECONDS);
```

### IO任务的用法
服务端
```java
@Slf4j
public class Server {
    public static void main(String[] args) {
        // 固定写法
        new ServerBootstrap()
                .group(new NioEventLoopGroup())
                .channel(NioServerSocketChannel.class)
                .childHandler(new ChannelInitializer<NioSocketChannel>() {

                    @Override
                    protected void initChannel(NioSocketChannel ch) throws Exception {
                        //不固定写法
                        ch.pipeline().addLast(new ChannelInboundHandlerAdapter(){
                            @Override
                            public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                                ByteBuf buf = (ByteBuf) msg;
                                // 这里最好不要默认字符集
                                log.debug(buf.toString(Charset.defaultCharset()));
                            }
                        });
                    }
                })
                .bind(8080);
    }
}

```

客户端
```java
public class Client {
    public static void main(String[] args) throws InterruptedException {
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

        System.out.println(channel);
        System.out.println();

    }
}
```

### 细分eventLoop职责

1. 细分boss和worker
```java
@Slf4j
public class Server {
    public static void main(String[] args) {
        // 固定写法
        new ServerBootstrap()
                // 第一个参数是Boss负责处理accept事件， 第二个参数是worker，负责其他事件
                .group(new NioEventLoopGroup(),new NioEventLoopGroup(2))
                .channel(NioServerSocketChannel.class)
                .childHandler(new ChannelInitializer<NioSocketChannel>() {

                    @Override
                    protected void initChannel(NioSocketChannel ch) throws Exception {
                        //不固定写法
                        ch.pipeline().addLast(new ChannelInboundHandlerAdapter(){
                            @Override
                            public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                                ByteBuf buf = (ByteBuf) msg;
                                // 这里最好不要默认字符集
                                log.debug(buf.toString(Charset.defaultCharset()));
                            }
                        });
                    }
                })
                .bind(8080);
    }
}
```

2. 细分任务
```java
@Slf4j
public class Server {
    public static void main(String[] args) {
        EventLoopGroup group = new DefaultEventLoopGroup();
        // 固定写法
        new ServerBootstrap()
                // 第一个参数是Boss负责处理accept事件， 第二个参数是worker，负责其他事件
                .group(new NioEventLoopGroup(),new NioEventLoopGroup(2))
                .channel(NioServerSocketChannel.class)
                .childHandler(new ChannelInitializer<NioSocketChannel>() {

                    @Override
                    protected void initChannel(NioSocketChannel ch) throws Exception {
                        //不固定写法
                        ch.pipeline().addLast("handler1",new ChannelInboundHandlerAdapter(){
                            @Override
                            public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                                ByteBuf buf = (ByteBuf) msg;
                                // 这里最好不要默认字符集
                                log.debug(buf.toString(Charset.defaultCharset()));
                                // 一定要写这个代码，不然下一个处理器不会接收到msg消息
                                ctx.fireChannelRead(msg); //将消息传递给下一个Handler
                            }
                        }).addLast(group,"handler2",new ChannelInboundHandlerAdapter() {
                            @Override
                            public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                                ByteBuf buf = (ByteBuf) msg;
                                log.debug(buf.toString(Charset.defaultCharset()));
                            }
                        });
                    }
                })
                .bind(8080);
    }
}

```

### Handler是怎么切换线程的？

AbstractChannelHandlerContext#invokeChannelRead()源码:
```java
static void invokeChannelRead(AbstractChannelHandlerContext next, Object msg) {
    final object m = next.pipeline.touch(ObjectUtil.checkNotNull(msg, "msg").next);
    // 下一个Handler对象
    EventExecutor executor = next.executor();
    // 是，直接调用
    if (executor.inEventLoop()){
        next.invokeChannelRead(m);
    }
    // 不是，将将要执行的代码作为任务提交给下一个事件循环处理(切换EventLoopGroup中的EventLoop即为线程)
    else {
        executor.execute(new Runable(){
            @Override
            public void run(){
                next.invokeChannelRead(m);
            }
        })
    }
}
```

原理：
判断下一个handler对象的EventLoop是否和当前的Handler的EventLoop是同一个，如果不是，则切换线程。
