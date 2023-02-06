13\. Thread
===
이펙티브 자바 스터디를 진행하면서 쓰레드에 대해 아는게 없음을 깨달았다. 자바의 정석을 먼저 복습하여 쓰레드를 학습해보자.

## 01 프로세스와 쓰레드
### 프로세스(process)
- 실행 중인 프로그램. 프로그램을 실행하면 OS로부터 실행에 필요한 자원을 할당받아 프로세스가 된다.
- 프로그램 수행에 필요한 데이터와 메모리 등의 자원 그리고 쓰레드로 구성되어 있다.

### 쓰레드(thread)
- 프로세스의 자원을 이용해서 실제로 작업을 수행한다.
- 모든 프로세스에는 최소한 하나 이상의 스레드가 존재한다.

## 02 멀티쓰레딩의 장단점
### 멀티쓰레딩
싱글쓰레드라면 한 번에 한 작업밖에 할 수 없다. 여러 사용자에게 서비스를 제공하는 경우 멀티쓰레딩은 필수적이다.
여러 쓰레드를 생성해서 사용자의 요청과 일대일로 처리되도록 프로그래밍 해야 한다.

프로세스를 생성하는 것은 쓰레드를 생성하는 것에 비해 시간과 메모리 공간이 필요하다.
- 동일한 작업을 하는 프로세스를 여러개 만들면 손해일 것이다.
- PCB의 모양을 기억하자. 쓰레드는 프로세스에서 공유가능한 값들은 공유하여 효율적으로 사용한다.
- 쓰레드는 동료 쓰레드와 code section, data section, OS resouces를 공유한다.

### 장점
- CPU 사용률 향상
- 자원을 보다 효율적으로 사용
- 사용자에 대한 응답성 향상
- 작업이 분리되어 코드가 간결해진다.

### 장점만 존재하진 않는다.
- 동기화, 교착상태를 고려해 신중하게 프로그래밍해야 한다.

## 03 - 05 쓰레드의 구현과 실행

### 구현
1. Thread클래스를 상속
2. Runnable 인터페이스를 구현

두 경우 모두 `run()` 메서드의 구현부를 만들어주기만 하면 된다. 
Runnable 인터페이스를 구현하는 방법은 더 높은 재사용성을 가질 수 있다.
![img.png](img.png)
- Thread는 Runnable을 구현하며, Runnable을 private 필드로 가지고 있다.  
- 기본적으로 Thread 생성자에 Runnable 인스턴스를 인수로 넘기면 필드에 저장하는 방식이다.

### 실행

```java
class ThreadEx1 {
	public static void main(String args[]) {
		ThreadEx1_1 t1 = new ThreadEx1_1();

		Runnable r  = new ThreadEx1_2();
		Thread   t2 = new Thread(r);	  // 생성자 Thread(Runnable target)

		t1.start();
		t2.start();
	}
}

class ThreadEx1_1 extends Thread {
	public void run() {
		for(int i=0; i < 5; i++) {
			System.out.println(getName()); // 조상인 Thread의 getName()을 호출
		}
	}
}

class ThreadEx1_2 implements Runnable {
	public void run() {
		for(int i=0; i < 5; i++) {
			// Thread.currentThread() - 현재 실행중인 Thread를 반환한다.
		    System.out.println(Thread.currentThread().getName());
		}
	}
}
```
- getName으로 Thread의 이름을 가져오기도 한다.
- Runnable 인스턴스를 생성자에 제공하는 것을 볼 수 있다.

### 실행 - start()
- 쓰레드를 생성하고 start()를 호출해야만 쓰레드가 실행된다.  
- **하지만** start()가 호출되었다고 해서 바로 실행되지 않는다. 
실행대기 상태에 있다가 <u>자신의 차례가 되어야 실행</u>된다. 물론 실행대기중인 쓰레드가 하나도 없으면 곧바로 실행 상태가 된다.

