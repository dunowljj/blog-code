Memory_Management 3
===
이화여자대학교 반효경 교수님의 운영체제 강의를 요약한 내용입니다. 틀린 부분이 있다면 지적해주시면 감사하겠습니다.  [강의 링크](http://www.kocw.net/home/cview.do?cid=4b9cd4c7178db077)

## 1.5. Multilevel Paging and Performance
![images.png](./images/multi_paging.png)
- 4단계 -> 메모리가려면 5번 접근 필요
- 대부분 TLB를 사용해서 4단계여도 속도가 많이 줄어든다.
- 메모리 접근시간 100, TLB접근 시간 20, TLB hit ratio가 98%인 경우 0.98 * 120 + 0.02 * 520 = 128 이라고 써져있다.
    - 0.98 * (20(TLB) + 100(메모리)) / 0.02 * (20(TLB) + (100(메모리) * 5(접근 횟수)))로 이해하면 된다.
    - TLB의 정보로 메모리에 한 번 접근하기 때문에 20 + 100 이며, 4단계 페이지 테이블은 4번 접근 후 메모리에 접근하기때문에 5 * 100이다.

## 1.6. 부가적인 비트들
![images.png](./images/val_in.png)
### 1.6.1. Valid(v) / Invalid(i)
- 사실 엔트리마다 부가적인 비트가 더 저장되어 있음. 그 중 하나
- valid 표시의 경우 : 해당 주의 frame에 유효한 내용(접근 허용)
- invalid의 경우 : 유효한 내용이 없음
    - 1 프로세스가 그 주소 부분을 사용하지 않는 경우
    - 2 해당 페이지가 메모리에 올라와 있지 않고, swap area에 있는 경우 등
### 1.6.2. Memory Protection
- read/write/read-only
- Protection bit : 접근 권한의 제어는 어떤 연산에 대한 접근 권한이 있는가를 말함. 다른 프로세스는 애초에 접근 불가능해서 상관없다. ex) code는 read-only로 data나 stack은 read/write를 다 준다.


## 1.7. Inverted Page Table

![images.png](./images/invert_pt1.png)
페이지 테이블이 각 프로세스마다 존재하는게 아니고, 시스템 안에 페이지 테이블이 딱 하나 존재한다. 프로세스 페이지의 개수 만큼이 아닌, 페이지 프레임 하나당 페이지 테이블에 하나의 엔트리를 둔 것이다.(system-wide) 

inverted page table은 페이지 프레임에서 논리적 페이지 번호로 역순으로 주소 변환하며, 논리 주소로 물리 주소를 알아내려면(페이지 번호가 주어지면) 엔트리를 전부 검색해서 해당 페이지가 몇번째 엔트리인지 확인하여 어떤 프레임에 있는지 알 수 있다. 문제는 페이지 테이블의 원래 장점은 배열처럼 인덱스를 이용해 바로 검색할 수 있는 것인데, inverted page table은 그런 장점이 없는 것이다. 즉, 시간적인 오버헤드가 크다.

그렇다면 굳이 오버헤드도 큰 이런 방법을 왜 사용할까? 이전에 나온 개념들처럼 공간 낭비를 줄이기 위해서다.
- 프레임에 올라간 해당 페이지가 어떤 프로세스의 p번째 페이지인가? 를 알기 위해 pid같이 저장해야한다.
- 모든 엔트리를 검색하는 것은 비효율적이기 때문에 associative register 사용 (비쌈)

![images.png](./images/invert_pt2.png)

## 1.8. Shared Page
![images.png](./images/shared_p.png)
다른 프로세스들과 공유할 수 있는 페이지를 말한다. 예시로 한글 파일을 여러개 연다면, code부분은 똑같다. 해당 부분은 shared code이며 한 카피만 메모리에 올리게 되는 것이다.
### 1.8.1. Shared code
- Re-entrant Code (= Pure code)
    - 찾아보니 Re-entrant는 재 진입성이라는 뜻을 가진다.
- read-only로 프로세스 간에 하나의 code만 메모리에 올림
- 모든 프로세스의 logical address space에서 동일한 위치에 있어야 함
### 1.8.2. private code and data
- 각 프로세스들은 독자적으로 메모리에 올림
- Private data는 logical address space의 아무 곳에 와도 무방

# 2. Segmentation
## 2.1. 의미
![images.png](./images/segment.png)
세그먼트는 프로세스를 구성하는 주소 공간을 의미단위(ex.code,data,stack)으로 나누는 것이다. 보통 code, data, stack 기준으로 나누며, 작게 함수단위로도 나눌 수 있다.
## 2.2. 구성
![images.png](./images/seg_arch.png)

- 페이징과 유사한 구조
- 두 가지로 구성 <세그먼트 번호, offset(논리적 위치)>
- 각 세그먼트가 서로 다른 메모리 주소에 올라갈 수 있기떄문에, 세그먼트 별로 주소 -> 테이블 필요.
- 세그먼트 번호로 세그먼트 테이블에서 해당 세그먼트의 limit, base 확인
- base = 세그먼트 테이블 위치, length = 세그먼트의 개수
    

![images.png](./images/seg_hard.png)

- 페이징과 다르게 세그먼트는 의미단위라 크기가 일정하지 않다. 
    - limit이라는 길이(엔트리의 수)를 테이블에 가지고 있다.
    - 프레임 번호를 찾는 페이징과 다르게 물리적 메모리에 어디에서 시작해야하는지 정확한 byte단위 주소로 알려줘야한다. 
- 문제 생기는 경우
    - 세그먼트가 3개인데 세그먼트 번호가 5번이다? -> trap
    - 세그먼트의 limit이 d에서 떨어진 offset이 더 크다면? -> trap
        - 세그먼트의 길이는 offset(d)으로 표현할 수 있는 비트수 이상이 될 수 없다. [4legs님 블로그 Segmentaion](https://4legs-study.tistory.com/50)

![images.png](./images/seg.arch2.png)

세그먼트는 의미 단위로 나누다 보니, 페이징과 다른 장단점이 있다.
- 장점    
    - 페이징보다 protection 하기 쉽다.
    - 의미 단위이기 때문에 공유와 보안에 있어 paging보다 훨씬 효과적이다.
- 단점 
    - 크기가 균일하지 않아 가변분할 방식에서와 동일한 문제점들이 발생한다.
