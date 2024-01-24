- [x] OSI 7 Layer & TCP/IP stack
- [ ] 세션과 쿠키 이야기
- [ ] 캐시 이야기
- [ ] 프록시 서버 이야기
- [ ] TCP & UDP 이야기

지난 시간에 OSI 7 Layer나 TCP/IP Stack에 대하여 다뤘다. 여러 레이어를 거치며 통신이 이루어진다고만 이해해도 일단은 괜찮다 더 중요한 건, 통신의 목적이 되는 "중요 데이터"를 어떻게 주고받는가 이다. 이렇게 통신의 목적이 되는 중요 데이터를 어떤 규약으로 통신하는가를 담당하는 레이어는 Transport Layer이다. 전송 레이어라고도 하며, 여기에는 `TCP`와 `UDP`라는 두 개의 프로토콜이 메인을 이루고 있다. 이에 대하여 잘 이해하면, 네트워크에서 가장 중요한 것 중 하나를 이해했다고 해도 과언이 아니다. 따라서 차근차근 transport 레이어의 전송 프로토콜인 TCP, UDP에 대하여 자바 코드로 이해해볼 예정이다. 이번 시간에는 TCP 위주로 확인해보자.

## TCP

TCP는 전송 계층 프로토콜의 가장 중요한 데이터 통신 프로토콜이다. 이는 연결형 프로토콜로, 상대 프로세스와 연결을 수립한 뒤 데이터를 주고받는 방식으로 이루어진다. 통신을 하기 전에 연결 요청과 연결 수락 과정을 거치면 통신 회선이 고정되어 데이터가 전달된다. 이를 통해 전달되는 데이터는 순서대로 전달되며 손실이 발생하지 않도록 컨트롤된다.

- 연결형 프로토콜
- 연결 수립 후 통신 회선이 고정되어 데이터 전달
- 데이터 순서를 보장, 패킷 손실 방지 컨트롤 O

TCP는 대표적으로 HTTP/1.1과 HTTP/2 통신에서 사용된다.(이 사실 만으로 TCP가 웹 통신에서 중요한 이유는 충분히 설명되었다 생각한다.) HTTP를 잘 모른다면, 웹 브라우저가 웹 서버와 통신할 때 보통 TCP를 사용한다고 이해하면 된다. 그 외에도 이메일 전송(SMTP), 파일 전송(FTP)에도 TCP가 사용된다. 이와 유사한 개념으로, 흔히들 우리는 TCP와 함께 네트워크 레이어의 IP 프로토콜을 같이 묶어서 TCP/IP라고 하는데, 이는 두 프로토콜이 항상 세트로 사용되기 때문이다. 각 레이어는 유기적으로 연결되어 하나의 통신 과정일 뿐이므로 공부할때에는 논리적으로 구분하더라도, 모두 분리된 개념이 아니라 하나로 통하는 것임을 명심해야 한다.

### 자바에서는 이를 어떻게 코드로 구현하나요?

자바에서는 TCP 통신을 수립하기 위해 `java.net` 패키지의 `ServerSocket`과 `Socket`클래스를 제공한다. 말 그대로, `ServerSocket` 클래스가 TCP 연결을 수락하는 서버 쪽 클래스고, `Socket`은 클라이언트 쪽에서 TCP 통신 연결을 요청하기 위해서도 쓰이고, 서버와 클라이언트 둘 다 데이터를 주고 받을 때 사용하는 클래스이다.

```java
// 방법 1
ServerSocket serverSocket = new ServerSocket(15000); // port 번호 : 15000

// 방법 2
ServerSocket serverSocket = new ServerSocket();
serverSocket.bind(new InetSocketAddress(15000));
```

기본적으로 자바 코드로 TCP 서버를 구축하기 위해서는 `ServerSocket` 객체를 생성해야 연결 요청을 수락할 수 있다. 이 객체를 사용하기 위해서는 프로세스 port 번호를 바인딩해줘야 하는데, 편한 방법은 객체를 생성할 때 해당 데이터를 통신할 서버 프로세스 port 번호를 생성자 argument로 지정해주는 것이다. 이렇게 서버 프로세스와 `ServerSocket` 객체가 바인딩 되었으며, client의 TCP 연결 요청 수락을 위한 준비가 끝난 것이다. (만약 서버 컴퓨터에 IP가 여러개로 할당되어 있다면, `InetSocketAddress`의 parameter에 특정 IP 주소를 지정해주면 된다.)

```java
// server
Socket server = serverSocket.accept(); // 클라이언트의 연결 요청을 기다림

// client
Socket client = new Socket("localhost", 15000); // 해당 IP 주소와 port로 TCP 연결
```

`.accept()` 메서드를 수행하면 client의 연결 요청이 들어올 때 까지 프로세스가 블락되며, 요청이 들어오면 해당 요청을 받으며 Socket 객체를 반환한다. 이 과정에서 두 노드 간 TCP 연결이 수립된 것이며, Socket 객체는 상대 프로세스의 Port 번호는 물론, 상대 호스트의 IP 주소까지 갖고 있다. 이는 다음과 같이 확인이 가능하다.

```java
InetSocketAddress isa = (InetSocketAddress) socket.getRemoteSocketAddress();  
String clientIp = isa.getHostName(); // 클라이언트 IP 주소  
int clientPort = isa.getPort(); // 클라이언트 Port 번호
```

