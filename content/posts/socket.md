---
title: "Socket"
date: 2021-04-11T14:20:18+08:00
draft: false
---

# Socket 用法详解

## 1 构造 Socket

构造方法的重载方式

```java
Socket();

Socket(InetAddress address, int port) throws UnknownHostException, IOExpection;

Socket(InetAddress address, int port, InetAddress localAddr, int localPort) throws IOExpection;

Socket(String host, int port) throws UnknownHostException, IOException;

Socket(String host, int port, InetAddress localAddr, int localPort) throws IOException;

Socket(Proxy proxy);
```

## 1.1 设定等待建立连接的超时时间

```java
Socket socket = new Socket();
SocketAddress remoteAddr=new InetSocketAddress("localhost", 8000);
socket.connect(remoteAddr, 60000); // 等待建立连接的超时时间为1分钟
```

**SocketAddress 类** 表示一个套接字地址

> `Socket.connect(SocketAddress endpoint, int timeout)` 方法：负责连接服务器
> > 参数endpoint：指定服务器的地址。<br/>
> > 参数timeout：设定的超时时间，以ms为单位；设为0，表示永远不会超时。<br/>


## 1.2 设定服务器地址

构造方法：

```java
// 第 1 个参数 address 表示主机的 IP 地址
Socket(InetAddress address, int port);

// 第 1 个参数 address 表示主机名字
Socket(String host, int port);
```

静态工厂方法——表示主机的IP地址：

```java
// 返回本地主机的 IP 地址
InetAddress addr1 = InetAddress.getLocalHost();

// 返回代表“222.34.5.7”的 IPv4 地址
InetAddress addr2 = InetAddress.getByName("222.34.5.7");

// 返回代表“2001:DB8:2DE::E13”的 IPv6 地址
InetAddress addr3 = InetAddress.getByName("2001:DB8:2DE::E13");

// 返回主机名为“www.javathinker.net”的 IP 地址
InetAddress addr4 = InetAddress.getByName("www.javathinker.net");
```

## 1.3 设定客户端的地址

显示地设置客户端的IP地址和端口：

```java
// 参数 localAddr 和 localPort 用来设置客户端的 IP 地址和端口

Socket(InetAddress address,int port, InetAddress localAddr, int localPort) throws IOExpection;

Socket(String host, int port, InetAddress localAddr, int localPort) throws IOException;
```

构造Socket对象：

```java
InetAddress remoteAddr = InetAddress.getByNmae("112.5.4.45");
InetAddress localAddr = InetAddress.getByName("112.5.4.3");

// 客户端使用端口 2345
Socket socket = new Socket(remoteAddr, 8000, localAddr, 2345);
```

## 1.4 客户连接服务器时可能抛出的异常

Socket的构造方法请求连接服务器时，可能出现的异常：

- UnknownHostException：如果无法识别主机名字或IP地址，就会抛出这种异常。
- ConnectException：如果没有没有服务器进监听指定的端口，或者服务器进程拒绝连接，就会抛出这种异常。
- SocketTimeoutException：如果等待连接超时，就会抛出这种异常。
- BindException：如果无法把Socket对象与指定的本地IP地址或端口半丁，就会抛出这种异常。

***

 - IOException
    - UnknownHostException
    - InterruptedIOException
        - SocketTimeoutException
    - SocketException
        - BindException
        - ConnectException

***

