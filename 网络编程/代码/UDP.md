发送数据

```java
// 有参: 指定端口号
// 无参: 随机一个可用端口号 发出
DatagramSocket ds = new DatagramSocket();
//数据准备
String str =  "hello socket";
byte[] bytes = str.getBytes();
InetAddress address = InetAddress.getByName("127.0.0.1");
int port = 10086;
// 打包数据 这里端口号是发往10086端口
DatagramPacket dp = new DatagramPacket(bytes,bytes.length,address,port);
// 发送数据
ds.send(dp);
// 释放资源
ds.close();
```

接收数据
```java
// 接收 绑定对应端口
DatagramSocket ds = new DatagramSocket(10086);

// 接收数据包
byte[] bytes = new byte[1024];
DatagramPacket dp = new DatagramPacket(bytes,bytes.length);
ds.receive(dp);

// 解析数据包
byte[] data = dp.getData();
int dpLength = dp.getLength();
InetAddress dpAddress = dp.getAddress();
int dpPort = dp.getPort();
System.out.println("接收到的数据是: " + new String(data,0,dpLength));
System.out.println("发送的地址是: " + dpAddress + " 端口是: " + dpPort);

//释放资源
ds.close();

```

聊天室
```java
// 发送代码
DatagramSocket ds = new DatagramSocket();
//数据准备
String str = "";
InetAddress address = InetAddress.getByName("127.0.0.1");
int port = 10086;
Scanner scanner = new Scanner(System.in);

while(!str.equals("886")){
    str = scanner.nextLine();
    byte[] bytes = str.getBytes();
    // 打包数据 这里端口号是发往10086端口
    DatagramPacket dp = new DatagramPacket(bytes,bytes.length,address,port);
    // 发送数据
    ds.send(dp);
}
// 释放资源
ds.close();


// 接收代码

// 接收 绑定对应端口
DatagramSocket ds = new DatagramSocket(10086);
// 接收数据包
byte[] bytes = new byte[1024];
DatagramPacket dp = new DatagramPacket(bytes,bytes.length);
while(true){
    ds.receive(dp);

    // 解析数据包
    byte[] data = dp.getData();
    int dpLength = dp.getLength();
    InetAddress dpAddress = dp.getAddress();
    int dpPort = dp.getPort();

    String str = new String(data,0,dpLength);
    System.out.println("接收到的数据是: " + str);
    System.out.println("发送的地址是: " + dpAddress + " 端口是: " + dpPort);
    if(str.equals("886")){
        System.out.println("结束连接");
        break;
    }
}

//释放资源
ds.close();
```