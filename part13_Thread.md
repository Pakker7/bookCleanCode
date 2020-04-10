### Thread
- 자바의신

#### 기본 동작
- 동작?
  - Thread를 시작하는 메소드는 start()
  - Thread가 시작하면 수행되는 메소드는 run()
  
- Runnable, Thread
  - 쓰레드 클래스가 다른 클래스를 확장할 필요가 있을 경우에는 Runnable 인터페이스를 구현하면 되며, 그렇지 않은 경우에는 Thread 클래스를 사용하는 것이 편하다.
  
  ```java
  public class RunnableSample implements Runnable {

    @Override
    public void run() {
      System.out.println("Runnable's run() method");
    }
  }
  ```
  ```java
  public class ThreadSample extends Thread{
    public void run() {
      System.out.println("ThreadSample's run() method");
    }
  }
  ```
  ```java
  public class RunThreads {
    public void runBasic() {
      RunnableSample runnable = new RunnableSample();
      ThreadSample thread = new ThreadSample();

      new Thread(runnable).start();
      thread.start();
      System.out.println("RunThreads.runBasic() method is ended.");

    }
      public static void main(String[] args) {
        RunThreads threads = new RunThreads();
        threads.runBasic();
      }
  }
  ```
  ```
  * 결과
  RunThreads.runBasic() method is ended.
  ThreadSample's run() method
  Runnable's run() method
  ```
- 순서는 계속 바뀜 이유? 
  - 프로세스가 아닌 하나의 쓰레드를 JVM에 추가 하여 실행한다는 것. 
  - 순차 실행이 아니라 동시 실행 이기 때문
  
- Thread 예제
  - 경마 게임
  
  ```java
  public class HorseRacing extends Thread{
    private int horseTotalNum;

    public HorseRacing() {}

    public HorseRacing(int horseTotalNum) {
      this.horseTotalNum = horseTotalNum;
    }

    public void run() {
      racing(Integer.toString(horseTotalNum));
    }

    public void racing(String name) {

      try {
        Random random = new Random();
        int sleepTime = random.nextInt(1000);

        sleep(sleepTime);
        System.out.println("[" + name + "] 말 도착!");

      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }

  }
  ```
  ```java
  public class Game {

    public static void main(String[] args) {

      int horseTotalCnt = 10;

      System.out.println("총 [" +horseTotalCnt+"] 마리의 말이 출발합니다.");

      for (int horseNum = 1; horseNum <= horseTotalCnt; horseNum++) {
        HorseRacing horseRacing = new HorseRacing(horseNum);
        horseRacing.start();
      }

    }
  }
  ```
  
#### synchronized
- 사용 이유 
  - 안전하게 하기 위해..?
  - 
- 사용 방법
  - 메소드 자체를 synchronized로 선언
  - 메소드 내의 특정 문장만 synchronized로 감싸기

