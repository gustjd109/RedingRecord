# CHAPTER10 : 시스템 수준 입출력(System-Level I/O)

## 10.5 RIO 패키지를 이용한 안정적인 읽기와 쓰기
1. RIO(Robust I/O) Package란?
    - 이름이 Robust인 이유는 이 I/O 장치의 특성 때문이며, 입출력 장치를 견고하게 만들어준다는 의미를 지니고 있음
    - 짧은 카운트가 발생할 수 있는 네트워크 프로그램 같은 응용에서 편리하고, 안정적이고 효율적인 I/O룰 재공
    - 두 가지 서로 다른 종류의 함수를 제공
    
### 10.5.1 RIO 버퍼 없는 입력 및 출력 함수
1. RIO 버퍼 없는 입력 및 출력 함수란?
    - 메모리와 파일 간에 버퍼링 없이 직접 데이터 전송 가능
     - 2진 데이터를 읽고 쓸 때 유용<br><br>
2. RIO 버퍼 없는 입력 및 출력 함수 정의
    ```C
    #include "csapp.h"

    asize_t rio_readn(int fd, void *usrbuf, size_t n);
    asize_t rio_writen(int fd, void *usrbuf, size_t n);
    Returns : number of bytes transferred if OK, 0 on EOF(rio_readn only), -1 on error
    ```
3. rio_readn() 함수
    - rio_readn() 함수 소스코드
        ```C
        ssize_t rio_readn(int fd, void *usrbuf, size_t n) 
        {
            size_t nleft = n;
            ssize_t nread;
            char *bufp = usrbuf;

            while (nleft > 0) {
            if ((nread = read(fd, bufp, nleft)) < 0) {
                if (errno == EINTR) /* Interrupted by sig handler return */
                nread = 0;      /* and call read() again */
                else
                return -1;      /* errno set by read() */ 
            } 
            else if (nread == 0)
                break;              /* EOF */
            nleft -= nread;
            bufp += nread;
            }
            return (n - nleft);         /* Return >= 0 */
        }
        ```
    - 식별자 fd의 현재 파일 위치에서 메모리 위치 usrbuf로 최대 n 바이트를 전송
    - 바이트 수를 반환하거나 EOF의 경우 0(rio_readn 함수에만 해당), 오류의 경우 -1을 반환
    - n 바이트 미만을 사용할 수 있으면, 정상적으로 반환
    - n 바이트가 요청되면(n = 0) 함수는 아무 작업도 수행하지 않음<br><br>
4. rio_writen() 함수
    - rio_writen() 함수 소스코드
        ```C
        ssize_t rio_writen(int fd, void *usrbuf, size_t n) 
        {
            size_t nleft = n;
            ssize_t nwritten;
            char *bufp = usrbuf;

            while (nleft > 0) {
            if ((nwritten = write(fd, bufp, nleft)) <= 0) {
                if (errno == EINTR)  /* Interrupted by sig handler return */
                nwritten = 0;    /* and call write() again */
                else
                return -1;       /* errno set by write() */
            }
            nleft -= nwritten;
            bufp += nwritten;
            }
            return n;
        }
        ```
    - usrbuf에서 식별자 fd로 n 바이트를 전송
    - 바이트 수를 반환하거나 오류 시 -1을 반환
    - n 바이트 미만이 기록되면 오류를 반환
    
### 10.5.2 RIO 버퍼를 통한 입력 함수
1. RIO 버퍼를 통한 입력 함수란?
    - 텍스트 라인들과 내용이 버퍼에 캐시되어 있는 파일의 2진 데이터를 효율적으로 읽도록 해줌
    - 버퍼를 사용하기 때문에 쓰레드-안전(thread-safe)하며, 같은 식별자에서 임의로 중첩될 수 있음
    - 텍스트 라인 전체를 내부 읽기 버퍼에서 복사하는 rio_readlineb() 함수와 텍스트 라인과 2진 데이터 모두를 읽을 수 있는 rio_readnb() 함수가 있음
        - 파일 -> 읽기 버퍼 -> (읽기 버퍼를 읽고 그 데이터를)메모리 버퍼로 복사<br><br>
2. RIO 버퍼를 통한 입력 함수 정의
    ```C
    #include "csapp.h"

    void rio_readinitb(rio_t *rp, int fd);
                                                        Returns : nothing
    asize_t rio_readlineb(rio_t *rp, void *usrbuf, size_t maxlen);
    asize_t rio_readnb(rio_t *rp, void *usrbuf, size_t n);
    Returns : number of bytes read if OK, 0 on EOF, -1 on error
    ```
3. rio_readinitb() 함수
    - rio_readinitb() 함수 소스코드
        ```C
        void rio_readinitb(rio_t *rp, int fd) 
        {
            rp->rio_fd = fd;  
            rp->rio_cnt = 0;  
            rp->rio_bufptr = rp->rio_buf;
        }
        ```
    - open한 식별자마자 한 번 호출
    - 읽고 싶은 파일 식별자 fd와 읽기 버퍼 rp를 연결<br><br>
4. rio_readnb() 함수
    - rio_readnb() 함수 소스코드
        ```C
        ssize_t rio_readnb(rio_t *rp, void *usrbuf, size_t n) 
        {
            size_t nleft = n;
            ssize_t nread;
            char *bufp = usrbuf;
            
            while (nleft > 0) {
            if ((nread = rio_read(rp, bufp, nleft)) < 0) 
                    return -1;          /* errno set by read() */ 
            else if (nread == 0)
                break;              /* EOF */
            nleft -= nread;
            bufp += nread;
            }
            return (n - nleft);         /* return >= 0 */
        }
        ```
    - 최대 n 바이트를 파일 rp로부터 메모리 위치 usrbuf로 읽음
    - 텍스트 라인과 2진 데이터 모두를 읽을 수 있고, n 바이트씩 가져옴
    - 바이트 수를 반환하거나 EOF의 경우 0, 오류의 경우 -1을 반환<br><br>
5. rio_readlineb() 함수
    - rio_readlineb() 함수 소스코드
        ```C
        ssize_t rio_readlineb(rio_t *rp, void *usrbuf, size_t maxlen) 
        {
            int n, rc;
            char c, *bufp = usrbuf;

            for (n = 1; n < maxlen; n++) { 
                if ((rc = rio_read(rp, &c, 1)) == 1) {
                *bufp++ = c;
                if (c == '\n') {
                        n++;
                    break;
                    }
            } else if (rc == 0) {
                if (n == 1)
                return 0; /* EOF, no data read */
                else
                break;    /* EOF, some data was read */
            } else
                return -1;	  /* Error */
            }
            *bufp = 0;
            return n-1;
        }
        ```
    - 텍스트 라인 전체를 내부 읽기 버퍼 rp에서 읽은 후, 메모리 버퍼 usrbuf으로 복사하고, 0(NULL)로 텍스트 라인을 종료
    - 최대 maxlen - 1개의 바이트를 읽으며, 종료용 0(NULL) 문자를 위한 공간을 남겨둠
    - maxlen - 1 바이트를 넘는 텍스트 라인들은 잘라내서 NULL 문자로 종료
    - 바이트 수를 반환하거나 EOF의 경우 0, 오류의 경우 -1을 반환