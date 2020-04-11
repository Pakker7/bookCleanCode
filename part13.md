### 동시성

#### 동시성이 필요한 이유
- 동시성에 대한 사실
  - 동시성은 항상 성능을 높여주지 않는다.
    - 대기 시간이 아주 길어 여러 스레드가 프로세서를 공유할 수 있거나, 여러 프로세서가 동시에 처리할 독립적인 계산이 충분히 많은 경우에만 성능이 높아진다
  - 동시성을 구현하면 설계가 변한다 
    - 스레드 추가시 **무엇** 과 **언제**를 분리해야한다. 그럼 설계가 달라질 수 있다
  - 웹 또는 EJB컨테이너 사용시에도 동시성 이해가 필요하다.
  - 동시성은 다소 부하를 유발
  - 동시성은 복잡하다. 간단한 문제라도 복잡함
  - 일반적으로 동시성 버그는 재현이 어렵다
    - 진짜 결함으로 간주되지 않고, 일회성으로 여겨져 넘어갈수 있다.
  - 동시성 구현은 근본적인 설계전략을 재고해야한다.
  
#### 동시성 방어 원칙 
동시성 코드가 일으키는 문제로부터 시스템을 방어하는 원칙과 기술

- 단일 책임 원칙
  - 항상 이야기 하는것..
  - 스레드는 꼭 독립적인 코드로 구현해라
- 자료 범위를 제한하라
  - 공유 객체를 사용하는 코드 내 임계영역을 synchronized 키워드로 보호할 수도 있지만.. 이런 공유하는 부분을 최대한 줄이는게 제일 좋다.
    - https://github.com/Pakker7/bookCleanCode/blob/master/part13_Thread.md#synchronized
  - 자료의 캡슐화, 공유자료 최대한 줄이기
- 자료 사본 사용하기
  - 읽기전용으로 사용
    - Collections.unmodifiableList()
    - 읽기 전용으로 만든 후에는 수정하면 예외처리
    ```java 
    public class CollectionsTest {

      public static void main(String[] argv) throws Exception { 

        try { 
          Hashtable<String, String> table = new Hashtable<String, String>(); 
          table.put("key1", "1"); 
          table.put("key2", "2"); 
          table.put("key3", "3"); 

                Map<String, String> m = Collections.unmodifiableMap(table);  // 사용 법

                System.out.println("Initial collection: " + table); 

                System.out.println("\nTrying to modify" + " the unmodifiablemap"); 
          m.put("key4", "4"); 

        } catch (UnsupportedOperationException e) { 
          System.out.println("Exception thrown : " + e); 
        } 
      } 
    }
    ```
    ```
    * 결과
    Initial collection: {key3=3, key2=2, key1=1}
    
    Trying to modify the unmodifiablemap
    Exception thrown : java.lang.UnsupportedOperationException
    ```
  - 각 쓰레드가 객체를 복사해 사용한 후 한 스레드가 해당 사본에서 결과를 가져오는 방법
  - 불변의 클래스
    - https://velog.io/@kyle/%EB%B6%88%EB%B3%80-%EA%B0%9D%EC%B2%B4%EB%9E%80-Java-Immutable-Object
- 스레드의 독립적 구현
  - 자원을 공유하지 않는다
    - ~~보통 db를 사용하므로 자원을 공유하지 않는게 힘들다..~~ 내가 잘못 이해함..
    - db는 트랜잭션이 있어서, 관련이 없다고 한다.
    - 관련이 있으려면 dirty read, http://blog.skby.net/dirty-read/

#### 라이브러리를 이해하라
- 스레드 환경에서 사용해도 안전한 컬렉션이 있다.
  - 패키지
    - java.util.concurent
    - java.util.concurrent.atomic
    - java.util.cocurrent.locks
  - ConcurrentHashMap
    - HashMap과의 차이는 key,value에 null을 허용하지 않음
    - HashTable 도 있지만, ConcurrentHashMap이 성능이 더 좋다. 
      - 이유는 HashTable은 단순하게 메서드에 sysnchronized가 붙어 있지만, 
        ConCurrentHashMap은 배열 아이템 별로 동기화처리를 하고 있기 때문 https://tomining.tistory.com/169
    - HashMap보다 ConcurrentHashMap이 더 빠르다고? 
      - 나와 같은 궁금증이 생긴 개발자 https://stackoverrun.com/ko/q/1686913
      - 성능 비교 https://j-i-y-u.tistory.com/30
  - ReentrantLock
    - **시작점과 끝점을 수동으로 설정이 가능**한 명시적인 동기화
    ```java
    public class reentrantlock {

      private final ReentrantLock locker = new ReentrantLock(); 

      public void method () { 
        locker.lock();

        try { 
          // 동기화내용들... 
        } catch (Exception e) { 

        } finally { 
          locker.unlock(); 
        }
      }
    }    
    ```
    - method
      - await() = wait()
      - signal() = notify()
      - signalAll() = notifyAll()
      ```java
      public class reentrantlock {

        private final ReentrantLock locker = new ReentrantLock(); 
        private final Condition lockerCondition = locker.newCondition();

        public void method () { 
          locker.lock();

          try { 
            // 동기화내용들... 
            lockerCondition.await();
            lockerCondition.signal();
            lockerCondition.signalAll();
          } catch (Exception e) { 

          } finally { 
            locker.unlock(); 

          }
        }
      }
      ```
  - Semaphore
    - 동시 접근 Thread 갯수 제어
  - CountDownLatch
    -  쓰레드를 N개 실행했을 때, 일정 개수의 쓰레드가 모두 끝날 때 까지 기다려야지만 다음으로 진행할 수 있거나 다른 쓰레드를 실행시킬 수 있는 경우 사용

