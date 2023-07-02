# CHAPTER12 : 동시성 프로그래밍(Concurrent Programming)
1. 동시성이란?
    - 특정 프로세스의 실행 시간이 다른 프로세스의 흐름과 겹치는 상황에서 동시에 실행하는 것<br><br>
2. 동시성 프로그램을 만드는 세 가지 방법
    - 프로세스
        - 각 논리적 흐름은 커널이 스케줄하고 관리하는 프로세스
        - 프로세스가 별도의 가상 주소공간을 가지기 때문에, 서로 통신하기를 원하는 흐름들은 모종의 명시적 프로세스간 통신 interprocess communication(IPC) 메커니즘을 사용해야 함<br><br>
    - I/O 다중화
        - <br><br>
    - 쓰레드
        - <br><br>
3. IPC(Inter-Process Communication)
    - 프로세스간의 통신을 위한 메커니즘<br><br>
    - 두 가지 모델
        - Shared Memory
            - <br><br>
        - Message Passing
            - <br><br>

## 12.1 프로세스를 사용한 동시성 프로그래밍