위 로직 중 `socket.getRemoteSocketAddress();`를 통해 `RemoteSocketAddress`라는 추상 클래스로 형변환된 변수를 반환받을 수 있는데, 이를 상속하여 구현한 클래스가 `InetSocketAddress`이다. 따라서 구현체 타입으로 다운캐스팅을 통해 해당 구현 클래스가 갖는 메서드를 사용할 수 있도록 한 코드이다.

```java
serverSocket.close(); // 서버 소켓 종료
```

`serverSocket` 객체의 `close()` 메서드를 통해 TCP 서버를 종료할 수 있으며, 이를 통해 socket에 바인딩 해 뒀던 port 번호를 언바인딩할 수 있다. 이렇게 한 이후에야 다른 프로그램에서 다시 해당 Port 번호를 사용할 수 있게 된다. (그 전에는 바인딩되어있는 프로세스가 있는 거라서 불가능)

### TCP 통신으로 Echo 구현하기

네트워크를 배우는 과정에서 한번쯤은 해보는, TCP 기반으로 echo를 제공하는 서버와 클라이언트 코드를 작성해 보자.

아래 코드는 로컬호스트의 15000번 포트를 이용하여 TCP 통신으로 echo 서비스를 제공하는 서버와 이를 이용하는 클라이언트를 구현한 내용이다. 

**TCP 기반 Socket Server**

TCP 방식은 당연히 서버가 먼저 실행되어 기다리고 있는 상황에서 client가 실행되어 데이터를 전송하고 이를 echo로 받는 시나리오에서만 가능하다. 따라서 SocketServer를 기준으로 먼저 서술하겠다.

```java
public class TCPServer {  
    private static ServerSocket serverSocket = null;  
  
    public static void main(String[] args) throws IOException {  
		// tcp socker server start
        startServer();  

		// set quit condition
        System.out.println("서버를 종료하려면 q를 입력하시오.");          
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        while (true) {  
            String input = br.readLine();  
            if (input.equals("q")) {  
                break;  
            }  
        }  
        br.close();  

		// terminate socket server
        stopServer();  
    }  
  
    public static void startServer() {  
        // define worker thread  
        Thread thread = new Thread(() -> {  
            try {  
                // serverSocket generated & port bound  
                System.out.println("server : start");  
                serverSocket = new ServerSocket(15000);  
                System.out.println("server : bind port");  
  
                while (true) {  
                    System.out.println("================================");  
                    System.out.println("server : wait for client request"); 
                     
                    // wait for establishing connection  
                    Socket socket = serverSocket.accept();  
  
                    // get informations of tcp connection host  
                    InetSocketAddress isa = (InetSocketAddress) socket.getRemoteSocketAddress();  
                    String clientIp = isa.getHostName();  
                    System.out.println("server : clientIP " + clientIp + " connected");  
                    // get data from client  
                    byte[] readBytes = new byte[1024];  
                    InputStream inputStream = socket.getInputStream();  
                    int input = inputStream.read(readBytes);  
                    String data = new String(readBytes, 0, input, "UTF-8");  
                    System.out.println("server : read data -> " + data);  
  
                    // send echo data to client  
                    DataOutputStream dataOutputStream = new DataOutputStream(socket.getOutputStream());  
                    dataOutputStream.writeUTF(data);  
                    dataOutputStream.flush();  
                    System.out.println("server : send echo -> " + data);  
  
                    // data transfer using tcp ended  
                    socket.close();  
                    System.out.println("server : clientIP " + clientIp + " connetion closed");  
                }  
  
            } catch (IOException e) {  
                throw new RuntimeException(e);  
            }  
        });  
        // worker thread start  
        thread.start();  
    }  
  
    public static void stopServer() {  
        try {  
            serverSocket.close();  
            System.out.printf("server : close");  
        } catch (IOException e) {  
            throw new RuntimeException(e);  
        }  
    }  
}
```

코드가 어렵진 않으니 간단하게만 설명하겠다. 기본적으로 클라이언트에서 데이터를 보내면 이를 매개값으로 하여 OutputStream의 `write()`메서드를 호출하면 된다. 만약 간편하게 처리하려면 `new DataInputStream()`을 이용하면 된다.


**TCP 기반 클라이언트**

```java
public class TCPClient {  
    public static void main(String[] args) {  
        try {  
            Socket socket = new Socket("localhost", 15000);  
  
            System.out.println("client : server connected!");  
  
            // send data  
            String data = "자바로 네트워크 이해하기!!!";  
            byte[] bytes = data.getBytes("UTF-8");  
            OutputStream outputStream = socket.getOutputStream();  
            outputStream.write(bytes);  
            outputStream.flush();  
            System.out.println("client : send data -> " + data);  
  
            // receive echo  
            DataInputStream dataInputStream = new DataInputStream(socket.getInputStream());  
            String echo = dataInputStream.readUTF();  
            System.out.println("client : receive echo -> " + echo);  
  
            socket.close();  
            System.out.println("client : close");  
  
        } catch (UnknownHostException e) {  
            // IP 주소 잘못된 경우  
            throw new RuntimeException(e);  
        } catch (IOException e) {  
            // 해당 포트에 연결이 어려운 경우  
            throw new RuntimeException(e);  
        }  
    }  
}
```

클라이언트도 서버와 유사하게 구성하고 있으므로, 자세한 설명은 생략한다.

위 코드를 통해 알 수 있는 건 다음과 같다.
- TCP는 연결을 수립한 뒤 데이터를 통신하는 프로토콜
- 