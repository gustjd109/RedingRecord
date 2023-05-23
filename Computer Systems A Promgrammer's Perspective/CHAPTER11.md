# CHAPTER11 : 네트워크 프로그래밍(Network Programming)

## 11.1 클라이언트-서버 프로그래밍 모델
1. 네트워크 응용 프로그램
    - 클라이언트-서버 모델에 기초
    - 한 개의 서버와 한 개 이상의 클라이언트로 구성<br><br>
2. 서버
    - 리소스를 관리
    - 리소스를 조작해서 클라이언트를 위한 일부 서비스를 제공
        - 웹 서버 : 디스크 파일들을 관리하고, 클라이언트를 대신해서 이들을 가져오고 실행
        - FTP 서버 : 클라이언트를 위해 저장하고 읽어오는 디스크 파일들을 관리
        - 이메일 서버 : 클라이언트를 위해 읽고 갱신하는 스풀 파일을 관리<br><br>
3. 트랜잭션  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FRIdXo%2FbtqUo8QX1Nc%2Fq9037xdAkTh142pwvFlia1%2Fimg.png"></img>
    - 트랜잭션 : 클라이언트-서버 모델에서의 근본적인 연산<br><br>
    - 트랜잭션의 4단계 구성
        - 1단계 : 클라이언트가 서비스를 필요로 할 때, 한 개의 요청을 서버로 전송
        - 2단계 : 서버는 요청을 받고, 해석하고, 자신의 자원들을 적절한 방법으로 조작
        - 3단계 : 서버는 응답을 클라이언트로 보내고, 다음 요청이 있을 때까지 대기
        - 4단계 : 클라이언트는 응답을 받고 처리

## 11.2 네트워크
1. 호스트(아래 내용에서 호스트가 자주 나와서 함 찾아봤심😒)
    - IP를 가지고 있는 양방향 통신이 가능한 네트워크에 연결된 컴퓨터나 기타 장치
        - 인터넷은 TCP/IP 프로토콜을 이용하여 통신하는데, 통신을 하려고 해도 목적지와 출발지가 없으면 어디로 데이터를 보낼지 받을지 모름
        - IP라는 고유한 주소를 통해 목적지와 출발지를 구할 수 있으며 호스트는 IP 주소를 가지고 있음<br><br>
2. 네트워크 호스트의 하드웨어 구성  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbHOW89%2FbtrfsdR0uGB%2FHRzacch5rDKNKZNpjUkFK1%2Fimg.png"></img>
    - 클라이언트와 서버는 종종 별도의 호스트에서 돌아가며, 컴퓨터 네트워크의 하드웨어 및 소프트웨어 자원을 사용해서 통신 수행<br><br>
    - 호스트 입장에서 네트워크 뭘까?
        - 단지 또 다른 입출력 장치
        - 컴퓨터에 데이터가 들어오거나 나가는 지점(소스와 싱크)이 되는 입출력 장치와 같은 것
        - 호스트 컴퓨터 확장 슬록에는 네트워크 어댑터(=랜카드)가 꽂혀있고, 이는 컴퓨터와 네트워크 사이에 물리적인 인터페이스를 제공
        - 네트워크에서 수신한 데이터는 I/O와 메모리 버스를 거쳐 메모리로 대개 DMA 전송으로 복사 <-> 메모리에서 네트워크로도 복사 가능<br><br>
3. 네트워크 범위 : LAN, WAN, internet
    - 네트워크는 물리적으로 지리적 근접성(nearness)에 따라 계층구조를 갖는 시스템<br><br>
    - 네트워크 분류
        - LAN(Local Area Network)
            - 비교적 가까운 거리에 위치한 장치들을 서로 연결한 네트워크
            - 집, 사무실, 학교 등의 건물과 같이 가까운 지역을 연결하는 네트워크
            - 각자의 컴퓨터를 연결하는 fully connected와 한 곳에 모아 사용하는 star 연결 방식을 사용
            - 근거리 통신이라 덜 복잡하므로 속도가 빠르고 오류가 적음
        - WAN(Wide Area Network)
            - 랜과 랜의 연결된 형태로, ISP가 제공하는 서비스를 사용하여 구축된 네트워크
            - 특정 도시, 국가, 대륙과 같이 매우 넓은 범위를 연결하는 네트워크
            - 여러 노드들이 서로 그물처럼 연결되는 mesh 연결 방식을 사용
            - 원거리 통신이라 LAN에 비해 복잡하므로 속도가 느리고, 여러 물리적 상황과 환경에 영향을 많이 받으므로 오류가 많음
        - 인터넷
            - TCP/IP 프로토콜을 사용하는 세계 최대 규모의 네트워크
            - 네트워크의 한 종류로, 전 세계의 큰 네트워크부터 작은 네트워크까지를 연결하는 거대한 네트워크
            - 인터넷은 여러 형태를 혼합한 형태의 연결 방식을 사용(start + mesh 등의 조합)<br><br>
    - ISP(Internet Service Provider)
        - 인터넷에 접속하는 수단을 제공하는 주체
        - 일반 사용자, 기업체, 기간, 단체 등이 인터넷에 접속하여 인터넷을 이용할 수 있도록 돕는 사업자
        - KT, LG U+, SKT와 같은 ISP가 인터넷 서비스를 제공<br><br>
    - WAN이 망가지면 어떻게 될까?(궁금해서 찾아봄🧐)
        - 2018년 11월 24일 KT 아현 지사 화재 때 인터넷이 먹통되는 문제 발생
        - ISP인 KT에서 대신 연결해 주었던 케이블들과 장비들이 망가지는 바람에 발생한 문제<br><br>
