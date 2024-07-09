## Handler和Pipeline

### Pipeline
header -> h1 -> h2 -> h3 -> h4 -> h5 -> h6 -> tail
其中h1、h2、h3为ChannelInboundHandler，h4、h5、h6为ChannelOutboundHandler
而OutboundHandler只能由写出数据来触发handler
handler执行顺序：
ch 是 NioSocketChannel
ctx 是当前handler节点

#### 读取ChannelInboundHandler：
super.channelRead(ctx, msg); // 触发下一个handler的channelRead方法


#### 写入ChannelOutboundHandler：
ch.writeAndFlush(msg); // 从tail开始，从左往右调用handler
ctx.writeAndFlush(msg); // 从当前handler节点开始，从左往右调用handler