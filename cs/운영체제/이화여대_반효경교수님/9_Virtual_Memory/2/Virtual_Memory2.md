Virtual Memory 2
===
이화여자대학교 반효경 교수님의 운영체제 강의를 요약한 내용입니다. 틀린 부분이 있다면 지적해주시면 감사하겠습니다. [강의 링크](http://www.kocw.net/home/cview.do?cid=4b9cd4c7178db077)

# 1. 다양한 캐슁 환경
## 1.1. 캐슁 기법
- 한정된 빠른 공간(=캐쉬)에 요청된 데이터를 저장해 두었다가 후속 요청시 캐쉬로부터 직접 서비스하는 방식
    - 느린 저장장치까지 갈 필요가 없다.
    - 디스크(더 느림)와 물리메모리(빠름)
- paging sysytem 외에도 
    - cache memory(cpu가 메인메모리 접근 시에 사이에 캐시메모리 확인)
    - buffer caching : 파일시스템에 대한 read/write 요청을 메모리에서 빠르게 서비스하는 방법
    - Web caching : 읽어온 웹 페이지를 저장했다가 보여주기.
등 다양한 분야에서 사용
## 1.2. 캐쉬 운영의 시간 제약
- replacemnt 알고리즘 : 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실세 시스템에서 ㅏㅅ용할 수 없음
- Buffer caching이나 Web caching의 경우(=대부분의 경우)
    - O(1) 에서 O(log n) 정도까지 허용
- Paging system인 경우
    - page fault인 경우에만 OS가 관여함
    - 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음
    - O(1)인 LRU의 list조작조차 불가능
## 1.3. LRU,, LFU 알고리즘의 구현의 경우
### LRU 구현
- 세로로 뻗은 LinkedList 생각, 제일 위에가 오래전에 참조된 페이지.
- 제일 위에 있는 페이지 쫓아내면 됨.
- 메모리 내에서 다시 참조되면 빼서 맨 아래로 이동시켜준다.
- O(1) complexity
### LFU 구현
- 한줄로 줄세우기 생각해보기. 맨 위에는 참조횟수 적은 페이지.
- 맨 위에 쫓아내기
- 메모리 내에서 다시 참조되면, 어디까지 내려갈 수 있는지 직접 비교해야 한다. 최악의 경우 O(n) complexity
+ 헤결 : heap에 구현, O(log n) complexity
## 1.4. Paging System에서 LRU, LFU 가능한가?  
- 애초에 주소변환은 하드웨어적으로 해결한다.
- invalid -> page fault 발생 -> I/O필요, trap -> OS개입 : 해당 경우만 운영체제가 개입한다.
### 1.4.1. 접근 시간, 참조횟수를 알 수 있는가?
- 운영체제는 메모리에서 참조횟수가 제일 적은 페이지를 쫓아내야하는데, 찾을 수 있는가?
    - 불가하다. 해당 프로세스에서 요청한 페이지가 이미 메모리에있다면, 운영체제에 cpu가 넘어가지 않고, 하드웨어적으로 주소변환해서 cpu를 읽어들인다. 접근 시간을 알 수 없다.
- 반대로 page fault 발생 시, 디스크를 접근하면서 운영체제로 cpu가 넘어가므로, 디스크의 페이지가 메모리에 올라온 시간을 알 수 있다.
- page fault시에만 운영체제에게 정보가 주어지기 때문에 결국에는 반쪽짜리 정보만 얻게 된다.
### 1.4.2. LRU 구조
- page fault에 의해 운영체제가 개입하므로 LinkedList에 새로 들어온 것을 매달아줄 수 있다. 
- 하지만  다시 참조되면, LinkedList를 끊어서 옮기는 작업을 할 수 없다. cpu가 운영체제로 넘어가지 않기 떄문이다.
### 1.4.3. 결론
- LRU, LFU는 사실 Pagaing system에서는 사용 못함.
- buffer caching, web caching 등에선 사용 가능.

# 2. Clock Algorithm
## 2.1. 설명
![images.png](./images/clock_algo.png)
- LRU의 근사(approximation) 알고리즘
    - 적어도 시계가 한바퀴 돌때까지 참조 안된 페이지를 쫓아낼 수 있다. 어느정도 유사 효과.
- Second chance algorithm, NUR , NRU(Not Recently Used)
- 하드웨어가 페이지들을 참조할때, reference bit을 1로 세팅,
- 교체 대상 페이지 선정(circular list). 0 찾을때까지 이동
- 운영체제는 이동하면서 1을 0으로 바꾸고, 0인 것을 발견 시 교체. 
- LRU처럼 가장 오래된 것을 쫓아내지는 못한다.
- 한 바퀴 되돌아와서도(=second chance)0 이면 replace
- 자주 사용되는 페이지라면 second chance가 올 때 1  
- LinkedList에서 페이지를 이동시키는 일 등은 운영체제가 못하기 떄문에 하드웨어가 bit를 세팅해준다.
### 2.2. 개선
- reference bit과 modified bit (dirty bit)을 함께 사용
- reference bit = 1 : 최근에 참조된 페이지
- modified bit = 1 : 최근에 변경된 페이지 (I/O를 동반하는 페이지)
    - modified bit = 0 인 경우, 디스크에서 올린 후 write 발생한 적이 없다. 쫓아낼때 그저 삭제.
    - modified bit =1 적어도 한번은 수정한 페이지. backing store에 반영 후 지워야 한다.

# 3. Page Frame의 Allocation
- 사실상 프로그램마다 페이지를 나누어 메모리에 올리기떄문에, 메모리에 다양한 프로그램의 페이지들이 존재한다. 여태 그것들을 그저 오래된 순서로 처리하는 등의 방식을 사용했다. **하지만 실제로는** 프로그램이 같이 올라와있어야 더 효율적이다.
## 3.1. 필요성
![images.png](./images/pf_allo.png)
- 명령어, 데이터 등 여러 페이지 동시 참조하는 경우
- 3개의 페이지가 특정 for문을 구성한다면? -> 3개의 페이지가 모두 있으면 for문이 계속 잘 돌아간다. 한꺼번에 allocate 되는 것이 유리함.
## 3.2. Allocation Scheme
- 특정 프로그램이 메모리를 전부 점유한다면?
### 3.2.1. Eqaul allocation
- 모든 프로세스에 똑같은 갯수 할당 -> 프로그램마다 필요한 페이지양이 다르다.
### 3.2.2. Proportional allocation
- 프로세스 크기에 비례하여 할당  -> 같은 프로그램도 시간에 따라 필요한 페이지 갯수가 다를 수 있다.
### 3.2.3. Priority allocation 
- 프로세스의 priority에 따라 다르게 할당

# 3.3. Global vs. local  Replacement
![images.png](./images/glo_loc.png)
## Global replacement
- 굳이 미리 할당하지 않고, 알아서 알고리즘을 사용하면 그때그때 프로세스별로 할당량이 조절된다.
## Local replacement
- 메모리 할당을 한다면, 각각의 프로그램에 메모리가 주어짐.
- 할당된 frame 내에서만 replacement

# 4. Thrashing
 - 어떤 프로그램의 어느정도의 최소 메모리가 할당조차안되면, page fault가 자주 일어난다.
 - 최소한의 page frame 할당이 안돼서 page fault가 지나치게 많이 일어나는 경우
 3644