4. 이더넷(Ethernet)
    - LAN에서는 이더넷이라는 기술을 사용하여 통신<br><br>
    - 이더넷 세그먼트  
        <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FZApKr%2FbtrQpJIB6Xh%2FKjBXlbyDWs22O5e63UkKR1%2Fimg.png"></img>
        - 꼬임쌍선(Twisted-pair cable) 즉, 랜 케이블과 허브로 구성
        - 방이나 빌딩의 층과 같이 작은 지역에 설치
        - 각 전선은 동일한 최대 비트 대역폭을 가짐(100Mb/s 또는 1Gb/s)
        - 한쪽 끝은 호스트의 어댑터에 연결되고, 다른 끝은 허브의 포트에 연결<br><br>
    - 이더넷 세그먼트 구성요소
        - 꼬임쌍선(Twisted-pair cable)  
            <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FkU7Xa%2FbtrQpSSMkEU%2F8knfsO4Mvbk4OmmuVq5lb0%2Fimg.png"></img>
            - 구리 선 여덟개를 두개씩 꼬아 만든 네 쌍의 전선
            - 구리선을 꼬는 이유는 노이즈를 방지하기 위함<br><br>
            - 꼬임쌍선 종류
                - 차폐 트위스티드 페어(STP, Shielded Twisted Pair)
                    - 두개씩 꼬아 만든 선을 실드(Shield)로 보호한 케이블
                    - 높은 대역폭과 실외 환경이 요구되는 하이엔드 제품에서 흔희 사용
                - 비차폐 트위스티드 페어(UTP, Unshielded Twisted Pair)
                    - 두개씩 꼬아 만든 선으로 된 케이블
                    - 가정, 사무실 및 대기업에서 흔히 사용<br><br>
        - 허브(Hub)  
            <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcLFQLm%2FbtrQqLFqknN%2F7MD1LchxqzPMZyfyZowSt0%2Fimg.png"></img>
            - 랜을 구성할 때 가까운 거리에 있는 장비들을 케이블을 사용하여 연결하는 장치
            - 포트를 여러 개 가지고 있어 컴퓨터 여러 대와 통신 가능
            - 어떤 포트로부터 데이터를 받았을 때, 항상 나머지 모든 포트로 데이터를 전송<br><br>
    - 이더넷 프레임  
        <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FkO83N%2FbtrQrt5iGKj%2Fh8n9J481HQp9TXNVPWX0iK%2Fimg.png"></img>
        - 허브는 한 포트에서 들어온 데이터를 나머지 모든 포트로 전달하는데 어떻게 다른 목적지 호스트로 전송할 수 있을까?🤔
            - 이더넷 프레임 이용
            - 이더넷 프레임은 프레임의 출처와 목적지를 명확하게 구분하고, 오류 검출 기능을 통해 신뢰성 있는 데이터 전송을 지원함므로 데이터를 안전하게 전달할 수 있음<br><br>
        - 이더넷 프레임이란?
            - 이더넷 기반의 네트워크에서 데이터를 전송할 때 사용되는 기본 단위
            - 컴퓨터와 네트워크 장치 간에 정보를 교환하기 위해 사용
            - 표준화된 구조를 가지고 있어 서로 다른 장치들 간의 데이터 전송을 원활하게 함<br><br>
        - 이더넷 프레임 구성 요소
            - 프리앰블(Preamble) : 프레임 시작을 알리는 동기화 비트 패턴으로, 수신 측에서 프레임의 시작을 인식 가능하게 함
            - 목적지 MAC 주소(Destination MAC Address) : 수신할 장치의 물리적 주소인 MAC 주소를 나타내며, 목적지 주소를 통해 프레임이 올바른 수신자에게 전달되도록 함
            - 소스 MAC 주소(Source MAC Address) : 데이터를 전송한 장치의 물리적 주소인 MAC 주소를 나타내며, 수신 측에서는 이를 통해 데이터의 출처를 확인할 수 있음
            - 이더타입(EtherType) : 프레임 내의 페이로드 데이터 형식을 식별하는 필드로, 상위 계층 프로토콜(예: IPv4, IPv6)의 종류를 알려줌
            - 페이로드(Payload) : 전송할 실제 데이터를 담고 있는 부분으로, 일반적으로 최대 1500바이트의 크기를 가짐
            - 프레임 체크 시퀀스(Frame Check Sequence) : 프레임의 무결성을 확인하기 위한 오류 검출 코드로, 수신 측에서 프레임의 오류를 검출하는데 사용<br><br>
        - 이더넷 어댑터는 어댑터의 비휘발성 메모리에 저장된 전체적으로 고유한 48비트 주소를 보유 = MAC 주소
        - MAC 주소의 앞쪽 24비트는 랜 카드 제조사 번호, 뒤쪽 24비트는 제조사가 붙인 일련번호<br><br>
        - 이더넷 세그먼트에 속한 호스트가 다른 호스트로 데이터를 전송할 경우, 데이터 앞에 헤더와 뒤에 트레일러를 같이 붙여 전송
            - 헤더 : 데이터(페이로드) 앞에 데이터의 출발지(소스 MAC 주소), 목적지(목적지 MAC 주소), 데이터 타입(이더타입) 정보가 있음
            - 트레일러 : 데이터(페이로드) 끝에 데이터 무결성을 보장하기 위한 프레임 체크 시퀀스가 있음<br><br>
    - 브릿지형 이더넷  
        <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbCQZdh%2FbtrQrGwAU7p%2F43kn69LzKE68RpTOeKWQk1%2Fimg.png"></img>
        - 브릿지형 이더넷이란?
            - 브릿지라는 장비를 사용해서 다수의 이더넷 세그먼트가 연결하여 더 큰 LAN을 구성한 것
            - 케이블이 허브와 브릿지를 연결하고, 브릿지와 브릿지를 연결함으로서 건물 전체나 캠퍼스 전체를 아우르는 더 큰 네트워크가 만들어질 수 있음<br><br>
