CPU_Scheduling1
===
이화여자대학교 반효경 교수님의 운영체제 강의를 요약한 내용입니다. 틀린 부분이 있다면 지적해주시면 감사하겠습니다.  [강의 링크](http://www.kocw.net/home/cview.do?cid=4b9cd4c7178db077)

# 1. CPU 스케줄링이란?
CPU 스케줄링은 ready queue에 대기하는 프로세스 중 누구에게 cpu를 줄 것인가를 결정하는 작업이라고 한다.   
크게 두 가지 이슈로 보자면, **먼저,** 당장 어떤 프로세스에게 cpu를 줄 것인지를 결정해야 한다. 그리고 **특정 프로그램에 cpu를 준 다음에는** cpu를 특정 시점에 가져갈 것인지, 해당 작업을 끝낼때까지 기다릴 것인지도 결정을 해야한다.

## 1.1. CPU burst, I/O burst
<img src='./images/burst.png' height="500px" width="700px">  

**CPU burst**는 프로그램을 실행할때, cpu에서 인스트럭션을 실행하는 단계를 말한다. 그에 반해 **I/O burst**는 I/O작업을 하는 단계를 말한다. 모든 프로그램의 실행은 위와 같이 CPU burst와 I/O burst를 번갈아가는 구조이다.

아무래도 **사용자와 상호작용이 자주 있을 경우**, I/O burst가 빈번하게 생길 것이고, 짧은 CPU burst를 가지게 될 것이다. **반대로** 유전자 염시 서열 분석과 같이 **오랜 시간 연산**이 필요한 거대한 작업들은 cpu를 오래 사용한 후, I/O 작업이 가끔씩 있는 구조이므로, CPU burst가 길어질 것이다.

## 1.2. CPU bound job, I/O bound job
이러한 **CPU burst가 짧은 프로그램**을 **I/O bound job**이라고 한다. 반대로 **CPU burst가 긴 경우**는 **CPU bound job**이라고 한다. 결국에는 CPU burst가 긴 경우와 짧은 경우가 섞여있다. 그런 여러 프로그램들을 적절히 스케줄링해주는 것이 CPU scheduling이다. 

예시로 I/O bound job의 경우는 interative한 경향이 있기떄문에, cpu를 늦게 주면 응답시간이 길어질 것이다.(사람이 오래기다린다.) 그런데 CPU bound job에 대책없이 cpu를 주고 작업을 계속하게 한다면, 짧은 CPU burst만 필요한 I/O bound job이 계속해서 대기하게 될 것이다. 적절한 스케줄링이 필요한 것이다.

## 1.3. CPU Scheduler & Dispatcher
CPU Scheduler
- Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.  

Dispatcher
- CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다. (문맥 교환)
  
# 2. 스케줄링 성능 척도
1. CPU utilization(이용률))
2. Throughput(처리량)
3. Turnaround time(소요시간, 반환시간)
4. Waiting time(대기 시간)
5. Response time(응답 시간)

# 3. 대표적인 CPU Scheduling
## 3.1. FCFS
## 3.2. SJF
## 3.3. Priority Scheduling
### Exponential Averaging
## 3.4. Round Robin (RR)