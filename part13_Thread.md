### Thread
- 자바의신 및 여러 인터넷 블로그

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