❗실행이 종료된 쓰레드는 다시 실행할 수 없다.
- 하나의 쓰레드에 대해 start()가 한 번만 호출될 수 있다는 의미
- 작업을 한 번 더 수행해야 한다면 새로운 쓰레드를 생성한 다음에 start()를 호출해야 한다.
- 두 번 이상 호출 시 IllegalThreadStateException 발생

## 06 start() 와 run()
- main메서드에서 run()을 호출하는 것은 생성된 쓰레드를 실행시키는게 아니라
단순히 클래스에 선언된 메서드를 호출하는 것이다.
- start()는 새로운 쓰레드가 작업을 실행하는데 필요한 호출스택을 생성한 다음에 run()을 호출해서 생성된 호출스택에 run()이 첫 번째로 올라가게 한다.

## 과정
1. main메서드에서 쓰레드의 start()를 호출
2. start()는 새로운 쓰레드를 생성, 쓰레드가 작업하는데 사용될 호출스택을 생성한다.
3. 새로 생성된 호출스택에 run()이 호출되어, 쓰레드가 독립된 공간에서 작업을 수행한다.
4. 두 호출 스택이 스케줄러가 정한 순서에 의해서 번갈아 실행된다.

## 07 main쓰레드
- main 메서드를 실행하는 쓰레드
- 프로그램을 실행하면 기본적으로 하나의 쓰레드를 생성하고, main 메서드를 호출해서 작업이 수행되도록 하는 것이다.
- 실행 중인 사용자 쓰레드가 하나도 없을 때 프로그램은 종료된다.

## 08 싱글쓰레드와 멀티쓰레드
<p style="text-align:center">
    <img src="img_1.png"/>
    <em style="display:block">자바의 정석 요약집 자료 1</em>
</p>

첫번째는 하나의 쓰레드로 두 개의 작업을 하는 경우이고, 
두번째는 두개의 쓰레드로 두 개의 작업을 수행하는 경우이다.
- 두번째의 경우, 얼핏보면 두 가지일을 동시에 처리하는 것처럼 보일 것이다. 사실 빠르게 작업 전환(context switching)이 이루어지고 있는 것이다.
- context switching에는 PC 정보 등을 저장하고 읽어 오는 시간이 소요된다. 당연히 프로세스의 스위칭이 쓰레드의 문맥 교환보다 더 무겁다. 
- 오히려 context switching 비용때문에 쓰레드가 2개인 두번째가 더 느릴 수 있다. 싱글 코어에서 단순히 CPU만을 사용하는 계산 작업이라면 오히려 싱글쓰레드가 효율적일 수 있는 것이다.


## 09 - 10 싱글쓰레드와 멀티쓰레드 예제

### 예제 코드 및 비교
```java
class Ex13_3 {
    static long startTime = 0;

    public static void main(String args[]) {
        ThreadEx13_3 th1 = new ThreadEx13_3();
        th1.start();
        startTime = System.currentTimeMillis();

        for(int i=0; i < 300; i++) {
            System.out.print("-");
        }

        System.out.print("소요시간1 :" + (System.currentTimeMillis() - Ex13_3.startTime));
    }
}

class ThreadEx13_3 extends Thread {
    public void run() {
        for(int i=0; i < 300; i++) {
            System.out.print("|");
        }

        System.out.print("소요시간2 :" + (System.currentTimeMillis() - Ex13_3.startTime));
    }
}
```
자바의 정석 예시 코드이다. 위의 코드를 실행하면 싱글코어일때와 멀티코어일때 실행 결과가 다르다.
- 싱글 코어의 경우 차례로 실행되어 쓰레드가 동시에 실행될 수 없다. "---|||---|||"와 같이 일정한 패턴을 보일 수 있다.
- 멀티 코어의 경우 동시에 실행이 가능하므로 불규칙한 패턴을 보인다.

<p style="text-align:center">
    <img src="img_2.png"/>
    <em style="display:block">자바의 정석 요약집 자료 2</em>
</p>

결국 전체적으로 비교했을때,
지금의 예제 코드 처럼 단순 출력 로직을 만든다면 속도의 차이가 다음과 같이 나타날 수 있다.

    멀티코어 2개 쓰레드 > 싱글 코어 1개 쓰레드 > 싱글 코어 2개 쓰레드

