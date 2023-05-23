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
### CSAPP 11장 11.6c 문제 풀이
1. Tiny의 출력을 조사해서 여러분이 사용하는 브라우저의 HTTP 버전을 결정하라.
    - 우선 CSAPP 솔루션이 나온 정답은 HTTP 1.1이지만, 1.0도 가능<br><br>
    - HTTP 버전 확인 방법
        - tiny 서버 실행
        - 브라우저에서 "AWS 퍼블릭 주소:포트번호"로 실행
        - 개발자 도구 실행
        - Network 항목 클릭
        - 마우스 오른쪽 클릭 -> header option 클릭 -> protocol 체크
        - 개발자 도구 인터페이스에 표시되는 protocol 항목에서 HTTP 버전 확인
        - 자세한 사항은 아래 사이트 참조
            - https://krksap.tistory.com/1152<br><br>
    - 1.0이 가능한 이유
        - serve_static 함수의 sprintf(buf, "HTTP/1.0 200 OK\r\n");을 그대로 사용하면 브라우저에서 HTTP 1.1을 전송해옴
        - sprintf(buf, "HTTP/1.1 200 OK\r\n");에서 HTTP 버전을 1.0로 변경하면 브라우저에서 HTTP 1.0을 전송해옴<br><br>
    - 그럼 2.0도 가능할까?
        - 가능할 것으로 보임
        - 하지만, tiny 서버를 이용하면 브라우저에서 HTTP 2.0을 전송받지 못함<br><br>
    - 왜 tiny 서버를 이용하면 브라우저에서 HTTP 2.0을 전송받지 못할까?
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