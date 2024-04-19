客户端
```java
Socket socket = new Socket("127.0.0.1",10000);

```

服务端
```java
ServerSocket serverSocket = new ServerSocket(10000);
Socket socket = serverSocket.accept();
```