5. 인터넷 프로토콜
    - LAN들은 라우터라고 부르는 특별한 장비를 통해 연결되어 internet을 형성
    - 라우터는 포트가 있어서 다른 네트워크들과 연결될 수 있는데, 라우터가 연결하는 네트워크들을 WAN이라고 하고, 라우터를 통해 수많은 LAN과 WAN이 연결되어 인터넷을 형성하게 되는 것<br><br>
    - 이렇듯 인터넷은 서로 다른 기술로 구성된 임의의 LAN과 WAN들로 구성되는데, 어떻게 하나의 호스트가 대이터를 목적지 호스트로 전송할 수 있을까?
        - 각 호스트와 라우터에서 실행되는 프로토콜 소프트웨어 단계가 서롤 다른 네트워크의 치아를 완화해주는 역할을 함으로써 해결
        - 프로토콜 소프트웨어는 호스트와 라우터가 서로 데이터를 전송할 수 있게 해주는 규칙(IP 프로토콜)을 실행하고, 아래 두 가지 기능을 제공해야 함<br><br>
    - 프로토콜 소프트웨어 기본 두 가지 기능
        - 명명법(Naming Scheme)
            - 각각의 호스트가 동일한 형식의 고유한 주소를 정해 줌
        - 전달기법(Delivery Mechanism)
            - 전달되는 모든 데이터는 패킷 단위로 전달
            - 패킷은 패킷 크기, 소스, 목적지 호스트 주소를 포함한 헤더와 데이터로 구성<br><br>
    - internet에서 데이터가 하나의 호스트에서 다른 호스트로 이동하는 방법(8단계)  
        <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FwMnOd%2FbtrfDgWhV21%2FMQUNWUaPHitdHDUWZCwHA0%2Fimg.png"></img>
        - 1단계
            - 클라이언트는 시스템 콜을 호출하여 클라이언트의 가장 주소 공간의 데이터를 커널 버퍼로 복사
        - 2단계
            - 호스트 A의 프로토콜 소프트웨어는 internet 헤더와 LAN1 프레임 헤더를 데이터에 추가하여 LAN1 이더넷 프레임을 생성
            - 이 이더넷 프레임을 LAN1 네트워크 어댑터로 전송
        - 3단계
            - LAN1의 네트워크 어댑터는 프레임을 네트워크로 복사
        - 4단계
            - 프레임이 라우터로 도달하면, 라우터의 LAN1 어댑터는 전선에서 이것을 읽어서 프로토콜 소프트웨어로 전달
        - 5단계
            - 라우터는 이전의 LAN1 프레임 헤더를 벗겨내고, 호스트 B의 주소를 갖는 새로운 LAN2 프레임 헤더를 앞에 붙여서 이것을 어댑터로 전달
        - 6단계
            - LAN2 어댑터는 이 프레임을 네트워크로 복사
        - 7단계
            - 이 프레임이 호스트 B에 도착하면 어댑터는 이 프레임을 저넌에서 읽어들이고, 이것을 프로토콜 소프트웨어로 전달
        - 8단계
            - 호스트 B의 프로토콜 스프트웨어는 패킷 헤더와 프레임 헤더를 벗기고, 서버가 이 데이터를 읽는 시스템 콜을 호출할 때 서버의 가상 주소공간으로 복사

## 11.3 글로벌 IP 인터넷


## 11.4 소켓 인터페이스
### 11.4.9 예제 Echo 클라이언트와 서버
1. Echo 클라이언트의 메인 루틴
    - echo 클라이언트 메인 루틴 코드
        ```C
        #include "csapp.h"

        int main(int argc, char **argv)
        {
            int clientfd;
            char*host, *port, buf[MAXLINE];
            rio_t rio;

            if(argc != 3) {
                fprintf(stderr, "usage: %s <host> <port>\n", argv[0]);
                exit(0);
            }
            host = argv[1];
            port = argv[2];

            clientfd = Open_clientfd(host, port);
            Rio_readinitb(&rio, clientfd);

            while(Fgets(buf, MAXLINE, stdin) != NULL) {
                Rio_writen(clientfd, buf, strlen(buf));
                Rio_readlineb(&rio, buf, MAXLINE);
                Fputs(buf, stdout);
            }
            Close(clientfd);
            exit(0);
        }
        ```
    - echo 클라이언트 메인 루프 동작 과정
        - 서버와의 연결을 수립
        - 클라이언트 표준 입력에서 텍스트 줄을 반복해서 읽는 루프에 진입
        - 서버에 텍스트 줄을 전송
        - 서버에서 echo 줄을 읽어서 그 결과를 표준 출력으로 인쇄<br><br>
    - 루프 종료 과정
        - 루프는 fgets가 EOF 표준 입력(사용자가 Ctrl + D를 눌렀거나 파일로 텍스트 줄을 모두 소진했을 경우)을 만나면 종료
        - 루프가 종료한 후, 클라이언트는 식별자를 닫음
        - 이때 서버로 EOF라는 통지가 전송
        - 서버는 rio_realineb함수에서 리턴 코드 0을 받으면 이 사실을 감지하고, 자신의 식별자를 닫은 후에 클라이언트는 종료
        - 클라이언트의 커널이 프로세스가 종료할 때 자동으로 열었던 모든 식별자를 닫아주기 때문에 24번 줄의 Close는 불필요
            - 하지만, 열었던 모든 식별자를 명시적으로 닫아주는 것이 올바른 프로그래밍 습관이므로 명시해 주는 것이 좋음<br><br>
