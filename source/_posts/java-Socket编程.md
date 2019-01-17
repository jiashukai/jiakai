---
title: java Socket编程
date: 2019-01-14 09:39:03
tags:
---
# java SOcket编程
java最初是作为网络编程，其对网络提供了高度的支持，使得客户端和服务器的沟通变得现实，而在网络编程中，使用最多的就是Socket
## Socket编程
### 一.网络基础知识
1.两台计算机间进行通讯需要以下三个条件：
    ip地址，协议，端口号      
2.TCP/IP协议：    
是目前世界上应用最为广泛的协议，是以TCP和IP为基础的不同层次上多个协议的集合，也是TCP/IP协议簇，或TCP/IP协议栈   

      TCP：传输控制协议   IP：互联网协议
3.TCP/IP五层模型
应用层：HTTP，FTP，SMTP，Telnet等     
传输层：TCP/IP     
网络层：     
数据链路层：   
物理层：网线，双绞线，网卡等   
4.IP地址    
为实现网络中不用计算机之间的通信，每台计算机都必须有一个唯一的标识符---IP地址。32位二进制
5.端口    
区分一台主机的多个不同应用程序，端口号范围为0-65535，其中0-1023位为系统保留。    
如：HTTP：80 FTP：21 Telnet：23

     IP地址+端口号组成了所谓的Socket，Socket是网络上运行的程序之间双向通信链路的终结点，是TCP和UDP的基础
6.Socket套接字：     
网络上具有唯一标识的IP地址和端口组合在一起才能构成唯一能识别的标识符套接字。    
Socket原理机制：

      通信的两端都有Socket，网络通信其实就是Socket间的通信，数据在两个Socket间通过IO传输。
7.java中的网络支持

    针对网络通信的不同层次，java提供了不同API，其提供的网络功能有四大类：
    InetAddress：用于标识网络上的硬件资源，主要是IP地址                   
    URL：统一资源定位符，通过URL可以直接读取或写入网络上的数据
    Sockets：使用TCP协议实现的网络通信Socket相关的类
    Datagram：使用UDP协议，将数据保存在用户数据报中，通过网络进行通信

# 二.InetAddress
InetAddress类用于标识网路坡上的硬件资源，标识互联网协议（IP）地址

      //获取本机的InetAddress实例
      InetAddress address=InetAddress.getLocalHost();
      address.getHostName();//获取计算机名
      address.getHostAddress();//获取IP地址
      byte[] bytes=address.getAddress();//获取字节数组形式的IP地址，以点分隔的四部分
      //获取其他主机的InetAddress实例
      InetAddress address2=InetAddress.getByName("其他主机名");
      InetAddress address3=InetAddress.getByName("IP地址");
# 三.URL类
## 1.URL统一资源定位符，表示Internet上某一资源的地址，协议名：资源名称

      //创建一个URL的实例
      URL baidu=new URL("http://www.baidu.com");
      URL url=new URL(baidu,"/index.html?username=tom#test")//? 表示参数，#表示锚点
      url.getProtocol();//获取协议
      url.getHost();//获取主机
      url.getPort();//如果没有指定端口号，根据协议不同使用默认端口号。此时getPort()方法的返回值为-1
      url.getPath();//获取文件路径
      url.getFile();//文件名，包括文件路径+参数
      url.getRef();//相对路径，就是锚点，即#号后面的内容
      url.getQuery();//查询字符串，即参数
## 2.使用URL读取网页内容
通过URL对象的openStream()方法可以得到指定资源的输入流，通过流能够读取或则访问网页上的资源

        URL url=new URL("http：//www.baidu.com");
        InputStream is=url.openStream();//通过openStream方法获取资源的字节输入流
        InputStreamReader isr=new InputStreamReader(is,"UTF-8");//将字节输入流转换为字符输入流，如果不指定编码，中文可能会出现乱码
        BufferReader br=new BufferedReader(isr);//为字符输入流添加缓冲，提高读取效率
        String data=br.readLine();//读取数据
        while(data!=null){
            System.out.println(data);
            data=br.readerLine();
        }
        br.close();
        isr.close();
        is.close();
