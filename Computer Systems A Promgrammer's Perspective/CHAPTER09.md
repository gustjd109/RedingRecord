# CHAPTER09 : 가상메모리(Virtual Memory)

## 9.9 동적 메모리 할당
- 가상메모리의 영역을 저수준의 mmap과 munmap 함수를 사용해서 생성하고 삭제할 수 있다.
- 하지만, 대개 추가적인 가상메모리를 런타임에 획득할 필요가 있을 때, 동적 메모리 할당기를 사용하는 것을 더 선호한다.
- 동적 메모리 할당기
    - 힙(heap)이라고 하는 프로세스의 가상메모리 영역을 관리한다.
    - 데이터와 힙의 경계는 Top of the heap이 있고, 이를 가리키는 포인터 변수 brk(break)가 있다.
    - 힙을 다양한 크기의 블록들의 집합으로 관리한다.
        - 블록 : 할당되었거나 가용한 가상메모리의 연속적인 묶음
    - 할당된 블록은 응용하기 위해 명시적으로 보존된다.
    - 가용한(free) 블록은 할당을 위해 사용할 수 있고, 응용이 명시적으로 할당할 때까지 가용한 상태로 남아있다.
    - 할당된 블록은 응용에 의해 명시적으로 또는 메모리 할당기에 의해 묵시적으로 반환될 때까지 할당된 채로 남아있다.  
    <img src="https://velog.velcdn.com/images/supssson/post/00548582-4f9a-4b51-8549-ed538cff5d70/image.jpeg" height="300" width="500"></img>
    - 할당기 유형
        - 두 개의 유형이 있으며 모두 응용이 명시적으로 블록을 할당하도록 요구하지만, 반환할 때의 차이점이 있다.
        - 명시적인 할당기(Explicit allocators)
            - 명시적으로 할당된 블록을 반환해 줄 것을 요구한다.
            - C언어에서는 malloc 함수로 블록 할당하고, free 함수로 블록을 반환한다.
            - C++에서는 new와 delete로 블록을 할당하고 반환한다.
        - 묵시적 할당기(Implicit allocators)
            - 자동으로 사용자가 함수로서 명시적 반환을 요구하지 않아도 알아서 사용하지 않는 메모리를 반환하도록 하는 것이다.
            - 가비지 컬렉터(garbage collector)라고도 부른다.
                - 가비지 컬렉터 : 자동으로 사용하지 않은 할당된 블록을 반환시켜주는 작업
                - List, ML, 자바 같은 상위수준 언어들은 할당된 블록들을 반환시키기 위해 가비지 컬렉션을 사용한다.

## 9.9.1 malloc과 free 함수
- malloc 함수
    ```C
    #include <stdlib.h>
    void *malloc(size_t, size);
    // Returns : pointer to allocated block if OK, NULL on error
    ```
    - C 표준 라이브러리는 malloc 패키지로 알려진 명시적인 할당기를 제공한다.
    - 프로그램은 malloc 함수를 호출해서 힙으로부터 블록들을 할당받는다.
    - 블록 내에 포함될 수 있는 어떤 종류의 데이터 객체에 대해서 적절히 정렬된 최소 size 바이트를 갖는 메모리 블록의 포인터를 반환한다.
        - 할당받는 메모리의 크기는 32비트에서는 주소가 8의 배수이며, 64비트에서는 16배수이다.
            - 최소 단위의 메모리 크기가 32비트에서는 8바이트, 64비트에서는 16바이트라는 것을 의미한다.
                - 그러면 왜 GNU의 malloc이 32비트에서 더블 워드 정렬, 64비트에서 쿼드 워드 정렬을 하는 것일까?
                    - OS가 데이터를 주고 받는 단위(몇 워드로 주고 받는가)와 관련
                    - 자료형과 관련
                        - 8바이트인 자료형을 모두 커버하기 위해 32비트 사용해야 한다.
                        - long double은 컴파일러마다 크기가 다르다.
                            - TC++ 컴파일러에서는 10바이트, Dev-C++ 컴파일러에서는 12바이트, 유닉스나 리눅스에서 사용하는 gcc 컴파일러에서는 16바이트로 구현되어 있다.
    - 할당받은 메모리를 초기화하고 싶다면 calloc 함수를 사용한다. -> 할당된 메모리를 0으로 초기화 해준다.
    - 이전에 할당받은 메모리의 크기를 변경하고 싶다면 realloc 함수를 사용한다.
    - mmap과 munmap 함수를 사용해서 명시적으로 힙 메모리를 할당하거나 반환하며, sbrk 함수를 사용한다.
- brk 함수
    ```C
    #include <unistd.h>
    int *brk(void *addr)
    ```
    - addr 주소 위치에 program break(현재 프로세스의 data segment의 끝부분 바로 다음 자리)를 설정한다.
    - program break의 위치를 옮김으로써 메모리를 할당하거나 반환하고, 성공 시 brk는 0을, 실패 시 -1을 반환한다.
- sbrk 함수
    ```C
    #include <unistd.h>
    void *sbrk(intptr_t incr);
    // Returns : old brk pointer on success, -1 on error
    ```
    - 커널의 brk 포인터에 incr(increment)을 더해서 힙을 늘리거나 줄인다.
    - 성공 시 이전의 brk 값을 반환하고, 실패 시 -1을 반환한다.
    - incr이 0이면, sbrk는 현재의 brk 값을 반환한다.
    - sbrk를 음수 incr로 호출하면 합법적이기는 하지만, 리턴 값(이전의brk 값)이 새로운 힙의 탑을 지나서 abs(incr) 바이트를 가리키기 때문에 복잡해진다.
- free 함수
    ```C
    #include <stdlib.h>
    void free(void *ptr);
    // Returns : nothing
    ```
    - 더 이상 할당받은 메모리가 필요하지 않다면 free 함수로 반환한다.
    - ptr 인자는 malloc, calloc, realloc에서 획득한 할당된 블록의 시작을 기리켜야 한다.
        - free 함수는 리턴값이 없기 때문에 제대로 동작 했다는 것을 알기 어렵고, 런타임 에러를 유발할 가능성이 있기 때문이다.
    - (d)는 p2에 할당된 6워드 블록을 반환해준 상태이다.
        - free 함수를 호출하여 블록을 반환하더라도 포인터 p2는 여전히 malloc된 블록을 가리킨다.
        - p2가 새로운 malloc 콜에 의해 다시 초기화될 때까지 p2를 사용하지 않는다.  
        <img src="https://velog.velcdn.com/images/supssson/post/b54954a3-cf0c-42c1-9143-c29fa8836608/image.jpeg" height="150" width="500"></img>