2. 반복적 echo 서버 메인 루틴
    - echo 서버 메인 루틴 코드
        ```C
        #include "csapp.h"

        int main(int argc, char **argv)
        {
            int listenfd, connfd;
            socklen_t clientlen;
            struct sockaddr_storage clientaddr; /* Enough space for any address */
            char client_hostname[MAXLINE], client_port[MAXLINE];

            if(argc != 2) {
                fprintf(stderr, "usage: %s <port>\n", argv[0]);
                exit(0);
            }

            listenfd = Open_listenfd(argv[1]);
            while(1) {
                clientlen = sizeof(struct sockaddr_storage);
                connfd = Accept(listenfd, (SA *)&clientaddr, &clientlen);
                Getnameinfo((SA *) &clientaddr, clientlen, client_hostname, MAXLINE, client_port, MAXLINE, 0);
                printf("Connected to (%s, %s)\n", client_hostname, client_port);
                echo(connfd);
                Close(connfd);
            }
            exit(0);
        }
        ```
    - echo 서버 메인 루프 동작 과정
        - 듣기 식별자(Open_listenfd) 오픈 후 무한 루프에 진입
        - 각각의 반복 실행은 클라이언트로부터 연결 요청 대기, 도메인 이름과 연결된 클라이언트의 포트 출력, 클라이언트를 서비스하는 echo 함수를 호출
        - echo 루틴이 리턴한 후 메인 루틴은 연결 식별자를 닫음
        - 클라이언트와 서버가 자신들의 식별자를 닫은 후 연결 종료<br><br>
    - 소켓 주소 구조체인 clientaddr 변수
        - accept가 리턴하기 전 clientaddr에는 연결의 다른 쪽 끝의 클라이언트의 소켓 주소로 채워짐
        - clientaddr이 struct sockaddr_in이 아닌 struct sockaddr_storage형으로 선언된 이유
            - 정의에 의해 sockaddr_storage 구조체는 모든 형태의 소켓 주소를 저장하기에 충분히 크며, 이것은 코드를 프로토콜-독립적으로 유지해줌<br><br>
    - 반복서버(iterative server)
        - 위와 같이 한 번에 한 개씩의 클라이언트를 반복해서 실행하는 종류의 echo 서버
        - 12장에서 다수의 클라이언트를 동시에 처리하는 복잡한 동시성 서버를 만드는 방법을 배울 예정<br><br>
3. 텍스트 줄을 읽고 echo해주는 echo 함수
    - echo 루틴 코드
        ```C
        #include "csapp.h"

        void echo(int connfd)
        {
            size_t n;
            char buf[MAXLINE];
            rio_t rio;

            Rio_readinitb(&rio, connfd);
            while((n = Rio_readlineb(&rio, buf, MAXLINE)) != 0) {
                printf("server received %d bytes\n", (int)n);
                Rio_writen(connfd, buf, n);
            }
        }
        ```
    - echo 루틴 동작 과정
        - 10번 줄에서 rio_readlineb 함수가 EOF를 만날 때까지 텍스트 줄을 반복해서 읽고 써줌<br><br>
4. echo 클라이언트와 서버 파일을 자동으로 컴파일 시켜주는 Makefile
    - Makefile이란?
        - shell에서 컴파일하는 방법 중 하나
        - makefile이라는 파일에 어떤 파일을 컴파일 하는지, 어떠한 방식으로 컴파일 할지 미리 작성해놓음
        - make라는 명령어를 통해 makefile이 들어있는 디렉토리에서 파일들의 종속관계를 파악하여 자동으로 컴파일 시켜줌<br><br>
    - Makefile의 장점
        - 여러 개의 파일을 컴파일할 경우, 자동화로 인해 시간 절약하고, 프로그램의 종속 구조를 쉽게 파악 가능
        - 프로그램이 일부 수정되었을 경우, 그 부분에 대해서만 컴파일 하도록 도와주기 때문에 훨씬 효율적<br><br>
    - Makefile 코드
        ```C
        CC = gcc
        CFLAGS = -O2 -Wall -I .
        LIB = -lpthread

        all: echo

        csapp.o: csapp.c
            $(CC) $(CFLAGS) -c csapp.c

        echoclient: echoclient.c csapp.o
            $(CC) $(CFLAGS) -o echoclient echoclient.c csapp.o $(LIB)

        echoserver: echoserver.c csapp.o
            $(CC) $(CFLAGS) -o echoserver echoserver.c csapp.o $(LIB)

        clean:
            rm -f *.o echo *~
            rm -f *.o echoclient *~
            rm -f *.o echoserver *~
        ```