# 四.TCP编程
1.TCP协议是面向连接的，可靠地，有序的，以字节流的方式发送数据，通过三次握手方式建立连接，形成传输数据的通道，在连接中进行大量的数据的传输，效率会稍低      
2.Java中基于TCP协议实现网络通信的类：（1）客户端的Socket类（2）服务器端的ServerSocket类
！[Image text](https://github.com/jiashukai/jiakai/blob/master/source/_posts/pictures/socket%E9%80%9A%E4%BF%A1.png)      
3.Socket通信的步骤
（1）创建ServerSocket和Socket（2）打开连接到Socket的输入与输出流
（3）按照协议对Socket进行读/写操作（4）关闭输入输出流，关闭Socket       
4.服务器端：
（1）创建ServerSocket对象。绑定监听端口（2）通过accept()方法监听客户端请求
（3）连接建立后，通过输入流读取客户端发送的请求消息（4）通过输出流向客户端发送相应消息（5）关闭相关资源

      //基于TCP协议的Socket通信，实现用户登录，服务端
      //创建一个服务器端Socket，即ServerSocket，指定绑定的端口，并监听此端口
      ServerSocket serverSocket= newServerSocket(10086);
      //调用accept方法开始监听，等待客户端的连接
      Socket socket=serverSocket.accept();
      //获取输入流，并读取客户端信息
      InputStream is=socket.getInputStream();
      InputStreamReader isr=new InputStreamReader(is);
      BufferedReader br=new BufferedReader(isr);
      String info=null;
      while((info=br.readLine())!=null){
          System.out.println("我是服务器，客户端说："+info)；
      }
      socket.shutdownInput();//关闭输入输出流
      //4.获取输出流，相应客户端的请求
      OutputStream os= socket.getOutputStream();
      PrintWriter pw=new PrintWriter(os);
      pw.write("欢迎你！");
      pw.flush();
      //5.关闭资源
      pw.close();
      os.close();
      br.close();
      isr.close();
      is.close();
      socket.close();
      serverSocket.close();
5.客户端              
（1）创建Socket对象，指明需要连接的服务器地址和端口号（2）连接建立后，通过输出流向服务器发送请求信息（3）通过输入流获取服务器相应的信息（4）关闭相应资源

      //客户端
      //创建客户端Socket，指定服务器地址和端口
      Socket socket =new Socket("localHost",10086);
      //获取输出流，向服务器发送消息
      OutputStream  os=new OutputStream();//字节流输出
      PrintWriter pw=new PrintWriter(os);//将输出流包装成打印流。
      pw.writer("用户名：admin，密码：123");
      pw.flush();
      socket.shutdownOutput();
      //获取输入流，并读取服务器短的响应信息
      InputStream in=socket.getInputStream();
      BufferedReader br=new BufferedReader(new InputStreamReader(is));
      String info=null;
      while((info=br.readLine())!=null){
        System.out.println("我是客户端，服务器说："+info);
      }
      br.close();
      is.close();
      pw.close();
      os.close();
      socket.close();
6.应用多线程实现服务器与客户端之间的通信
（1）服务器端创建ServerSocket，循环调用accept()等待客户端连接（2）客户端创建一个socket并请求和服务器端连接（3）服务器接受客户端请求，创建socket与该客户端建立专线连接（4）建立连接的两个socket在一个单独的线程上对话（5）服务器端继续等待新的连接

      //服务器线程处理
      //和本线程相关的socket
      Socket socket=null;
      public serverThread(Socket socket){
        this.socket=socket;
      }
      public void run(){
        //服务器处理代码
      }
      //服务器代码
      ServerSocket serverSocket=new ServerSocket(10086);
      Socket socket=null;
      int count=0;//记录客户端的数量
      while(true){
        socket=serverSocket.accept();
        ServerSocket serverThread=new ServerThread(socket);
        serverThread..start();
        count++;
        System,out,println("客户端连接数量："+count);
      }

五.UDP编程
UDP协议（用户数据报协议）是无连接的，不可靠的，无序的，速度快
进行数据传输时，首先将要传输的数据定义成数据报，大小限制在64k，在数据报中指明数据所要达到的Socket（主机地址和端口号），然后再将数据报发送出去。                          
DatagramPacket类：表示数据包                          
DatagramSocket类：进行端到端通信的类            
1.服务器端实现步骤：
（1）创建DatagreamSocket,指定端口号（2）创建DatagramPacket（3）接受客户端发送的数据信息
（4）读取数据

    //服务器端，实现基于UDP的用户登录
    //创建服务器端的DatagramSocket,指定端口号
    DatagrameSocket socket=new DatagrameSocket(10010);
    //创建数据报，用于接受客户端发送的数据报
    byte[] data=new byte[1024];
    DatagramePacket packet=new DatagramePacket(dtat,data.length);
    //接受客户端发送的数据
    socket.receive(packet);//此方法在接受数据报之前会一直阻塞
    String info=new String(data,0,data.length);
    System.out.println("我是服务器，客户端告诉我"+info);

    //向客户端响应数据
    //1.定义客户端的地址，端口号，数据
    InetAddress address=packet.getAddress();
    int port=packet.getPort();
    byte[] data2="欢迎您".getBytes();
    //创建数据报，包含响应的数据信息
    DatagramPacket packet2=new DatagramPacket(data2,data2.length,address,port);
    //响应客户端
    socket.send(packet2);
    //关闭资源
    socket.close();
2.客户端实现步骤       
    (1)定义发送信息（2）创建DatagramPacket,包含将要发送的信息
    （3）创建DatagramSocket（4）发送数据

    //客户端
    //可以服务器的地址，端口号，数据
    InetAddress address=InetAddress.getByName("localhost");
    int port=10010;
    byte[] data=="用户名：admin；密码：123".getBytes();
    //创建数据报，包含发送的数据信息
    DatagramPacket packet=new DatagramPacket(data,data.length,address,port);
    //创建DatagramSocket 对象
    DatagramSocket socket=new DatagramSocket();
    //4向服务器发送数据
    socket.send(packet);

    //接受服务器端响应的数据报
    //创建数据报，用于接受服务器端响应的数据
    byte[] data2=new byte[1024];
    DatagramPacket  packet2=new DatagramePacket(data2,data2.length);
    //接受服务器相应的数据
    socket.receive(packet2);
    String raply=new String(data2,0,packet.getLength());
    Systen.out.println("我是客户端，服务器说："+raply);
    socket.close();