#### 실행 모델을 이해하라
- 스레드 환경의 실행모델
  - 한정된 자원 (Bound Resource)
    - 다중 스레드 환경에서 사용하는 자원으로, 크기나 숫자가 제한적이다. 데이터베이스 연결, 길이가 일정한 읽기/쓰기 버퍼 등이 예다.
  - 상호 배제 (MutualExclusion)
    - 한 번에 한 스레드만 공유 자료나 공유 자원을 사용할 수 있는 경우를 가리킨다.
  - 기아(Starvation) 
    - 한 스레드나 여러 스레드가 굉장히 오랫동안 혹은 영원히 자원을 기다린다. 예를 들어, 항상 짧은 스레드에게 우선순위를 준다면, 짧은 스레드가 지속적
으로 이어질 경우, 긴 스레드가 기아 상태에 빠진다.
  - 데드락 (Deadlock) 
    - 여러 스레드가 서로가 끝나기를 기다린다. 모든 스레드가 각기 필요한 자원을 다른 스레드가 점유하는 바람에 어느 쪽도 더 이상 진행하지 못한다.
    - ex. 크롬 사용중에 프로그램 강제종료, 프로그램 설치과정 (다른 프로그램은 모두 꺼주세요..) 등
    - 발생 조건
      - **이 4가지조건이 모두 만족되야 교착상태가 일어남**
      - 상호 배재
      - 점유 상태로대기
      - 선점 불가
        - 선점와 비선점이 있음
      - 순환성 대기
  - 라이브락(Livelock) 
    - 락을 거는 단계에서 각 스레드가 서로를 방해한다. 스레드는 계속해서 진행하려 하지만, 공명(resonanoe》으로 인해, 굉장히 오랫동안 혹은 영원히 진행
하지 못한다.

- 실제 실행 모델에서 문제가 생기는 순서.. 
  - 생산자-소비자
    - 생산자, 소비자, 둘을 잇는 대기열(한정된 자원)
      - 1. 생산자 스레드는 대기열에 빈공간이 있는지 확인 후 비었으면 채운다.
      - 2. 생산자 스레드가 소비자 스레드에게 시그널을 보낸다.
      - 3. 소비자 스레드는 대기열에 빈 공간이 있다는 시그널을 보낸다.
    - 잘못하면 둘다 시그널만 보내면서 기다리다가 멈춰버림....
  - 읽기-쓰기
    - 읽기 쓰레드는 공유자원 사용, 쓰기 쓰레드가 공유자원을 갱신한다 라고 했을 때
    - 처리율의 문제
      - 처리율 강조하면 기아 현상이 생김
      - 갱신 허용하면 처리율에 영향을 미침
    - 읽기 스레드가 없을 때까지 갱신을 원하는 쓰기 스레드가 버퍼를 기다리는 방법 -> 하지만.. 쓰기 스레드에게 우선권을 준 상태에서 쓰기 스레드가 계속 이어진다면 처리율이 떨어짐..
  - 식사하는 철학자들
    - ex. 둥근식탁에 철학자 한무리, 각 철학자 왼쪽에는 포크가 놓여있다. 식탁 가운데는 커다란 스파게티 한 접시가 놓였다. 철학자들은 배가 고프지 않으면 생각하며 시간을 보낸다. 배가고프면 양손에 포크를 들고 스파게티 먹는다. 양손에 포크 없으면 스파게티를 못먹음..
    - 철학자 : 스레드 / 포크 : 자원 ..
 
- 동기화 하는 메서드 사이에 의존성이 존재하면 동시성 코드에 찾아내기 어려운 버그가 생긴다. (synchronized 사용)
  - **https://pjh3749.tistory.com/268** 
  - 어려운 버그는 알겠으나, 어떻게 해야할지는 아직 잘 와닿지 않는다.. 뭐 이해는 되는데... 아직 써보지도 않아서 어떻게...

- 공유 객체에서 여러 메소드 필요한 경우 방법
  - 1. 클라이언트에서 잠금 - 클라이언트에서 첫 번째 메서드를호출하기 전에 서버를 잠근다. 마지막 메서드를 호출할 때까지 잠금을 유지한다.
  - 2. 서버 에서 잠금 - 서버 에다 *"서버를 잠그고 모든 메서드를 호출한 후 잠금을 해제하는”* 메서드를 구현한다. 클라이언트는 이 메서드를 호출한다.
  - 3. 연결Adapted 서버 - 잠금을 수행하는 중간 단계를 생성한다. ‘서버에서 잠금’방식과 유사하지만 원래 서버는 변경하지 않는다.

#### 동기화 하는 부분을 작게 만들어라
#### 올바른 종료 코드는 구현하기 어렵다.
#### 스레드 코드 테스트 하기
- ? 스프링은 원래 사람들의 요청을 멀티 스레드로 자동으로 해준다고 함.. 전혀 고려하지 않았는데 이제 알았네......
- 말이 안되는 실패는 잠정적인 스레드 문제로 취급하라
- 다중 스레드를 고려하지 않은 순차 코드부터 제대로 돌게 만들자 

- ? 스레드를 많이 생성 한다고 해서 좋은게 아니다 : 스레싱, http://blog.skby.net/%EC%8A%A4%EB%A0%88%EC%8B%B1-thrashing/

#### 결론
최우선적으로 SRP를 숙지하라. 
시스템을 최대한 POJO단위로 잘라 스레드 관련 코드와 非 스레드 관련 코드를 나누어라. 
스레드 관련 코드를 테스트할 때에는 그 이외의 것들은 제외하고 스레드 관련 문제만 테스트하라. 
이는 스레드 관련 문제가 최대한 작은 부분에 집중되게 한다.
하지만 문제는 항상 생긴다