5. echo 클라이언트와 서버 통신 방법
    - 서버를 먼저 실행시켜준 후, 클라이언트를 실행시켜 통신<br><br>
    - 서버 실행 방법
        - cd echo
        - make echoserver
        - ./echoserver 8000<br><br>
    - 클라이언트 실행 방법
        - cd echo
        - make echoclient
        - ./echoclient localhost 8000
    - make echoserver와 echoclient는 makefile을 따로 작성하여 사용한 것으로 사용자마다 다름<br><br>
6. 구현 결과
    - echo 클라이언트와 서버가 정상적으로 연결되었고, 서로 통신이 가능한 것을 확인했다.
    - 연결 및 통신 결과를 이미지로 넣으면 좋은데 아직 하는 방법을 몰라서 공부해야할 것 같다...😥<br><br>
    - 임시로 코드 블록을 통해 구현 결과를 확인해보자.
        ```
        - 클라이언트에서 아래 단어 입력
        hello
        hi

        - 서버 출력 결과
        server received 6 bytes
        server received 3 bytes
        ```

## 숙제 문제
### 11.6c 문제 풀이
1. 문제
    - Tiny의 출력을 조사해서 여러분이 사용하는 브라우저의 HTTP 버전을 결정하라.
        - 우선 CSAPP 솔루션이 나온 정답은 HTTP 1.1이지만, 1.0도 가능<br><br>
2. HTTP 버전 확인 방법
    - tiny 서버 실행
    - 브라우저에서 "AWS 퍼블릭 주소:포트번호"로 실행
    - 개발자 도구 실행
    - Network 항목 클릭
    - 마우스 오른쪽 클릭 -> header option 클릭 -> protocol 체크
    - 개발자 도구 인터페이스에 표시되는 protocol 항목에서 HTTP 버전 확인
    - 자세한 사항은 아래 사이트 참조
        - https://krksap.tistory.com/1152<br><br>
3. 1.0이 가능한 이유
    - serve_static 함수의 sprintf(buf, "HTTP/1.0 200 OK\r\n");을 그대로 사용하면 브라우저에서 HTTP 1.1을 전송해옴
    - sprintf(buf, "HTTP/1.1 200 OK\r\n");에서 HTTP 버전을 1.0로 변경하면 브라우저에서 HTTP 1.0을 전송해옴<br><br>
4. 그럼 2.0도 가능할까?
    - 가능할 것으로 보임
    - 하지만, tiny 서버를 이용하면 브라우저에서 HTTP 2.0을 전송받지 못함<br><br>
5. 왜 tiny 서버를 이용하면 브라우저에서 HTTP 2.0을 전송받지 못할까?
    - 헤더의 차이
        - HTTP/1.1
            - HTTP/1.1의 헤더는 평문이고, 크기가 500에서 800바이트 정도
            - 쿠키가 포함된 경우 킬로바이트까지 증가
            - 예를 들어, 네이버를 요청한 상태에서 또 다른 요청을 하게되면 헤더의 차이는 없음
            - 단, 중복된 필드가 존재할 것이고, 중복된 데이터를 다시 보낼 것
        - HTTP/2.0
            - HTTP/2.0에서는 헤더를 중복해서 전송하지 않고, 압축해서 전송
            - HTTP/1.1과 동일하게 네이버에 요청한 상태에서 또 다른 요청을 했다고 가정
                - 첫 번째 요청에서는 모든 필드를 모두 전송
                - 두 번째 요청에서는 중복되는 필드를 제외한 나머지 필드만을 전송
            - 이렇게 HTTP/2.0은 중복 헤더 데이터를 전송하지 않으므로 매 요청바다 오버헤드를 크게 줄일 수 있음
                - 같은 요청을 폴링하는 경우, 헤더가 변한게 없으므로 헤더 오버헤드는 0바이트
            - 위와 같이 헤더 중복을 제거하고, 과거 포스팅한 허프만 코딩 방식으로 또 한번 압축을 진행
            - 이런 방식으로 HTTP/2.0에서는 헤더 필드를 압축하여 프로토콜 오버헤더를 줄일 수 있음
            - 헤더 압축에 대해 더 자세한 사항은 RFC7540 공식 문서 참고
    - 즉, tiny 서버에는 HTTP/2.0의 헤더를 중복해 주는 기능을 수행하는 함수가 없어서 그렇지 않을까 하는 생각?!🤔

### 11.7 문제 풀이
1. 문제
    - Tiny를 확장해서 MPG 비디오 파일을 처리하도록 하시오. 실제 브라우저를 사용해서 여러분의 결과를 체크하시오.<br><br>
1. 문제 풀이 과정
    - get_filetype 함수에 MPG 비디오 파일을 처리하기 위한 코드 추가
    - 비디오가 브라우저에 표시될 수 있도록 home.html 파일에 video 태그 코드 추가
    - 두 파일 수정 후, 서버를 실행하여 브라우저에서 영상이 잘 나오는지 확인<br><br>