##### 메소드 자체를 synchronized로 선언
  - 여러 쓰레드가 한 객체에 선언된 메소드에 접근하여 데이터를 처리하려고 할 때 동시에 연산을 수행하여 값이 꼬이는 경우가 발생할 수 있음
  - 당연히 매개변수,메소드에서 사용하는 지역변수는 문제가 없지만, 공유하는 인스턴스 변수를 수정할 시 문제가 됨
  
  ```java
  public class CommonCalculate {

    private int amount;

    public CommonCalculate() {
      amount = 0;
    }

    public void plus(int value) {
      amount += value;
    }

    public void minus(int value) {
      amount -=value;
    }

    public int getAmount() {
      return amount;
    }
  }
  ```
  ```java
  public class ModifyAmountThread extends Thread{
	
    private CommonCalculate calc;
    private Boolean addFlag;

    public ModifyAmountThread() {
    }

    public ModifyAmountThread(CommonCalculate calc, Boolean addFlag) {
      this.calc = calc;
      this.addFlag = addFlag;
    }

    @Override
    public void run() {

      for (int i = 0; i < 10000; i++) {
        if(addFlag) {
          calc.plus(1);
        } else {
          calc.minus(1);
        }

      }
    }
  }
  ```
  ```java
  public class RunSync {

    public static void main(String[] args) {
      RunSync runSync = new RunSync();
      runSync.runCommonCalculate();
    }

    public void runCommonCalculate() {
      CommonCalculate calc = new CommonCalculate();
      ModifyAmountThread thread1 = new ModifyAmountThread(calc, true);
      ModifyAmountThread thread2 = new ModifyAmountThread(calc, true);

      thread1.start();
      thread2.start();

      try {
        thread1.join(); // thread 종료될때까지 기다림
        thread2.join();
        System.out.println("final value is " + calc.getAmount());

      } catch (Exception e) {
        e.printStackTrace();

      }
    }
  }
  ```
  ```
  * 실행 결과 
  final value is 12913... // 20000 이 안나옴..
  ```
  - thread1이 도는 동시에 thread2 가 돌기 때문에 예상되는 결과와 다르게 나옴... 이럴 때 이렇게 synchronized를 붙이면 됨
  ```java
  public synchronized void plus(int value) {
		amount += value;
	}
  ```
  
##### 메소드 내의 특정 문장만 synchronized로 감싸기
- 메소드자제에 synchronized를 쓰면 효율성이 떨어질 수 있다.
  - ex. 메소드가 30줄이라 가정 했을 때 amount 변수를 딱 한줄에서만 다룬다면? 이 부분만 하는게 효율적임
  ```java
	Object lock = new Object();
	public void plus(int value) {
		synchronized (lock) {		// 잠금용 객체
						// 하나의 쓰레드만 일을 할 수 있도록 허용
			amount += value;	// 중괄호 내에 있는 연산만 동시에 여러쓰레드에서 처리하지 않겠다는 의미
		}
	}

	public void minus(int value) {
		synchronized (lock) {
			amount -=value;
		}
	}
  ```
  - 근데 lock 이라는 객체를 여러번 곳에 사용하면, 다른 변수임에도 불구하고 다같이 끝날 때 까지 기다려야함..  그래서 각자 사용하는 곳마다 객체를 만들어서 쓰는게 좋음
  

##### 쉽게 하는 실수
- synchronized 시에 calc라는 같은 객체를 참조했다. 이때에만 synchronized가 유효하다.
- 다른 객체 참조할 때는 synchronized를 안써도 되겠지?
```java
CommonCalculate calc = new CommonCalculate();
ModifyAmountThread thread1 = new ModifyAmountThread(calc, true);
ModifyAmountThread thread2 = new ModifyAmountThread(calc, true);
```

##### 쓰레드를 통제하는 메소드
- Thread.State(enum 클래스)
  -  NEW : 쓰레드 객체는 생성 되었지만, 아직 시작되지는 않은 상태
  - RUNNABLE : 쓰레드가 실행중인 상태
  - BLOCKED : 쓰레드가 실행 중지 상태이며 모니터락이 풀리기를 기다리는 상태
  - WATTING : 쓰레드가 대기중인 상태
  - TIMED_WATTING : 특정시간만큼 쓰레드가 대기중인 상태
  - TERMINATED : 쓰레드가 종료된 상태

- 메서드
  - join() 
    - 수행중인 스레드 중지 시까지 기다림
  - wait()
    - 다른 쓰레드가 Object 객체에 대한 nofity() 메소드나 notifyAll() 메소드를 호출할 때까지 현재 쓰레드가 대기하고 있도록 한다.
  - notify()
    - Object 객체의 모니터에 대기하고 있는 단일 쓰레드를 깨운다
  - notifyAll()
    - Object 객체의 모니터에 대기하고 있는 모든 쓰레드를 깨운다.
  - 모니터?
    - lock 객체와 같이 쓰레드가 안전하게 수행하도록 도와주는 객체