오히려 싱글코어 1개 쓰레드가 싱글코어 2개 쓰레드보다 빠른 것이다.


이미지 속에 3번째 그래프인 `멀티 코어 - 병행` 부분을 살펴보면 겹치는 부분이 존재한다. **화면이라는 자원을 놓고 두 쓰레드가 경쟁**하는 것이다.

### 불확실성에 주의하기
추가로 위 결과는 실행할 때마다 다른 결과를 얻을 수 있다. 그 이유는,
- OS의 프로세스 스케줄러의 영향을 받기 때문이다. 
- JVM의 쓰레드 스케줄러에 의해서 어떤 쓰레드가 얼마나 실행될 것인지 결정되는 것과 같이 프로세스도 프로세스 스케줄러에 의해서 실행순서와 실행시간이 결정된다.

따라서 매 순간 상황에 따라 프로세스에 할당되는 실행시간이 일정하지 않고, 쓰레드에도 마찬가지이다. 
자바가 OS(플랫폼) 독립적이라고 하지만 실제로는 OS종속적인 부분이 몇 가지 있는데 쓰레드도 그 중 하나이다.

## 11 쓰레드의 I/O 블락킹(blocking)
두 쓰레드가 **서로 다른 자원을 사용하는 작업**의 경우 싱글쓰레드 프로세스보다 **멀티쓰레드 프로세스가 더 효율적**이다.
**외부기기의 입출력을 필요로 하는 경우 유용하다.** 예시로 한 쓰레드가 사용자 입력을 기다리느라 멈춰있을때, 다른 작업을 나머지 쓰레드로 진행할 수 있다.

## 12 쓰레드의 I/O 블락킹(blocking) 예제 1
```java
class ThreadEx6 {
	public static void main(String[] args) throws Exception
	{
		String input = JOptionPane.showInputDialog("아무 값이나 입력하세요."); 
		System.out.println("입력하신 값은 " + input + "입니다.");

		for(int i=10; i > 0; i--) {
			System.out.println(i);
			try {
				Thread.sleep(1000);
			} catch(Exception e ) {}
		}
	}
}
```
입력이 먼저 존재할 때, 입력을 마치기 전에는 출력이 되지 않는다.

## 13 쓰레드의 I/O 블락킹(blocking) 예제 2
```java
class ThreadEx7 {
	public static void main(String[] args) throws Exception 	{
		ThreadEx7_1 th1 = new ThreadEx7_1();
		th1.start();

		String input = JOptionPane.showInputDialog("아무 값이나 입력하세요."); 
		System.out.println("입력하신 값은 " + input + "입니다.");
	}
}

class ThreadEx7_1 extends Thread {
	public void run() {
		for(int i=10; i > 0; i--) {
			System.out.println(i);
			try {
				sleep(1000);
			} catch(Exception e ) {}
		}
	}
}
```
쓰레드를 두 개로 나누어 처리하면 입력을 기다리면서도 출력이 계속 된다.

## 14 쓰레드의 우선순위
쓰레드는 우선순위라는 속성(멤버변수)를 가지고 있다. 이 우선순위 값에 따라 쓰레드가 얻는 실행시간이 달라진다.
예시로 파일전송기능이 있는 메신저의 경우 파일다운로드보다 채팅의 우선순위 높아야 편할 것이다. 시각적이거나 빠르게 반응해야하는 작업들은 우선순위를 보통 높게 잡아야 한다.

### 우선순위 지정하기
```Java
void setPriority(int newPriority)   // 우선순위 변경
int getPriority()                   // 우선순위 반환
```

- 쓰레드가 가질 수 있는 우선순위는 1~10이다. 숫자가 높을수록 우선순위가 높다.
- 쓰레드의 우선순위는 생성한 쓰레드로부터 상속받는다. 
  - main 메서드를 수행하는 쓰레드는 우선순위가 5이므로 main에서 생성 시 기본으로 5가 된다.