2. 수정된 get_filetype 함수
    ```C
    void get_filetype(char *filename, char *filetype)
    {
    if(strstr(filename, ".html"))
        strcpy(filetype, "text/html");
    else if(strstr(filename, ".gif"))
        strcpy(filetype, "image/gif");
    else if(strstr(filename, ".png"))
        strcpy(filetype, "image/png");
    else if(strstr(filename, ".jpg"))
        strcpy(filetype, "image/jpg");
    // 숙제 문제 11.7 : Tiny를 확장해서 MPG 비디오 파일 처리하기 위한 코드 추가
    else if(strstr(filename, ".mp4"))
        strcpy(filetype, "video/mp4");
    else
        strcpy(filetype, "text/plain");
    }
    ```
3. 수정된 home.html 파일
    ```HTML
    <html>
        <head><title>Tiny Web Sever Test</title></head>
        <body>
            <h2>Dave O'Hallaron</h2>
            <img align="middle" src="godzilla.gif">
            <br>
            <!-- 숙제 문제 11.7 : Tiny를 확장해서 MPG 비디오 파일 처리하기 위한 코드 추가 -->
            <h2>Thunder</h2>
            <video width="300" height="300" controls : autoplay>
                <source src="Thunder.mp4" type="video/mp4">
                Your browser does not support the video tag.
            </video>
        </body>
    </html>
    ```

### 11.9 문제 풀이
1. 문제
    - Tiny를 수정해서 정적 컨텐츠를 처리할 때 요청한 파일을 mmap과 rio_readn 대신에 malloc, rio_readn, rio_writen을 사용해서 연결 식별자에게 복사하도록 하시오.<br><br>
    - 문제가 좀 이상하다...😒
        - 원서에 있는 문제를 찾아보니 문제가 다르다는 것을 찾았고, 원서의 문제는 다음과 같다.
            - Modify Tiny so that when it serves static content, it copies the requested file to the connected descriptor using malloc, rio_readn, and rio_writen, instead of mmap and rio_writen.
            - 해석하면, Tiny를 수정해서 정적 컨텐츠를 처리할 때 요청한 파일을 mmap과 rio_writen 대신에 malloc, rio_readn, rio_writen을 사용해서 연결 식별자에게 복사하도록 하시오.(문제가 다르잖아?!...😑)
            - 즉, mmap과 munmap을 이용해서 메모리를 매핑하고 연결 식별자에게 복사하는 것을 malloc, free를 이용해서 메모리를 동적 할당 및 해제하고, rio_readn과 rio_writen을 사용해서 연결 식별자를 복사할 수 있도록 코드를 수정하면 될 것 같다.<br><br>
2. 수정된 serve_static 함수
    ```C
    void serve_static(int fd, char *filename, int filesize)
    {
    int srcfd;
    char *srcp, filetype[MAXLINE],buf[MAXBUF];

    /* Send response headers to client */
    get_filetype(filename, filetype);
    sprintf(buf, "HTTP/1.1 200 OK\r\n");
    sprintf(buf, "%sServer : Tiny Web Server\r\n", buf);
    sprintf(buf, "%sConnection : close\r\n", buf);
    sprintf(buf, "%sContent-length : %d\r\n", buf, filesize);
    sprintf(buf, "%sContent-type : %s\r\n\r\n", buf, filetype);
    Rio_writen(fd, buf, strlen(buf));
    printf("Response headers : \n");
    printf("%s", buf);

    /* Send response body to client */
    // 숙제 문제 11.9 : 정적 컨텐츠를 처리할 때 요청한 파일을 mmap과 rio_readn 대신에 malloc, rio_readn, rio_writen을 사용해서 연결 식별자에게 복사 처리 추가
    srcfd = Open(filename, O_RDONLY, 0);
    // srcp = Mmap(0, filesize, PROT_READ, MAP_PRIVATE, srcfd, 0);
    srcp = (char*)Malloc(filesize);
    Rio_readn(srcfd, srcp, filesize);
    Close(srcfd);
    Rio_writen(fd, srcp, filesize);
    // Munmap(srcp, filesize);
    free(srcp);
    }
    ```