## 9.9.2 왜 동적 메모리 할당인가?
- 동적 메모리 할당을 사용하는 이유는 프로그램을 실제 실행시키기 전에는 자료 구조의 크기를 알 수 잆는 경우들이 존재하기 때문이다.
    - 예를 들어, n개의 정수 리스트를 라인마다 한 개씩 배열로 읽는 프로그램을 작성 한다고 하자.
        - 입력은 정수 n과 읽고 배열에 저장해야 하는 n개의 정수로 구성되어 있다.
        - 가장 간단한 방법은 배열을 정해진 최대 배열 크기를 갖는 정적 배열로 정의하는 것이다.
        ```C
        #include <csapp.h>
        #define MAXN 15213

        int array[MAXN];

        int main()
        {
            int i, n;

            scanf("%d", &n);
            if(n > MAXN)
                app_error("Input file too big");
            for(i = 0; i < n; i++)
                scanf("%d", &array[i]);
            exit(0);
        }
        ```
        - 위 코드처럼 배열을 정해진 크기를 사용해서 할당하는 것은 종종 나쁜 방법이 된다.
            - MAXN보다 더 큰 파일을 읽으려고 한다면, 프로그램을 더 큰 MAXN 값을 사용해서 컴파일하는 것이 유일한 대책이다.
        - 더 나은 방법은 n 값을 알 수 있을 때 배열을 런타임에 동적으로 할당하는 것이다.
            - 배열의 최대 크기는 가용한 가상메모리의 양에 의해서만 제한된다.
            ```C
            #include <csapp.h>
        
            int main()
            {
                int *array, i, n;

                scanf("%d", &n);
                array = (int *)malloc(n * sizeof(int));
                for(i = 0; i < n; i++)
                    scanf("%d", &array[i]);
                free(array);
                exit(0);
            }
            ```

## 9.9.3 할당기 요구사항과 목표
- 명시적 할당기들은 다소 엄격한 제한사항 내에서 동작한다.
- 요구사항
    - 임의의 요청 순서 처리하기
        - 할당기는 할당/반환 요청이 들어왔을 때, 즉시 그 요청을 시행해야 한다.
    - 요청에 즉시 응답하기
        - 할당기는 블록들을 이들이 어떤 종류의 데이터 객체라도 저장할 수 있도록 하는 방식으로 정렬해야 한다.
    - 힙만 사용하기
        - 확장성을 갖기 위해서 할당기가 사용하는 비확장성 자료 구조들은 힙 자체에 저장되어야 한다.
    - 블록 정렬하기(정렬 조건)
        - 할당기는 블록들을 이들이 어떤 종류의 데이터 객체라도 저장할 수 있도록 하는 하는 방식으로 정렬해야 한다.
    - 할당된 블록을 수정하지 않기
        - 할당기는 가용 블록을 조작하거나 변경할 수만 있다.
        - 블록이 할당되면 이들을 수정하거나 이동하지 않는다.
        - 따라서, 할당된 블록들을 압축하는 것 같은 기법들은 허용되지 않는다.
- 목표
    - 처리량 극대화하기
        - n번의 할당과 반환 요청의 배열이 주어졌을 때, 할당기의 처리량을 최대화하려고 하며, 이것은 단위 시간당 완료되는 요청의 수로 정의한다.
        - 일반적으로, 할당과 반환 요청들을 만족시키기 위한 평균 시간을 최소화해서 처리량을 최대화한다.
    - 메모리 이용도를 최대화하기
        - 한 시스템에서 모든 프로세스에 의해 할당된 가상메모리의 양은 디스크 내의 스왑 공간의 양에 의해 제한된다.
        - 최고 이용도(peak utilization)
            - 할당기가 얼마나 힙을 효울적으로 사용하는지를 규정하는 방법 중에서 가장 유용한 단위이다.  
            <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FRvzhk%2FbtqTF2K38h0%2FUOGkXByKJ2eOwd0dexNn60%2Fimg.png"></img>
            - 간단하게 말하면, 이용도U = ( 실제로 사용하는 힙의 크기 = maxPi ) / ( 할당된 힙의 크기 = Hk )
            - 즉, U = 1이면 낭비되는 것 없이 힙을 온전히 그대로 사용하고 있고, U = 0 이라면 실제로 사용되는 힙이 없음에도 불구하고 힙이 할당되어 있는 것이다.

## 9.9.4 단편화(Fragmentation)
- 단편화
    - 메모리 공간이 작은 조각으로 나눠져 사용 가능한 메모리가 충분히 존재하지만 할당이 불가능한 상태일 떄 발생한다.
- 단편화 종류
    - 내부 단편화
        - 할당된 블록이 요구하는 데이터보다 더 크게 할당되어 사용하는 메모리 공간을 낭비할 때 발생한다.
        - 예를 들어, 아래 그림을 보자.  
        <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbfy5Je%2FbtqTIpMKXVU%2FNGzbVdi7cNZbyfjxxdrHW0%2Fimg.png"></img>
            - 정수는 4바이트이므로 5 * sizeof(int)는 5칸을 차지한다.
            - 하지만, 64바이트에서는 8바이트씩(2칸씩)만 배치할 수 있으므로 2의 배수로 할당해서 6칸을 차지한다.
            - 따라서, 4바이트가 낭비되고 있는 상태이다.
        - 정량화하기가 간단
            - 단순히 할당된 블록의 크기와 이들의 데이터 사이의 차이의 합이다.
    - 외부 단편화
        - 총 메모리 공간은 충분하지만, 요청을 처리할 수 있는 단일한 가용 블록이 없을 때 발생한다.
        - 예를 들어, 아래 그림을 보자.  
        <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FSQYyv%2FbtqTF3QMGDL%2FcMgVLmauXSFSISfdR3UZek%2Fimg.png"></img>
            - 현재 여유 메모리는 6칸(24바이트)이나, 4칸과 2칸으로 나누어져 있으므로 24바이트 블록을 할당할 수 없다.
        - 할당기의 요구사항 중 "할당된 블록을 수정하지 않기"로 인해 할당된 블록을 수정하거나 이동이 불가능하여 정렬할 수 없으므로 대응이 어렵다.
        - 내부 단편화보다 측정하기 더 어렵고 예측하기 불가능하기 때문에 할당기들은 대개 많은 수의 더 작은 가용 블록들보다는 더 적은 수의 큰 블록들을 유지하려는 방법을 채택한다.
            - 예를 들어, 8칸을 배정할 경우 4개로 쪼개서 2칸씩 여기저기에 배치하는 것 보다는 8칸짜리로 한 곳에 배치하는 것을 선호한다는 것이다.

