# TCP的连接和释放

> TCP传输控制协议，是一个面向连接的协议。在运用此协议进行数据传输前都会进行连接的建立工作（三次握手）；当数据传输完毕，连接的双方都会通知对方要释放此连接（四次挥手）。

## TCP标志位

  | 标志 | 说明                 |
  | ---- | --------------------|
  | SYN  | synchronous建立联机  |
  | ACK  | acknowledgement 确认 |
  | PSH  | push传送             |
  | FIN  | finish结束           |
  | RST  | reset重置            |
  | URG  | urgent紧急           |

## 过程

  ![](./img/tcp.gif)

### 三次握手
  - 第一次握手：
    
    客户端发送一个 **TCP 标志位** `SYN=1，ACK=0` 的数据包给服务端，并随机会产生一个**序列号**` Sequence number = 3233`.当服务端接收到这个数据后，服务端由 `SYN=1` 可知客户端是想要建立连接；

  - 第二次握手

    服务端要对客户端的联机请求进行确认，向客户端发送应答号 `ACK=1、SYN=1`
    确认号 `Acknowledge number = 3234` ，此值是客户端的序列号加 1 ，还会产生一个随机的序列号 `Sequence number= 36457 `，这样就告诉客户端可以进行连接；

  - 第三次握手
  
    客户端收到数据后检查 `Acknowledge number` 是否是 `3233 + 1` 的值，以及 ACK 的值是否为 1，若为 1，host1 会发送 `ACK=1`、确认号码 `Acknowledge number=36457`，告诉服务端,你的请求连接被确认，连接可以建立。

### 四次挥手

  - 第一次挥手
  
    当传输的数据到达尾部时，客户端向服务端发送 `FIN=1` 标志位；可理解成，客户端向服务端说，我这边的数据传送完成了，我准备断开了连接；

  - 第二次挥手

    因 TCP 的连接是 **全双工的双向连接**，关闭也是要从两边关闭；当服务端收到客户端发来的 `FIN=1` 的标志位后，服务端**不会立刻**向客户端发送 `FIN=1` 的请求关闭信息，而是先向客户端发送一个 `ACK=1` 的应答信息，表示：你请求关闭的请求我已经收到，但我可能还有数据没有完成传送，你再等下，等我数据传输完成了我就告诉你；

  - 第三次挥手
  
    服务端数据传输完成，向客户端发送 `FIN=1` ，客户端收到请求关闭连接的请求后，客户端就明白服务端的数据已传输完成，现在可以断开连接了，

  - 第四次挥手：

    客户端收到 `FIN=1` 后，客户端还是怕由于网络不稳定的原因，怕服务端不知道他要断开连接，于是向服务端发送 `ACK=1` 确认信息进行确认，
    把自己设置成 `TIME_WAIT` 状态并**启动定时器**，如果服务端没有收到 `ACK` ，服务端 TCP 的定时器到达后，会要求客户端重新发送 ACK ，当服务端收到ACK后，服务端就断开连接；
    当客户端等待2MLS（2倍报文最大生存时间）后，没有收到服务端的重传请求后，他就知道服务端已收到了ACK，所以客户端此时才关闭自己的连接。