3. 시스템 콜 mmap() 함수와 munmap() 함수
    - 6주차 malloc-lab 구현할 때, 공부했어야 하는 부분인데 하지 못해서 마침 문제에 언급이 있어서 학습했고, 내용은 다음과 같다.<br><br>
    - mmap() 함수
        - mmap() 함수 원형
            ```C
            #include <unistd.h>
            #include <sys/mman.h>
            
            void* mmap(void* start, size_t length, int prot, int flags, int fd, off_t offset);
            ```
        - mmap() 함수 설명
            - 파일이나 디바이스를 응용 프로그램의 주소 공간 메모리에 대응시키기 위해 사용하는 시스템 호출
            - fd로 저장된 디바이스 파일에서 offset에 해당하는 물리 주소에서 시작하여 length 바이트 만큼을 start 주소로 대응시킴
            - start 주소는 보통 0으로 지정하고, 강제적인 요구가 아니기 때문에 다른 값을 지정해도 꼭 그 값으로 대응시켜 반환되지는 않음
            - offset과 length는 PAGE_SIZE 단위여야 함
            - 지정된 영역에 대응되는 응용 프로그램에서 사용 가능한 실제 시작 위치를 반환
            - 성공하면 mmap은 대응된 영역의 포인터를 반환
            - 에러가 발생하면 MAP_FAILED(-1)이 반환되며, errno는 적당한 값으로 설정<br><br>
        - mmap() 함수 매개변수
            - start : 요청한 물리 주소 공간을 매핑하고자 하는 주소(보통은 0을 사용)
            - length : 매핑하고자 하는 주소 공간의 크기(PAGE_SIZE의 배수)
            - prot : 메모리 보호 모드 설정
                - PROT_EXEC : 페이지 실행 가능
                - PROT_READ : 페이지 읽기 가능
                - PROT_WRITE : 페이지 쓰기 가능
                - PROT_NONE : 페이지 접근 불가능
            - flag : 대응된 객체 타입, 옵션, 페이지 복사본에 대한 수정을 그 프로세스에만 보일 것인지 참조하는 다른 프로세스와 공유할 것인지 설정
                - MAP_FIXED : 지정된 주소 이외에는 선택하지 않음
                    - 지정된 주소를 사용할 수 없으면 mmap 실패
                - MAX_SHARED : 이 객체를 대응시키는 다른 프로세스와 대응 영역을 공유
                - MAX_PRIVATE : 다른 프로세스와 대응 영역을 공유하지 않음
            - fd : 디바이스 파일의 파일 디스크립터
            - offset : 매핑시키고 싶은 물리 주소<br><br>
        - mmap() 함수 반환값
            - 성공하면 대응된 영역의 포인터를 반환하고, 실패하면 MAP_FILED(-1)이 반환되고, errno는 다음 값으로 설정
            - EBADF: fd가 유효한 파일 디스크립터가 아님
            - EACCES: MAP_PRIVATE가 설정되었지만, fd가 읽을 수 있도록 열려있지 않음 / MAP_SHARED와 PROT_WRITE가 설정되었지만, fd가 쓸 수 있도록 열려있지 않음
            - EINVAL: start나 length나 offset이 적당하지 않음(즉, 너무 크거나 PAGESIZE 경계로 정렬되어 있지 않음)
            - ETXTBUSY: MAP_DENYWRITE가 설정되었으나 fd로 지정된 객체가 쓸수 있도록 열려있음
            - EAGAIN: 파일이 잠겨있거나 너무 많은 메모리가 잠겨있음
            - ENOMEM: 사용할 수 있는 메모리가 없음<br><br>
    - munmap() 함수
        - munmap() 함수 원형
            ```C
            #include <unistd.h>
            #include <sys/mman.h>
            
            int munmap(void* start, size_t length);
            ```
        - munmap() 함수 설명
            - 할당된 메모리 영역을 해제시키는데 사용하는 시스템 호출
            - start와 length는 mmap() 함수에 의해 반환된 주소와 매개변수로 지정한 length 값이어야 함<br><br>
        - munmap() 함수 매개변수
            - start : mmap에 의해서 반환된 주소
            - length : mmap에 의해서 지정된 크기<br><br>
        - munmap() 반환값
            - 성공하면 0을 반환하고, 실패하면 -1을 반환하며, errno가 설정(보통 EINVAL이 설정)
            - EBADF : fd가 유효한 파일 디스크립터가 아님
            - EINVAL : start나 length 혹은 offset이 적당하지 않음(즉, 너무 크거나 PAGESIZE 경계로 정렬되어 있지 않음)<br><br>
4. rio_readn()과 rio_writen() 함수
    - 문제를 풀면서 rio_readn()과 rio_writen() 함수가 어떤 기능을 수행하는지 궁금해져서 학습했다.
    - rio_readn()과 rio_writen() 함수 소스코드는 csapp.c에 정의되어 있다.
    - 하지만, 아래 사이트(펜실베이니아 주립대학교에서 제공하는 사이트인듯)의 코드에 좀 더 자세하게 주석처리가 되어있어서 참고하여 학습했고, 해당 내용은 다음과 같다.
        - https://www.cse.psu.edu/~deh25/cmpsc311/Lectures/System-Level-I_O.html<br><br>
    - rio_readn() 함수
        - rio_readn() 함수 소스코드
            ```C
            ssize_t rio_readn(int fd, void *usrbuf, size_t n)
            {
            size_t nleft = n;        /* 0 <= nleft == n */
            char *bufp = usrbuf;

            while (nleft > 0) {      /* loop invariant: 0 <= nleft <= n */
                ssize_t rc = read(fd, bufp, nleft);
                if (rc < 0) {          /* read() error */
                if (errno == EINTR)  /* interrupted by a signal */
                    continue;          /* no data was read, try again */
                else
                    return -1;         /* errno set by read(), give up */
                    /* ??? It may be that some data was read successfully
                    * on a previous iteration.  Is it correct to give up
                    * entirely?  In some cases, yes, but always?
                    */
                }
                if (rc == 0)           /* EOF */
                break;
                bufp += rc;            /* read() success, 0 < rc <= nleft */
                nleft -= rc;           /* 0 <= new nleft < old nleft <= n */
            }

            return (n - nleft);      /* return >= 0 */
            }
            ```
        - rio_readn() 함수 설명
            - 견고하게 n 바이트를 읽는 함수(버퍼링되지 않음)
            - 바이트 수를 반환하거나 EOF의 경우 0, 오류의 경우 -1을 반환
            - n 바이트 미만을 사용할 수 있으면, 정상적으로 반환
            - n 바이트가 요청되면(n = 0) rio_ 함수는 아무 작업도 수행하지 않음
            - 표준 함수 read() 및 write()는 다른 인수의 유효성을 확인한 다음 다른 작업을 수행하지 않음<br><br>
    - rio_writen() 함수
        - rio_writen() 함수 소스코드
            ```C
            ssize_t rio_writen(int fd, void *usrbuf, size_t n)
            {
            size_t nleft = n;        /* 0 <= nleft == n */
            char *bufp = usrbuf;

            while (nleft > 0) {      /* loop invariant: 0 <= nleft <= n */
                ssize_t rc = write(fd, bufp, nleft);
                if (rc < 0) {          /* write() error */
                if (errno == EINTR)  /* interrupted by a signal */
                    continue;          /* no data was written, try again */
                else
                    return -1;         /* errno set by write(), give up */
                    /* ??? It may be that some data was written successfully
                    * on a previous iteration.  Is it correct to give up
                    * entirely?  In some cases, yes, but always?
                    */
                }
                if (rc == 0)           /* nothing written, but not an error */
                continue;            /* try again */
                bufp += rc;            /* write() success, 0 < rc <= nleft */
                nleft -= rc;           /* 0 <= new nleft < old nleft <= n */
            }

            return n;
            }
            ```
        - rio_writen() 함수 설명
            - 견고하게 n 바이트를 씁는 함수(버퍼링되지 않음)
            - 바이트 수를 반환하거나 오류 시 -1을 반환
            - n 바이트 미만이 기록되면 오류를 반환