## 9.9.5 구현 이슈
- 예를 들어, 아래 그림을 보자.  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FzkXmy%2FbtqTDbPAksa%2FlqNCnFS46kDcNSCl2OTYA1%2Fimg.png"></img>
    - p1, p2, p3는 각각 할당된 공간을 가리키는 포인터이며, p1, p2, p3는 할당된 공간의 처음을 가리키고, p4는 p3가 사용하는 오른쪽 공간에 온다고 생각해보자.
        - 여기서 free(p2)를 했는데, 사실 free 함수는 아무것도 하지 않는다고 가정했을 경우 2칸을 배정받아도 p2의 공간이 아닌 p3 다음에 올 것이다.
        - 이 경우에는 연산이 매우 간단하므로(심지어 free는 아무것도 하지 않음), 속도는 매우 빠를 것이나 금방 메모리가 가득찰 것이다.
    - 반대로, free가 해당 공간을 비우고 다음 배정을 할 경우, 메모리의 상황에서 모든 경우의 수를 고려해서 새로운 공간을 배정한다고 하자.
        - 그 경우에는 메모리의 공간은 극도로 효율적일 것(최고이용도라고 쓰기도 함)이나, 속도는 매우 느릴 것이다.
    - 따라서, 할당기는 속도와 공간의 효율사이에서 좋은 균형을 추구해야하며, 다음과 같은 이슈를 고려해야 한다.
- 고려해야할 이슈
    - 가용블록 구성 : 어떻게 메모리의 비어있는 공간이 어디에, 얼마만큼 있는지 알 것인가?
    - 배치 : 어떻게 블록을 배치해야 효율적으로 공간을 사용할 수 있을 것인가?
    - 분할 : 새로운 블록을 배치한 후, 남은 부분들로 무엇을 할 것인가?
    - 연결 : 방금 반환된 블록으로 무엇을 할 것인가?

## 9.9.6 묵시적 가용 리스트(Implicit Free Lists)
- 실전에서 쓰이는 할당기는 블록 경계를 구분하고, 할당된 블록과 가용 블록을 구분하기 위해 데이터 구조를 필요로 한다.
- 대부분의 할당기는 이 정보를 블록 내에 저장하며, 단순한 접근법은 아래와 같다.  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Feibcwo%2FbtqTAEdnzDC%2FeKvz2Xx1TBOc5VODBhhiT1%2Fimg.png"></img>
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fnwhyy%2FbtreY4nhU2p%2FRPkdUIVNYUYCkDnm0LCjok%2Fimg.png"></img>
    - 한 블록은 1워드(4바이트) 헤더, 데이터와 패딩으로 구성된다.
    - 헤더(header)
        - 블록 크기(헤더와 추가적인 패당 포함)와 블록이 할당되었는지 가용 상태인지에 대한 정보를 담고 있다.
        - 더블 워드 정렬 제한조건을 부과한다면 블록 크기는 항상 8의 배수이고, 블록 크기의 하위 3비트는 항상 0이다.
        - 4바이트의 32비트 중 상위 29비트만 저장할 필요가 있으며, 나머지 3비트는 다른 정보를 인코드하기 위해 남겨둔다.
        - 위의 그림에서 헤더 부분의 a를 보면 1일 때는 할당 상태, 0일 때는 가용 상태이다.
    - 데이터(payload)
        - 헤더 다음에는 응용이 malloc을 불렀을 때 요구한 데이터가 따라온다.
    - 패딩(padding)
        - 데이터 다음에는 사용하지 않는 패딩이 따라올 수 있는데, 크기는 가변적이다.
        - 만약 8바이트의 메모리를 할당하고 싶다고 하자.
            - 헤더 4바이트 + 데이터 8바이트 = 12 바이트의 공간이 필요하다.
            - 하지만, 메모리는 8바이트 단위로 묶여있기 떄문에 실제로는 16바이트를 할당받게 되고, 쓰지않는 4바이트의 공간이 남아있다.
            - 이 남아있는 4바이트의 공간을 패딩이라고 한다.
        - 패딩을 하는 이유
            - 외부 단편화를 극복하기 위한 할당기 전략의 일부분일 수 있다.
            - 정렬 요구사항을 만족하기 위해 필요할 수 있다.
- 묵시적 리스트
    - 힙을 연속된 할당 및 가용 블록의 배열로 첫 번째와 같이 구성된 구조를 묵시적 리스트라고 한다.
    - 가용 블록이 헤더 내 필드에 의해서 묵시적으로 연결되기 때문이다.
- 묵시적 가용 리스트
    - 장점 : 단순성
    - 단점 : 할당된 블록을 배치하는 것과 같은 가용 리스트를 탐색해야 하는 연산들의 비용이 힙에 있는 전체 할당된 블록과 가용 블록의 수에 비례한다는 것이다.

## 9.9.7 할당한 블록의 배치
- 어플리케이션이 메모리 할당을 요청할 때, 할당기는 요구되는 메모리 만큼의 가용 블록이 있는지를 검색할 것이다.
- 이때, 할당기가 이 검색을 수행하는 방법은 배치 정책에 의해서 결정된다.
- 이 정책에는 first fit, next fit, best fit이 주로 사용된다.
- first fit
    - 가용 리스트를 처음부터 검색해서 크기가 맞는 첫 번째 가용 블록을 선택한다.
    - 장점 : 리스트의 마지막에 가장 큰 가용 블록들을 남겨두는 경향이 있다.
    - 단점 : 리스트의 앞부분에 작은 가용 블록들을 남겨두는 경향이 있어서 큰 블록을 찾는 경우에 검색 시간이 늘어난다.(크든 작든 한 블록을 검사하는 것에 같은 시간이 걸린다)
- next fit
    - first fit에 대한 대안으로 만들어졌으며, 이전 검색에서 가용 블록을 발견했다면, 처음부터 검색하는 것이 아니라 이전 검색에서부터 검색을 시작한다는 것이다.
    - first fit보다 최악의 메모리 이용도를 갖는 것으로 밝혀졌다.
    - 장점 : 대체로 블록은 앞에서부터 채워나가기 때문에 원하는 블록을 빨리 찾을 가능성이 높다.
    - 단점 : 대신에 앞에 가용 블록이 남아있어도 검색 시도를 하지 않기 때문에 메모리 이용도가 낮아지게 된다.
- best fit
    - 처음부터 전부 검색하여, 크기가 딱 맞는 블록이 어떤것인지 추려내서 할당한다.
    - first fit이나 next fit보다는 더 좋은 메모리 이용도를 갖는다는 것으로 밝혀졌다.
    - 장점 : 모든 경우의 수에서 가장 최선의 수를 선택하기 때문에 최고의 메모리 이용도를 가진다.
    - 단점 : 힙을 완전히 다 검색해서 최저의 속도를 가지게 된다.

