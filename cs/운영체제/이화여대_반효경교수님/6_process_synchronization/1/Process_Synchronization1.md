Process_Synchronization1
===
이화여자대학교 반효경 교수님의 운영체제 강의를 요약한 내용입니다. 틀린 부분이 있다면 지적해주시면 감사하겠습니다.  [강의 링크](http://www.kocw.net/home/cview.do?cid=4b9cd4c7178db077)

# 1. Synchronization의 필요
## 1.1. 데이터의 접근
<img src='./images/data_acess.png' height="600px" width="850px">  

- 저장소를 Storage, 연산하는 곳을 Execution이라는 이름으로 임의로 지정
- 데이터 저장된 곳에서 연산할 데이터를 받아서 다시 결과를 전달해줌
    - 누가 먼저 읽어왔는가에 따라 결과가 달라질 수 있다.
    - process_sychronization이 이슈가 되는 이유
## 1.2. Race_Condition(경쟁 상태)
동시에 접근해서 생기는 문제들
- 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
- 데이터의 최종 연산 결과가 마지막에 그 데이터를 다룬 프로세스에 따라 달라진다.
### 여러 연산 박스가 저장 박스를 공유한다면?
<img src='./images/race_condition.png' height="600px" width="850px">  

- 왼쪽 박스는 1을 더하는 연산, 오른쪽 박스는 1빼는 연산
- 왼쪽에서 데이터를 읽어가서 하나 증가시키는 동안, 오른쪽에서 데이터를 읽어간다면?
    - 더하기 전의 데이터를 읽어서 빼서 돌려줬기떄문에, 빼기밖에 반영이 안된다.

### CPU가 여러 개라면?
<img src='./images/race_condition2.png' height="600px" width="850px">  

- race condition 발생 가능 경우들
    - 시스템콜로 커널의 코드가 실행 중(커널의 데이터 접근, 변수 변경)인 상황에서 cpu를 뺏겨서 또 다른 프로세스한테 cpu가 넘어갔는데 또 시스템콜을 해서 커널의 코드가 또 실행 
    - 커널의 코드 실행중에 인터럽트가 들어옴. 인터럽트 처리 코드도 커널의 코드

# 2. OS에서의 race condition
## 2.1. 커널 수행 중 인터럽트 발생
<img src='./images/racon_inOS1.png' height="600px" width="850px">  

- 커널이 cpu에서 실행하면서 count의 값을 증가시킨다. 보통 여러 개의 인스트럭션으로 실행된다.
    - 1 메모리에 변수 값을 cpu의 레지스터로 불러들임
    - 2 증가시킴
    - 3 다시 메모리에 변수위치에 가져다 씀
- 위 과정 중 중간(읽어들인 상태)에 인터럽트가 들어오면 인터럽트 처리 루틴이 수행됨
- 인터럽트 처리 루틴도 커널 코드라서 주소를 공유한다.
+ count++ 과정동안 인터럽트를 차단하는 방식으로 해결

## 2.2. 프로세스가 시스템콜로 커널모드일때, 문맥교환이 일어나는 경우
<img src='./images/kernel_racon.png' height="600px" width="850px">  

- user mode - kernel mode 반복
- 시스템콜을 해서 kernel mode일때, 값을 변경 중에 본인이 **할당시간이 끝나서** cpu가 B한테 넘어간 경우. B에서 증가시킨 내용은 무시된다.
- 해결 : 할당시간이 끝나도 cpu를 빼앗지않도록 함. 할당시간이 편차가 생길 수 있다. 하지만 real-time이 아닌 time-sharing시스템의 경우 큰 문제 없다.

## 2.3. Multiprocessor에서 shared memory 내의 kernel data
<img src='./images/multipro_racon.png' height="600px" width="850px">  

- 커널 전체 락/언락하기 : 한번에 하나의 커널만 들어갈 수 있게 하는 방법
- 해당 데이터 락/언락하기 : 데이터를 가져가기전에 lock을 걸고, 처리 후 unlock

## 2.4. Process Synchronization 문제
- 공유 데이터에 동시 접근은 데이터의 불일치를 발생시킬 수 있다.
- 일관성 유지를 위해 협력 프로세스 간의 실행 순서를 정해주는 메커니즘 필요
- race condition을 막기 위해 concurrent process는 동기화가 잘되어야 함


# 3. The Critical_Section Problem (임계구역)
<img src='./images/critical_section.png' height="600px" width="850px">  

- n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 공유 데이터를 접근하는 코드 : critical section

# 4. Critical_Section 해결을 위한 Algorithm (소프트웨어적 해결)
<img src='./images/critical_solve.png' height="600px" width="850px">  

- 크게 critical section인 코드와 그렇지 않은 코드로 구분 가능하다. (공유 데이터에 접근하거나 그렇지 않거나)
- critical section에 동시에 여러 프로세스가 들어가지 못하게 하기 위해 entry section에서 lock, exit section에서 unlock과 같이 해결 가능.

## 3가지 조건
### Mutual Exclution(상호 배제)
- 어떤 프로세스가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 각자의 critical section에 들어가면 안 된다.
### Progress(진행)
- 아무도 critical section에 없으면 critical section에 들어가고자하면 통과시켜줘야한다.
### Bounded Waiting(유한 대기)
- 다른 프로세스들끼리 독점하여 생기는 Starvation 방지. 프로세스가 critical section에 들어가려는 요청을 한 후 해당 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야한다.
### 가정
- 모든 프로세스의 수행 속도는 0보다 크고, 프로세스들 간의 상대적인 수행속도는 가정하지 않을때.

## 4.1. 알고리즘 1
<img src='./images/critical_section_algorithm1.png' height="600px" width="850px">  

- P0은 turn이 0일떄 critical section 들어갈 수 있다는 의미이다.
- while문을 순회하다가, 자신의 turn이 되면 critical section에 들어간다.
- critical section에 들어갔다 나오면서 다른 프로세스의 turn으로 바꾸어준다.
+ 문제 : turn을 다른 프로세스가 바꿔주지 않으면 들어갈 수 없다. 극단적인 경우 영원히 들어갈 수 없다.
+ **Mutual Exclution** 는 만족하지만 **Progress**를 만족 못시킨다. 

## 4.2. 알고리즘 2
<img src='./images/critical_section_algorithm2.png' height="600px" width="850px">  

- 프로세스마다 자신의 flag를 가진다. flag는 자신이 critical section에 들어가고자 하는지 표현한다. 처음엔 false이다.
- 들어가고자 할때 flag를 true로 바꾸고, 상대방의 flag를 체크한다. 서로 깃발을 들어준다.
+ 문제 : 자신의 깃발을 들은 상태에서 cpu를 빼앗기면, cpu를 가져간 프로세스도 깃발을 들어버린다. 가져간 프로세스는 깃발이 들린것을 보고 critical section에 들어가지 않고 기다린다. 그러다가 cpu가 넘어가고 상대방도 기다린다.
+ **Mutual Exclution** 는 만족하지만 **Progress**를 만족 못시킨다.

## 4.3. 알고리즘 3
<img src='./images/critical_section_algorithm3.png' height="600px" width="850px">  

- turn과 flag 모두 사용
- 들어가고자 할때 깃발을 들고 turn을 상대방에게 준다.
- 상대방이 깃발을 들고 있고, turn도 가지고 있을 경우 기다린다.
- 하나라도 아니면 critical section에 들어간다.
+ **Mutual Excultion** , **Progress**, **Bounded Waiting** 모두 만족
+ 문제 : Busy Waiting(=spin lock)!
    + while문을 계속 돌면서 상대가 못들어가게 막는다. 본인의 cpu 할당시간에 critical section이 비워지려면 멀었는데 기다리느라 낭비하는 경우.


# 5. Synchronization Hardware (하드웨어적 해결))
<img src='./images/synchro_hardware.png' height="600px" width="850px">  
- 인스트럭션 실행 도중에 cpu가 뺏기진 않기 때문에, 인스트럭션 하나로 데이터를 읽고 쓰는 작업을 동시에 실행할 수 있다면, lock 문제를 간단하게 해결할 수 있다.
    - 위의 예시 Test_and_set(a) : 두 가지 작업을 atomic하게 수행
    - 코드부분을 보면 while(Test_and_Set(lock)); 볼 수 있음.