### 11.10 문제 풀이
1. 문제 A
    - 그림 11.27의 CGI adder 함수에 대한 HTML 형식을 작성하시오.
    - 이 형식은 사용자가 함께 더할 두 개의 숫자로 채우는 두 개의 텍스트 상자를 포함해야 한다.
    - 여러분의 형식은 GET 메소드를 사용해서 컨텐츠를 요청해야 한다.<br><br>
    - form-adder.html 소스코드
        ```html
        <html>
            <head><title>Tiny Sever</title></head>
            <body>
                <form action="/cgi-bin/form-adder" method="GET">
                    <p>First Number : <input type="text" name="first"/></p>
                    <p>Second Number : <input type="text" name="second"/></p>
                    <input type="submit" value="Submit"/>
                </form>
            </body>
        </html>
        ```
    - HTML form 태그
        - 사용자가 입력하거나 선택한 정보를 서버로 전송하기 위해서 쓰는 태그
        - 사용자 입력을 위한 요소로 사용자로부터 정보를 수집하는 역할 수행
        - 주로, 웹페이지 상에서 로그인, 회원가입 등에 사용<br><br>
        - form 태그 속성
            - action : 폼 전송 버튼을 눌렀을 때 이동하는 페이지의 경로를 지정
            - name : 폼을 식별하기 위한 이름을 지정
            - accept-charset : 폼 전송에 사용할 문자 인코딩 지정
            - target : action에서 지정한 파일을 현재 창이 아닌 다른 위치에 열도록 지정
                - target 속성
                    - _blank : 서버로부터 받은 응답을 새로운 윈도우나 탭에서 보여줌
                    - _self : 서버로부터 받은 응답을 링크가 위치한 현재 프레임에서 보여줌(기본값으로 생략 가능)
                    - _parent : 서버로부터 받은 응답을 현재 프레임의 부모 프레임에서 보여줌
                    - _top : 서버로부터 받은 응답을 명시된 프레임에서 보여줌
            - method : 폼을 서버에 전송할 http 메소드 지정(GET 또는 POST)
                - GET
                    - URL 끝에 데이터를 붙여 전송하는 방법
                    - 데이터가 외부에 노출되어 보안에 취약
                    - 지정된 리소스에서 데이터를 요청하는 경우인 읽을 때 사용하는 메소드
                - POST
                    - URL에 보이지 않게 데이터를 전성하는 방법
                    - 보내려는 데이터가 개인 정보나 보안을 요구하는 경우 사용
                    - 지정된 리소스에서 데이터를 처리할 경우인 쓰기, 수정, 삭제할 때 사용<br><br>
2. 문제 B
    - 실제 브라우저를 사용해서 Tiny로부터 이 형식을 요청하고, 채운 형식을 Tiny에 보내고, adder가 생성한 동적 컨텐츠를 표시하는 방법으로 작업을 체크하라.
    - form-adder 함수 소스코드
        ```C
        #include "csapp.h"

        int main(void) {
        char *buf, *p;
        char arg1[MAXLINE], arg2[MAXLINE], content[MAXLINE];
        int n1 = 0, n2 = 0;

        /* Extract the two arguments */
        if((buf = getenv("QUERY_STRING")) != NULL) {
            p = strchr(buf, '&');
            *p = '\0';
            // strcpy(arg1, buf);
            // strcpy(arg2, p + 1);
            // n1 = atoi(arg1);
            // n2 = atoi(arg2);
            sscanf(buf, "first = %d", &n1);
            sscanf(p + 1, "second = %d", &n2);
        }

        /* Make the response body */
        // sprintf(content, "QUERY_STRING = %s", buf);
        sprintf(content, "Welcome to add.com: ");
        sprintf(content, "%sTHE Internet addition portal. \r\n<p>", content);
        sprintf(content, "%sThe answer is : %d + %d = %d\r\n<p>", content, n1, n2, n1 + n2);
        sprintf(content, "%sThanks for visiting!\r\n", content);

        /* Generate the HTTP response */
        printf("Connection: close\r\n");
        printf("Content-length: %d\r\n", (int)strlen(content));
        printf("Content-type: text/html\r\n\r\n");
        printf("%s", content);
        fflush(stdout);
        
        exit(0);
        }
        ```