## 9.9.8 가용 블록의 분할
- 할당기가 크기가 맞는 가용 블록을 찾은 후에 가용 블록의 어느 정도를 할당할지에 대해 정책적 결정을 내려야 한다.
- 한 가지 옵션은 이 가용 블록 전체를 사용하는 것이다.
    - 간단하고 빠르지만, 내부 단편화가 생긴다는 큰 단점이 있다.
    - 만일 배치정책으로 인해 크기가 잘 맞는다면, 일부 추가적인 내부 단편화는 수용할 수도 있다.
    - 크기가 잘 안 맞는다면, 할당기는 대개 가용 블록을 두 부분으로 나누게 된다.
        - 첫 번째 부분은 할당한 블록이 되고, 나머지는 새로운 가용 블록이 된다.
- 그림을 보면서 좀 더 쉽게 이해 해보자.  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcEdBMS%2FbtqTIo1xYyh%2F0OVg1ihG42nNmDmFV2V1XK%2Fimg.png"></img>
    - 만약 위 그림과 같은 상황에서 8바이트(2칸)의 공간 할당을 원한다고 하자.
    - 그럼 맨 앞(왼쪽)에서 검색해서 P2에 도달했을 때, 48바이트(6칸)의 공간이 있는 것을 알게된다.  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fd27KeF%2FbtqTJmh7KUZ%2FQ5ROk3uRAZkgA1yNyJJDJk%2Fimg.png"></img>
    - 이때, 2가지의 경우의 수가 있다.
    - 위 그림과 같이 P2에서 P3까지 공간을 전부 할당시키는 것이다.
    - 그렇다면 연산은 단순해서 속도는 빠르겠지만, 16바이트(4칸)의 내부 단편화가 생기게 된다. 즉, 비효율적이라는 말이다.  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fep3QDw%2FbtqTDcuicjb%2FTm6jPmcIHYFRXlZZx4DmTK%2Fimg.png"></img>
    - 두번째 방안으로는 위 그림처럼 필요한 만큼만 할당하는 것이다.
    - 약간의 연산은 필요하겠지만, 메모리의 활용도는 높아지게 된다.

## 9.9.9 추가적인 힙 메모리 획득하기
- 할당기가 요청한 블록을 찾을 수 없는 경우에는 어떻게 해야할까?
    - 메모리에서 물리적으로 인접한 가용 블록들을 연결해서 더 큰 가용 블록을 만들어본다.
        - 이렇게 해도 충분히 큰 불록이 만들어지지 않거나 가용 블록이 이미 최대로 연결되었다면, 할당기는 커널에게 sork 함수를 호출해서 추가적인 힙 메모리를 요청한다.
        - 할당기는 추가 메모리를 더 큰 가용 블록으로 변환하고, 이 블록을 가용 리스트에 삽입한 후에 요청한 블록을 이 새로운 가용 블록에 배치한다.

## 9.9.10 가용 블록 연결하기
- 오류 단편화(false fragmentation)
    - 할당기가 할당한 블록을 반환할 때, 새롭게 반환하는 블록에 인접한 다른 가용 블록들을 병합해주지 않아 발생하는 현상이다.
    - 이런 인접 가용 블록들을 병합해주지 않으면, 충분한 메모리가 있음에도 불구하고 할당하지 못하게 된다는 의미다.
    - 그림을 통해 오류 단편화를 확인해 보자.  
        <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdv61vu%2FbtqTLw5m6cC%2Fq5ufFKB5jWv1KFdSdkivq0%2Fimg.png"></img>
        - 위 그림을 보면 각각 3워드의 데이터를 갖는 두 개의 인접한 블록들이 있다.
        - 4워드를 요청했을 때, 두 개의 가용 블록 전체의 크기는 요청을 만족시킬 정도로 충분히 크지만 할당에 실패한다.
- 오류 단편화를 극복하기 위해 사용하는 방법
    - 연결(coalescing) 과정을 통해 사용하고 있던 블록이 반환되었을 때, 전후에 가용 블록이 있는지 검색하여 병합해 준다.
    - 즉시 연결(immediate coalescing) : 블록이 반환되었을 때, 바로 앞뒤를 병합해 준다.
        - 단순하고 좋게 보이지만, 불필요한 때에도 병합을 해서 무의미한 연산을 할 가능성이 높다.
    - 지연 연결(deferred coalescing) : 할당 요청이 실패할 때 모든 가용 블록을 검색하여 병합해 준다.

## 9.9.11 경계 태그로 연결하기
- 할당기는 어떻게 연결을 구현하는가?
    - 반환하려고 하는 블록을 현재 블록(current block)이라고 할 때, 다음 가용 블록을 연결하는 것은 간단하다.
    - 현재 블록의 헤더의 주소에서 헤더의 크기를 더해주면 다음 블록의 헤더를 찾을 수 있다.
    - 하지만, 이전 블록을 찾기 위해서는 앞에서부터 다시 탐색해야 가능하기 때문에 비효율적이다.
    - 이런 해결할 방법이 없을까?
        - 경게 태그라는 기법이 있다.
- 경계 태그(footer)  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FKCxo1%2FbtreVxRNgmu%2FrwsTmAbea1TenixiKImD20%2Fimg.png" height="300"></img>
    - 헤더를 그대로 복사하여 블록의 끝에 추가한다.
    - 4byte를 차지하게 된다.
    - 상수 시간에 이전 블록을 연결하게 해준다.
    - 다만 작은 크기의 블록을 여러개 할당하면 상당한 양의 메모리 오버헤드가 발생한다.
    - 그런데 우리가 4바이트의 정수만을 할당받는다고 가정해보자.
        - 그렇다면 블록의 구성은 헤더(4바이트)+데이터(4바이트)+풋터(4바이트)의 12바이트의 블록을 할당받게 된다.
        - 즉, 4바이트의 공간을 위해서 8바이트의 추가정보가 필요하게 되니 매우 비효율적이다.
        - 작은 크기의 블록을 다룬다면 상당한 양의 메모리 오버헤드가 발생할 수 있고, 헤더와 풋터가 할당된 블록의 절반을 차지하게 될 수도 있다.
    - 그러면 경계 태그는 언제 사용해야 가장 효율적인가?
        - 그 블록의 헤더로 돌아갈 때이다.
        - 그렇다면 어떤 경우에 블록의 헤더로 돌아가게 되는가?
            - 그건 그 블록이 가용일 경우에만 해당되고, 비가용일 경우에는 헤더로 돌아갈 필요가 없다.
            - 즉, 할당했을 경우에는 블록의 끝에 풋터를 달아놓을 이유가 없다.
            - 블록을 반환했을 때, 비어진 블록 끝에 풋터를 달아놔서 다음 블록이 가용으로 바뀌었을 때, 이 블록과 병합하도록 하는 것이다.
    - 경계 태그의 존재 의의는 한 블록을 반환했을 경우 그 다음 가용 블록은 찾기 쉽지만, 앞에 있는 가용블록은 찾기 어려워서 달아놓은 것이 핵심이다.
