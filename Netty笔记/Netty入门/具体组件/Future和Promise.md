## JDK中的Future

```java
Futrue<String> future = executor.submit(new Callable<String>() {
    @Override
    public String call() throws Exception {
        // do something
        return "result";
    }
});

String result = future.get();
```

## EventLoop中的Future

```java
Futrue<String> future = eventLoop.submit(new Callable<String>() {
    @Override
    public String call() throws Exception {
        // do something
        return "result";
    }
});

String result = future.get();
```

## EventLoop中的Promise

```java
DefaultPromise<String> promise = new DefaultPromise<>(eventLoop);

new Thread(() -> {
    Thread.sleep(1000);
    promise.setSuccess("success");
})

promise.get(); 
```