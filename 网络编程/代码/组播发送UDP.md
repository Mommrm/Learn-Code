```java
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
```