- 할당기가 현재 블록을 반환할 때 가능한 네 가지 경우 모두를 연결하는 방법  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdxRMmN%2FbtrBOuwUW7K%2Fwf8a4eUdKWMvOMoTRoPm20%2Fimg.png"></img>
    - 이전 블록과 다음 블록 모두 할당상태다.
        - 현재 블록의 상태를 변경하면 된다.
    - 이전 블록은 할당상태, 다음 블록은 가용상태다.
        - 현재블록과 다음 블록이 통합된다.
    - 이전 블록은 가용상태, 다음 블록은 할당상태다.
        - 이전 블록의 헤더와 현재 블록의 풋터는 두 블록의 크기를 합한 것이다.
        - 이전 블록과 다음 블록 통합
    - 이전 블록과 다음 블록 모두 가용상태다.
        - 세 블록 모두 하나의 가용 블록으로 통합

## 9.9.12 종합 설계 : 간단한 할당기의 구현
- 경계 태그 연결을 사용하는 묵시적 가용 리스트에 기초한 간단한 할당기의 구현을 따라가 보기로 한다.
- 할당기 기본 설계
    - 우리의 할당기는 아래 코드에서 보여주는 것과 같은 memlib.c 패키지에서 제공되는 메모리 시스템의 모델을 사용한다.
        ```C
        /* Private global variables */
        static char *mem_heap;  /* 힙의 처음을 가리키는 포인터 */
        static char *mem_brk; /* 배당 받은 힙 공간의 마지막 자리를 가리키는 포인터 */
        static char *mem_max_addr; /* MAX_HEAP의 끝 자리를 나타내는 포인트이며, 이 이상으로는 할당할 수 없음 */
        
        /* *mem_init - Initilaize the memory system model */
        
        // 할당기를 초기화하고, 성공하면 0을, 아니면 -1을 반환
        void mem_init(void)
        {
            mem_heap = (char *)Malloc(MAX_HEAP);
            mem_brk = (char *)mem_heap;
            mem_max_addr = (char *)(mem_heap + MAX_HEAP);
        }
        
        /*
        * mem_sbrk - Simple model of the sbrk function. Extends the heap
        * by incr bytes and returns the start address of the new area.
        * In this model, the heap cannot be shrunk
        */
        
        // incr(할당 요청이 들어왔을 때, 요청된 용량)가 들어왔을 때, 이것이 MAX_HEAP을 초과하지 않으면 추가로 mem_brk를 할당 요청 용량만큼 옮긴다.
        // 용량의 크기가 음수이거나, MAX_HEAP을 초과하면 error 메세지를 반환한다.
        void *mem_sbrk(int incr)
        {
            char *old_brk = mem_brk;
            
            if ( (incr < 0) || ((mem_brk + incr) > mem_max_addr)){
                errno = ENOMEM;
                fprintf(stderr, "ERROR: mem_sbrk failed. Ran out of memory...\n")
                return (void *) -1;
            }
            mem_brk += incr;
            return (void *)old_brk;
        }
        ```
        - 이 모델의 목적은 우리가 설계한 할당기가 기존의 시스템 수준의 malloc 패키지와 상관없이 돌 수 있도록 하기 위한 것이다.
- 할당기의 기본 블록 구성
    - 최소 블록 크기는 16바이트이고, 가용 리스트는 묵시적 가용 리스트로 구성되며, 아래 그림과 같이 변하지 않는 형태를 갖는다.  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fp4Hoa%2Fbtre4Odp0ce%2FyoRVbdKPA3hY4pFcBdFMKK%2Fimg.png"></img>
    - 첫 번째 워드
        - 더블 워드 경계로 정렬된 미사용 패딩 워드이다.
    - 프롤로그(prolog) 블록
        - 패딩 다음에는 특별한 프롤로그(prolog) 블록이 오며, 이것을 header와 footer로만 구성된 8바이트 할당 블록이다.
        - 프롤로그 블록은 초기화 과정에서 생성되며 절대 반환하지 않는다.
    - 일반 블록
        - 프롤로그 다음에는 malloc 또는 free를 호출해서 생성된 0 또는 1개 이상의 일반 블록들이 온다.
    - 에필로그(epilogue) 블록
        - 힙은 항상 특별한 에필로그(epilogue) 블록으로 끝나며, 헤더만으로 구성된 크기가 0으로 할당된 블록이다.
    - 프롤로그와 에필로그 블록들은 연결과정 동안에 가자앚리 조건을 없애주기 위한 속임수다.
    - 할당기는 한 개의 정적(static) 전역변수를 사용하며, 항상 프롤로그 블록을 가리킨다.
- 가용 리스트 조작을 위한 기본 상수와 매크로
    -  할당기 코드 전체에서 가용 리스트 조작을 위한 기본 상수 및 매크로를 정의하는 코드는 다음과 같다.
        ```C
        /* 기본 상수 및 매크로 */
        #define WSIZE 4 // 워드와 헤더 및 풋터 크기 정의
        #define DSIZE 8 // 더블 워드 크기 정의
        #define CHUNKSIZE (1<<12) // 초기 가용 블록과 확장시 추가되는 블록 크기 정의

        #define MAX(x, y) ((x) > (y)? (x) : (y)) // 최대값 구하는 함수 정의

        /* 헤더 및 풋터의 값(크기와 할당 여부) 반환 */
        #define PACK(size, alloc) ((size) | (alloc)) // 크기와 할당 비트를 통합해서 헤더와 풋터에 저장할 수 있는 값 반환

        /* 주소 p에서 워드를 읽거나 쓰는 함수 */
        #define GET(p) (*(unsigned int *)(p)) // 포인터 p가 가리키는 블록의 데이터 반환
        #define PUT(p, val) (*(unsigned int *)(p) = (val)) // 포인터 p가 가리키는 블록의 값 저장

        /* 헤더 또는 풋터에서 블록의 크기와 할당된 구역을 읽어옴 */
        // & ~0x7 => 0x7:0000 0111 ~0x7:1111 1000이므로 ex. 1011 0111 & 1111 1000 = 1011 0000 : size 176bytes
        #define GET_SIZE(p) (GET(p) & ~0x7) // 포인터 p가 가리키는 헤더 또는 풋터의 크기 반환
        // & 0x1 => ex. 1011 0111 | 0000 0001 = 1 : Allocated
        #define GET_ALLOC(p) (GET(p) & 0x1) // 포인터 p가 가리키는 헤더 또는 풋터의 할당 비트 반환

        /* 각각 블록 헤더와 풋터가 가리키는 포인터 반환 */
        // bp : 현재 블록 포인터
        #define HDRP(bp) ((char *)(bp) - WSIZE) // 현재 블록 헤더의 위치 반환(bp - 1워드)
        #define FTRP(bp) ((char *)(bp) + GET_SIZE(HDRP(bp)) - DSIZE) // 현재 블록 풋터의 위치 반환(bp + 현재 블록 크기 - 더블 워드 크기)

        /* 각각 이전 블록과 다음 블록을 가리키는 포인터 반환 */
        #define NEXT_BLKP(bp) ((char *)(bp) + GET_SIZE(((char *)(bp) - WSIZE))) // 다음 블록의 블록 포인터 반환(bp + 현재 블록 크기 - 1워드)
        #define PREV_BLKP(bp) ((char *)(bp) - GET_SIZE(((char *)(bp) - DSIZE))) // 이전 블록의 블록 포인터 반환(bp - 현재 블록 크기 - 2워드)
        ```
