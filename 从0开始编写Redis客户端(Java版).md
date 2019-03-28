# 001 - 从0开始编写Redis客户端

### 说在前面的话

redis火得一塌糊涂，它的应用场景非常广泛。做为一名IT工程师，做为攻城狮的我们，若不深入了解它,那么我们跟咸鱼有什么区别，在这里让我们一起走进
redis客户端发现之旅</br>
Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。从2010年3月15日起，Redis的开发工作由VMware主持。从2013年5月开始，Redis的开发由Pivotal赞助。

### 官方文档

https://redis.io

### 初步了解RESP协议

RESP协议是Redis客户端和服务端之间的通讯协议</br>
史上最详细的协议文档 https://redis.io/topics/protocol

### 协议说明

1、客户端和服务端通过TCP进行数据交互
2、 传输的数据遵循RESP协议，如下：
每一段数据通过 进行分隔，每段数据通过一个特殊标志作为开头
* 代表数组
$ 多行字符串
+ 单行信息
- 错误信息
: 整型数字
### 程序代码如下(java版)
5类：
Connection.java  连接
Constant.java    常量
JiBaoJedisClient.java  客户端
Protocol.java 协议
TestJiBaoJedis.java  测试

##### Connection.java
```
/**
 * Created by zorro on 2019/3/27.
 */
@Data
public class Connection {
    public String host;
    public int port;
    private Socket socket;
    private InputStream inputStream;
    private OutputStream outputStream;
    public Connection connection(String host,int port) {
        try {
            socket = new Socket(host,port);
            inputStream = socket.getInputStream();
            outputStream = socket.getOutputStream();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return  this;
    }

}

```

##### Constant.java
```
/**
 * Created by zorro on 2019/3/27.
 */
public class Constant {
    public static final String  DOLLAR_SYMBOL="$";
    public static final String  STAR_SYMBOL="*";
    public static final String  ENTER__SYMBOL="\r\n";
    public static final String  SET_COMMAND = "SET";
    public static final String  GET_COMMAND = "GET";
}


```

##### JiBaoJedisClient.java
```
/**
 * Created by zorro on 2019/3/27.
 */
public class JiBaoJedisClient {
    private Connection conn ;
    private Protocol protocol;
    public JiBaoJedisClient(String host,int port) {
        conn = new Connection();
        conn.connection(host,port);
    }
    public String get(String key) {
        try {
            protocol = new Protocol();
            StringBuffer sb = protocol.getCommand(key);
            conn.getOutputStream().write(sb.toString().getBytes());
            byte[] bytes = new byte[1024];
            conn.getInputStream().read(bytes);
            return new String(bytes);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
    public String set(String key,String val) {
        try {
            protocol = new Protocol();
            StringBuffer sb = protocol.setCommand(key,val);
            conn.getOutputStream().write(sb.toString().getBytes());
            byte[] bytes = new byte[1024];
            conn.getInputStream().read(bytes);
            return new String(bytes);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}

```

##### Protocol.java
```

/**
 * Created by zorro on 2019/3/27.
 */
public class Protocol {

    public StringBuffer setCommand(String key,String val) {
        StringBuffer sb = new StringBuffer();
        sb.append(Constant.STAR_SYMBOL).append("3").append(Constant.ENTER__SYMBOL);

        sb.append(Constant.DOLLAR_SYMBOL).append("3").append(Constant.ENTER__SYMBOL);
        sb.append(Constant.SET_COMMAND).append(Constant.ENTER__SYMBOL);

        sb.append(Constant.DOLLAR_SYMBOL).append(key.getBytes().length).append(Constant.ENTER__SYMBOL);
        sb.append(key).append(Constant.ENTER__SYMBOL);

        sb.append(Constant.DOLLAR_SYMBOL).append(val.getBytes().length).append(Constant.ENTER__SYMBOL);
        sb.append(val).append(Constant.ENTER__SYMBOL);
        return sb;

    }
    public StringBuffer getCommand(String key) {
        StringBuffer sb = new StringBuffer();
        sb.append(Constant.STAR_SYMBOL).append("2").append(Constant.ENTER__SYMBOL);

        sb.append(Constant.DOLLAR_SYMBOL).append("3").append(Constant.ENTER__SYMBOL);
        sb.append(Constant.GET_COMMAND).append(Constant.ENTER__SYMBOL);

        sb.append(Constant.DOLLAR_SYMBOL).append(key.getBytes().length).append(Constant.ENTER__SYMBOL);
        sb.append(key).append(Constant.ENTER__SYMBOL);
        return sb;

    }

}

```

##### TestJiBaoJedis.java

```

/**
 * Created by zorro on 2019/3/27.
 */
public class TestJiBaoJedis {
    public static void main(String args[]) {
        JiBaoJedisClient client = new JiBaoJedisClient("127.0.0.1",6379);
        System.out.println(client.set("name","佐罗"));
        System.out.println(client.get("name"));
    }
}

```

### 写在后面的话
到这里一个简易的redis客户端实现了，只要你理解其原理你可以用其他语言去实现它，c c++ php go py等，做上层应用开发小伙伴可能畏惧TCP，别怕！<br/>
只要够用心，你可以一步一个脚印，从应用层干到物理层。不瞎逼逼了，就这样吧 

