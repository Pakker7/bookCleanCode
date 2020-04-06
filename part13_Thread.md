### Thread
- 자바의신


- 동작?
  - Thread를 시작하는 메소드는 start()
  - Thread가 시작하면 수행되는 메소드는 run()
  
- Runnable, Thread
  - 쓰레드 클래스가 다른 클래스를 확자앟ㄹ 피료가 있을 경우에는 Runnable 인터페이스를 구현하면 되며, 그렇지 않은 경우에는 Thread 클래스를 사용하는 것이 편하다.
  
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
    
  
  
  
  
  