- 초기 가용 리스트 만들기
    - mm_malloc이나 mm_free를 호출하기 전에 어플리케이션은 mm_init 함수를 호출해서 힙을 초기화해야 한다.
    - mm_init 함수는 최초의 가용 블록(4words)을 가지고 힙을 생성하고 할당기를 초기화한다. 성공하면 0, 실패하면 -1을 리턴한다.  
        <img src="https://github.com/dkkim0122/malloc-lab/blob/main/image/Untitled(14).png?raw=true" height="150"></img>
        - mm_init 함수에서 할당받은 4번째칸의 구조는 다음과 같다.
        - 1번째 칸 : 0을 적어넣는다.
        - 2번째 칸 : 용량은 2워드이며, 1(할당됨) 이라는 헤더를 적어 넣는다. 
        - 3번째 칸 : 용량은 2워드이며, 1 이라는 풋터를 적어 넣는다.(2번째, 3번째 칸은 prologue block이라고 칭한다.)
        - 4번째 칸 : 용량은 0워드이며, 1 이라는 헤더를 적어 넣는다.(이 칸은 앞으로 epilogue block header 라고 칭한다.)
    - 빈 가용 리스트를 초기화해 주는 mm_init 함수의 코드는 다음과 같다.
        ```C
        int mm_init(void)
        {
            /* 메모리에서 4워드 가져와서 빈 힙 생성 */
            // mem_sbrk: 힙 영역을 incr(0이 아닌 양수) 바이트 만큼 확장하고, 새로 할당된 힙 영역의 첫 번째 바이트를 가리키는 제네릭 포인터를 반환
            if((heap_listp = mem_sbrk(4 * WSIZE)) == (void *) -1) // heap_listp에 4워드 만큼의 메모리를 확장
                return -1; // 실패 시 -1 반환

            PUT(heap_listp, 0);                                 // 더블 워드 경계로 정렬된 미사용 패딩
            PUT(heap_listp + (1 * WSIZE), PACK(DSIZE, 1));      // 프롤로그 헤더를 1워드 할당하고 DSIZE로 값 삽입
            PUT(heap_listp + (2 * WSIZE), PACK(DSIZE, 1));      // 프롤로그 풋터를 1워드 할당하고 DSIZE로 값 삽입
            PUT(heap_listp + (3 * WSIZE), PACK(0, 1));          // 에필로그 헤더를 1워드 할당하고 0 사이즈 삽입
            heap_listp += (2 * WSIZE); // 정적 전역 변수는 늘 prologue block을 가리킴

            /* CHUNKSIZE 만큼 힙을 확장해 초기 가용 블록을 생성 */
            if(extend_heap(CHUNKSIZE / WSIZE) == NULL) // 힙을 CHUNKSIZE 바이트로 확장하고 초기 가용 블록을 생성
                return -1; // 실패 시 -1 반환
            return 0;
        }
        ```
- 힙을 초기화한 후, extend_heap 함수를 호출해서 힙을 CHUNKSIZE 만큼 힙을 확장해 초기 가용 블록을 생성한다.
    - 힙을 CHUNKSIZE 바이트로 확장하고 초기 가용 블록을 생성하는 extend_heap 함수의 코드는 다음과 같다.
        ```C
        static void *extend_heap(size_t words)
        {
            char *bp;
            size_t size;

            size = (words % 2) ? (words + 1) * WSIZE : words * WSIZE; // 워드가 홀수인 경우 정렬을 위해 인접 2워드의 배수(8바이트)로 반올림하며, 추가적인 힙 공간 요청
            if((long)(bp = mem_sbrk(size)) == -1) // mem_sbrk 함수에서 size가 할당 가능한 힙의 범위를 초과했다고 판단하면, -1을 반환하고 할당하지 않음
                return NULL;

            PUT(HDRP(bp), PACK(size, 0)); // 새로운 블록에 헤더 생성
            PUT(FTRP(bp), PACK(size, 0)); // 새로운 블록의 푸터 생성
            PUT(HDRP(NEXT_BLKP(bp)), PACK(0, 1)); // 새로운 블록 뒤에 붙는 에필로그 헤더 생성

            return coalesce(bp); // 이전 힙이 가용 블록으로 끝났다면, 두 개의 가용 블록을 통합하기 위해 coalesce 호출
        }
        ```
        - extend_heap은 두 가지 다른 경우에 호출한다.
            - 힙이 초기화될 경우와 mm_malloc이 적당한 맞춤 fit을 찾지 못했을 경우 호출한다.
        - 위 경우일 때 정렬을 유지하기 위해서 extend_heap은 요청한 크기를 인접 2워드의 배수(8배수)로 반올림하고, 메모리 시스템으로부터 추가적인 힙 공간을 요청한다.
- 블록 반환
    - 응용은 이전에 할당한 블록을 mm_free 함수를 호출해서 반환한다.
    - 블록을 반환하는 mm_free 함수의 코드는 다음과 같다.
        ```C
        void mm_free(void *ptr)
        {
            size_t size = GET_SIZE(HDRP(ptr)); // 해당 블록의 크기를 알아내 헤더와 풋터의 정보를 수정

            PUT(HDRP(ptr), PACK(size, 0)); // 헤더를 0으로 할당
            PUT(FTRP(ptr), PACK(size, 0)); // 풋터를 0으로 할당
            coalesce(ptr); // coalesce를 호출하여 가용 메모리를 연결
        }
        ```
