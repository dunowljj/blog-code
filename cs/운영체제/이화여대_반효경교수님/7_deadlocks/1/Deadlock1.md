Deadlock1
===
이화여자대학교 반효경 교수님의 운영체제 강의를 요약한 내용입니다. 틀린 부분이 있다면 지적해주시면 감사하겠습니다.  [강의 링크](http://www.kocw.net/home/cview.do?cid=4b9cd4c7178db077)


# 1. Deadlock
![image.png](./images/deadlock.png)
- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

![image.png](./images/deadlock_p.png)
- 테이프가 서로 한쪽식 가지고 원하는 경우
- Bianry semaphores 
- 자원 사용 단계 : 요청, 획득, 사용, 반납

## 1.1. Deadlock 발생의 4가지 조건