- 블록 연결
    - coalesce 함수는 mm_free 함수를 이용하여 블록을 반환하고, 경계 태그 연결을 사용해서 상수 시간에 인접 가용 블록들과 통합한다.
    - 직전 블록의 풋터과 직후 블록의 헤더를 보고 가용 여부를 확인한다.
    - 가용 블록을 연결하는 coalesce 함수의 코드는 다음과 같다.
        ```C
        static void *coalesce(void *bp)
        {
            // 직전 블록의 풋터과 직후 블록의 헤더를 보고 가용 여부를 확인
            size_t prev_alloc = GET_ALLOC(FTRP(PREV_BLKP(bp))); // 직전 블록 가용 여부
            size_t next_alloc = GET_ALLOC(HDRP(NEXT_BLKP(bp))); // 직후 블록 가용 여부
            size_t size = GET_SIZE(HDRP(bp));

            if(prev_alloc && next_alloc) { // case 1 : 이전, 다음 블록이 모두 할당인 경우 -> 현재만 반환
                return bp;
            }

            else if(prev_alloc && !next_alloc) { // case 2 : 이전 블록 할당, 다음 블록 가용인 경우 -> 다음 블록과 병합
                size += GET_SIZE(HDRP(NEXT_BLKP(bp)));
                PUT(HDRP(bp), PACK(size, 0));
                PUT(FTRP(bp), PACK(size, 0));
            }

            else if(!prev_alloc && next_alloc) { // case 3 : 이전 블록 가용, 다음 블록 할당인 경우 -> 이전 블록과 병합
                size += GET_SIZE(HDRP(PREV_BLKP(bp)));
                PUT(FTRP(bp), PACK(size, 0));
                PUT(HDRP(PREV_BLKP(bp)), PACK(size, 0));
                bp = PREV_BLKP(bp);
            }

            else { // case 4 : 이전, 다음 블록 모두 가용인 경우 -> 이전 블록과 다음 블록 병합
                size += GET_SIZE(HDRP(PREV_BLKP(bp))) + GET_SIZE(FTRP(NEXT_BLKP(bp)));
                PUT(HDRP(PREV_BLKP(bp)), PACK(size, 0));
                PUT(FTRP(NEXT_BLKP(bp)), PACK(size, 0));
                bp = PREV_BLKP(bp);
            }
            return bp;
        }
        ```
- 블록 할당
    - 어플리케이션은 mm_malloc 함수를 호출하여 size 바이트의 메모리 블록을 요청한다.
    - 추가적인 요청들을 체크한 후에 할당기는 요청한 블록 크기를 조절해서 헤더와 풋터를 위한 공간을 확보하고, 더블 워드 요건을 만족시킨다.
    - 블록을 할당하는 mm_malloc 함수의 코드는 다음과 같다.
        ```C
        void *mm_malloc(size_t size)
        {
            size_t asize; /* 조정된 블록 크기 */
            size_t extendsize; /* 적합하지 않은 경우 힙을 확장할 크기 */
            char *bp;

            /* 가짜 요청 무시 */
            // 만약 할당 요청받은 용량이 0이면 그냥 반환
            if(size == 0)
                return NULL;

            /* 오버헤드 및 정렬 요구 사항을 포함하도록 블록 크기를 조정 */
            if(size <= DSIZE) // 사이즈가 2워드(8바이트) 이하라면 4워드로 할당 요청(앞의 1워드는 헤더에, 뒤의 1워드는 풋터)
                asize = 2 * DSIZE;
            else // 할당 요청의 용량이 2워드 초과(8바이트의 배수에 맞지 않은 경우)라면 충분한 8바이트의 배수의 용량을 할당
                asize = DSIZE * ((size + (DSIZE) + (DSIZE - 1)) / DSIZE);

            /* 적당한 크기의 가용 블록을 가용 리스트에서 검색 */
            if((bp = find_fit(asize)) != NULL) { // find_fit 함수로 적당한 크기의 가용 블록을 검색
                place(bp, asize); // place 함수로 초과 부분을 분할하고 새롭게 할당한 블록의 블록 포인터를 반환
                return bp;
            }

            /* 적당한 크기의 가용 블록을 찾지 못한다면 extend_heap 함수로 힙을 확장하여 추가 확장 블록을 배정 */
            extendsize = MAX(asize, CHUNKSIZE); // 둘 중 더 큰 값으로 사이즈 지정
            // extend_heap()은 워드 단위로 인자를 받으므로 WSIZE로 나눔
            if((bp = extend_heap(extendsize / WSIZE)) == NULL) // 힙 확장 실패 시 NULL을 반환(extendsize / WSIZE = 칸의 개수)
                return NULL;
            place(bp, asize); // 힙 확장 성공 시 블록을 배치하고 bp 반환
            return bp;
        }
        ```
        - 동적 할당할 메모리 크기를 받아 알맞은 크기의 블록을 할당한다.
        - 만약 후보가 없으면 힙을 CHUNKSIZE만큼 늘려 할당 받는다. 그리고 할당 받은 블록의 포인터를 반환한다.
- 적절한 가용 블록 검색
    - mm_malloc 함수에서 find_fit 함수를 통해 적당한 크기의 가용 블록을 검색한다.
    - 적당한 가용 블록을 검색하는 find_fit 함수의 코드는 다음과 같고, 여기서는 first_fit을 사용한다.
        ```C
        static void *find_fit(size_t asize)
        {
            void *bp;

            // Free list에서 유일한 할당 블록은 리스트 맨 뒤의 프롤로그 블록이므로, 할당 블록을 만나면 모든 list 노드들을 다 확인한 것으로 탐색 종료
            for(bp = heap_listp; GET_SIZE(HDRP(bp)) > 0; bp = NEXT_BLKP(bp)) { // bp는 힙의 시작점을 가리키는 heap_listp에서 시작하여 에필로그 블록까지 탐색
                if(!GET_ALLOC(HDRP(bp)) && (asize <= GET_SIZE(HDRP(bp)))) { // 만약 해당 헤더의 블록이 할당되어있지 않고(GET_ALLOC 함수로 판단), 사이즈도 원하는 크기(asize)보다 크다면(GET_SIZE로 판단)
                    return bp; // bp를 반환
                }
            }
            return NULL; // 못 칮으면 NULL 반환
        }
        ```
- 데이터 배치
    - 요청한 블록을 가용 블록의 시작 부분에 배치하고, 나머지 부분의 크기가 최소 블록 크기와 같거나 큰 경우에는 분할한다.
    - 데이터를 배치하고 분할하는 place 함수의 코드는 다음과 같다.
        ```C
        static void place(void *bp, size_t asize) // place 함수는 데이터를 할당할 가용 블록의 bp와 배치 용량을 할당받음
        {
            size_t csize = GET_SIZE(HDRP(bp)); // 할당할 가용 블록의 전체 크기를 csize로 저장

            if((csize - asize) >= (2 * DSIZE)) { // 분할이 가능한 경우
                // 요청 용량만큼 블록을 배치하고 헤더와 풋터를 배치
                PUT(HDRP(bp), PACK(asize, 1));
                PUT(FTRP(bp), PACK(asize, 1));
                // 남은 블록에 헤더와 풋터를 배치
                bp = NEXT_BLKP(bp);
                PUT(HDRP(bp), PACK(csize-asize, 0));
                PUT(FTRP(bp), PACK(csize-asize, 0));
            }
            else { // csize와 asize의 차이가 네 칸(16바이트)보다 작다면 해당 블록을 통째로 사용하고, 헤더와 풋터를 배치
                PUT(HDRP(bp), PACK(csize, 1));
                PUT(FTRP(bp), PACK(csize, 1));
            }
        }
        ```
- 메모리 재할당
    - mm_realloc 함수는 malloc으로 할당 받은 메모리 영역의 size를 변경하여 재할당받는 역할을 한다.
    - 여기서 이미 할당된 블록의 크기를 직접 건드리는 것이 아니라, 임시로 요청 크기 만큼의 블록을 만들고 현재의 블록을 반환해야 한다.
    - 또한 memcpy 함수를 이용하여 블록 내 데이터도 같이 옭겨준 다음 기존 블록을 반환한다.
    - mm_realloc 함수의 코드는 다음과 같다.
        ```C
        void *mm_realloc(void *ptr, size_t size)
        {
            void *oldptr = ptr;
            void *newptr;
            size_t copySize;
            
            newptr = mm_malloc(size);
            if (newptr == NULL)
            return NULL;
            copySize = GET_SIZE(HDRP(oldptr));
            if (size < copySize)
            copySize = size;
            memcpy(newptr, oldptr, copySize);
            mm_free(oldptr);
            return newptr;
        }
        ```
- Implicit, first-fit으로 구현된 할당기 테스트 결과
    - 점수 : Perf index = 44 (util) + 9 (thru) = 53/100
    - 가용 블록 탐색 시 항상 처음 위치에서 시작하는 방식으로, 처리량(Thru) 점수가 낮게 나온다.
    - 또한, 내부 단편화와 외부 단편화가 많이 생길 수 있는 가능성이 있다.
- Implicit, next-fit으로 구현된 할당기 테스트 결과
    - 점수 : Perf index = 44 (util) + 38 (thru) = 83/100
    - 가용 블록 탐색 시 마지막 탐색 지점부터 시작하는 방식으로, first fit 대비 빨리 가용블록을 찾을 확률이 높다.
- Implicit, best-fit으로 구현된 할당기 테스트 결과
    - 점수 : Perf index = 45 (util) + 8 (thru) = 53/100
    - 가용 블록 탐색 시 first-fit과 마찬가지로 힙의 처음부터 끝까지 탐색하지만, 가장 알맞는 크기의 블록을 찾을 수 있으므로 메모리 이용도는 다른 방법보다 우수하다.

## 9.9.13 명시적 가용 리스트(Explicit Free List)
- 묵시적 가용 리스트는 블록 할당 시간이 전체 힙 블록의 수에 비례하기 때문에 처리 속도가 느려 범용 할당기에는 적합하지 않다.
    - 힙 블록의 수가 사전에 알려져 있고, 작고 특수한 경우에는 좋을 수도 있다.
- 명시적 가용 리스트
    - 가용 블록들을 일종의 명시적 자료구조로 구성해 가용 리스트들을 모두 연결한다.
    - 아래 그림처럼 각 블록 내에 이전 가용 블럭의 시작 주소를 가리키는 Pred(predeccessor)와 다음 가용 블록의 시작 주소를 가리키는 SUCC(successor) 포인터를 포함하는 이중 연결 가용 리스트로 구성될 수 있다.  
        <img src="https://velog.velcdn.com/images%2Fwhddn0221%2Fpost%2F275f263c-d3d7-4487-87fc-1f48c41a7667%2Fimage.png"></img>
    - 이중 연결 가용 리스트를 이용하여 명시적 가용 리스트를 구현할 경우 first fit 탐색 시간을 가용 블록의 수에 비례하는 것으로 줄일 수 있다.
    - 명시적 가용 리스트를 사용할 때 가용 블록을 어떤 방식으로 정렬하는 가에 따라 블록의 반환 시간은 리스트의 크기에 비례하거나 상수 시간을 가질 수 있다.
- 명시적 가용 리스트 내에서 블록을 정렬하는 두 가지 정책
    - LIFO(후입선출, Last In First Out)
        - 새롭게 반환한 블록들을 리스트의 시작 부분에 삽입하고, first fit을 사용하는 방식이다.
        - 할당기가 최근에 사용된 블록들을 먼저 조사하기 때문에 블록의 반환은 상수 시간에 수행된다.
        - 경계 태그를 사용하면 연결도 상수 시간에 수행된다.
    - 주소 순으로 정렬
        - 리스트를 주소 순으로 정렬해 리스트 내 각 블록의 주소가 다음 블록의 주소보다 작도록 하는 것이다.
        - 이때 블록을 반환할 때 리스트 내에서 블록이 들어갈 위치를 찾아야 하므로 선형 검색 시간이 필요로 하다.
        - 주소 순으로 정렬하는 방식은 LIFO 방식보다 best fit의 이용도에 근접하는 더 좋은 메모리 이용도를 가진다.
- 명시적 가용 리스트 단점
    - 가용 블록들이 충분히 커서 모든 필요한 포인터 뿐만 아니라 헤더와 풋터까지 포함해야 한다는 것이다.
        - 그 결과, 최소 블록 크기가 커지고 잠재적인 내부 단편화 가능성이 증가한다.

## 9.9.14 분리 가용 리스트(Segregated Free List)
- 단일 연결 가용 블록 리스트를 사용하는 할당기는 한 개의 블록을 할당하는 데 가용 블록의 수에 비례하는 시간이 필요하다.
- 분리 가용 리스트
    - 다수의 가용 리스트를 유지하며, 각 리스트는 거의 동일한 크기의 블록들을 저장한다.
    - 기본적인 아이디어는 모든 가능한 블록 크기를 크기 클래스라고 하는 동일 클래스의 집합들로 분리하는 것이다.
- 크기 클래스를 정의하는 방법
    - 블록 크기를 2의 제곱으로 나누는 방법
        ```
        [1], [2, 3], [4 ~ 7], [8 ~ 15], [16 ~ 31], ...
        ```
    - 크기가 작은 블록들은 자신의 크기 클래스에 할당하고, 큰 블록들은 2의 제곱으로 분리하는 방법
        ```
        [1], [2], [3], [4], ... , [1024], [1025], ... , [2048 ~ 4095], [4096 ~ 8192] ...
        ```
- 할당기는 가용 리스트의 배열을 관리하고, 크기 클래스마다 크기가 증가하는 순서로 한 개의 가용 리스트를 가진다.
- 할당기가 크기 n의 블록이 필요하면 적당한 가용 리스트를 검색한다.
- 이때 리스트 내부에 가용 가능한 블록이 없다면, 다음 리스트를 검색한다.
- 나머지는 명시적 가용 리스트를 이용한 할당기와 대부분 비슷하다.

## 참조
- 컴퓨터 시스템(Computer Systems A Programmer's Perspective)
- https://firecatlibrary.tistory.com/34
- https://wonyoung2257.tistory.com/87
- https://github.com/dkkim0122/malloc-lab