# 클래스

### 깨끗한 클래스

- 객체지향 5대 원칙
  - SRP(단일 책임 원칙) : 소프트웨어의 설계 부품(클래스, 함수 등)은 단 하나의 책임만을 가져야 한다.
  - OCP(개방-폐쇄 원칙) : 기존의 코드를 변경하지 않고(Closed) 기능을 수정하거나 추가할 수 있도록(Open) 설계해야 한다.
  - LSP(리스코프 치환 원칙) : 자식 클래스는 부모클래스에서 가능한 행위를 수행할 수 있어야 한다.
  - DIP(의존 역전 원칙) : 의존 관계를 맺을 때, 변화하기 쉬운 것 보단 변화하기 어려운 것에 의존해야 한다는 원칙이다. 
  - ISP(인터페이스 분리 원칙): 한 클래스는 자신이 사용하지 않는 인터페이스는 구현하지 말아야 한다. 하나의 일반적인 인터페이스보다는, 여러 개의 구체적인 인터페이스가 낫다
을 말하며, 앞자를 따서 SOILD 원칙

클래스 체계
- 클래스 안의 순서
  - 정적 공개 상수 static public 
  - 정적 비공개 변수 static private
  - 비공개 인스턴스 변수
  - 공개 변수가 필요한 경우는 거의 없다 ->?

변수 목록 다음에는 공개함수가 나온다.
비공개(private) 함수는 자신을 호출하는 공개함수 직후에 넣는다..?
 
접근제어자 반환타입 메소드이름(매개변수목록) { // 선언부
    // 구현부
}

### 캡슐화
캡슐화의 정의를 보면 필요한 속성(Attribute) 와 행위(Method) 를 하나로 묶는 작업.
+ 클래스의 담는 내용중 중요한 데이터나 기능을 외부에서 접근하지 못하게 하는 은닉성이라는게 있음

-접근지정자
public 공개 / protected 동일패키지, 상속 클래스/default 동일패키지/ private 동일 클래스
(js에서는 함수명 앞에 _붙인다고함)

단일책임원칙

### 응집도
결합도는 낮게, 응집도는 높게

- 결합도?
  - 한 모듈과 다른 모듈 간의 상호 의존도 또는 연관 관계이다.
낮은 결합도를 가진 프로그램 코드는 한 모듈 내의 에러가 다른 모듈에 영향을 미치는 파급효과의 최소화가 가능하며, 한 모듈의 변경이 다른 모듈에 큰 영향을 미치지 않고 모듈의 유지 보수 및 변경이 가능하다. 또한 특정 모듈의 내부 사항을 자세히 알지 못해도 그와 관련된 다른 모듈의 효과적 취급이 가능

- 응집도?
  - 한 모듈 내에 존재하는 함수, 데이터 등의 구성 요소들 사이의 밀접한 정도
  - 응집도가 높은 모듈은 하나의 모듈 안에 필요한 함수나 데이터와 같은 구성 요소들이 똘똘 뭉쳐서 존재한다. 그래서 프로그램 전체에서 담당하는 기능을 수행하는 것에 있어서는 다른 모듈과의 상호작용이 거의 없이도 단일 기능을 수행할 수 있게 된다. 정보 은닉과도 관계.

메서드가 변수를 많이 사용할수록 메서드와 클래스는 응집도가 높다

- 응집도를 유지하면 작은 클래스 여럿이 나온다
  - 변수가 많은 큰 함수 하나가 있다. 큰 함수 일부를 작은 함수 하나로 빼내고 싶은데，빼내려는 코드가 큰 함수에 정의된 변수 넷을 사용한다. 변수 네 개를 새 함수에 인수로 넘겨야 옳을까? 전혀 아니다! 만약 네 변수를 클래스 인스턴스 변수로 승격한다면 새 함수는 인수가 필요 없다. 그만큼 함수를 쪼개기 쉬워진다.
그러나 이렇게 하면 클래스가 응집력을 잃는다 

- 이유
  - 몇몇 함수만 사용하는 인스턴스 변수가 점점 더 늘어나기 때문이다. 
- 해결방법 
  - 다른 클래스로 분리(몇몇 함수가 몇몇 변수만 사용하니까)

- 예시 

정처기 알고리즘 같기도하고..

```
public class PrintPrimes {
    public static void main(String[] args) {
        final int M = 1000; 
        final int RR = 50;
        final int CC = 4;
        final int WW = 10;
        final int ORDMAX = 30; 
        int P[] = new int[M + 1]; 
        int PAGENUMBER;
        int PAGEOFFSET; 
        int ROWOFFSET; 
        int C;
        int J;
        int K;
        boolean JPRIME;
        int ORD;
        int SQUARE;
        int N;
        int MULT[] = new int[ORDMAX + 1];

        J = 1;
        K = 1; 
        P[1] = 2; 
        ORD = 2; 
        SQUARE = 9;

        while (K < M) { 
            do {
                J = J + 2;
                if (J == SQUARE) {
                    ORD = ORD + 1;
                    SQUARE = P[ORD] * P[ORD]; 
                    MULT[ORD - 1] = J;
                }
                N = 2;
                JPRIME = true;
                while (N < ORD && JPRIME) {
                    while (MULT[N] < J)
                        MULT[N] = MULT[N] + P[N] + P[N];
                    if (MULT[N] == J) 
                        JPRIME = false;
                    N = N + 1; 
                }
            } while (!JPRIME); 
            K = K + 1;
            P[K] = J;
        } 
        {
            PAGENUMBER = 1; 
            PAGEOFFSET = 1;
            while (PAGEOFFSET <= M) {
                System.out.println("The First " + M + " Prime Numbers --- Page " + PAGENUMBER);
                System.out.println("");
                for (ROWOFFSET = PAGEOFFSET; ROWOFFSET < PAGEOFFSET + RR; ROWOFFSET++) {
                    for (C = 0; C < CC;C++)
                        if (ROWOFFSET + C * RR <= M)
                            System.out.format("%10d", P[ROWOFFSET + C * RR]); 
                    System.out.println("");
                }
                System.out.println("\f"); PAGENUMBER = PAGENUMBER + 1; PAGEOFFSET = PAGEOFFSET + RR * CC;
            }
        }
    }
}
```

- 바뀐 함수
```
public class PrimePrinter {
    public static void main(String[] args) {
        final int NUMBER_OF_PRIMES = 1000;
        int[] primes = PrimeGenerator.generate(NUMBER_OF_PRIMES);

        final int ROWS_PER_PAGE = 50; 
        final int COLUMNS_PER_PAGE = 4; 
        RowColumnPagePrinter tablePrinter = 
            new RowColumnPagePrinter(ROWS_PER_PAGE, 
                        COLUMNS_PER_PAGE, 
                        "The First " + NUMBER_OF_PRIMES + " Prime Numbers");
        tablePrinter.print(primes); 
    }
}
```
```
public class RowColumnPagePrinter { 
    private int rowsPerPage;
    private int columnsPerPage; 
    private int numbersPerPage; 
    private String pageHeader; 
    private PrintStream printStream;

    public RowColumnPagePrinter(int rowsPerPage, int columnsPerPage, String pageHeader) { 
        this.rowsPerPage = rowsPerPage;
        this.columnsPerPage = columnsPerPage; 
        this.pageHeader = pageHeader;
        numbersPerPage = rowsPerPage * columnsPerPage; 
        printStream = System.out;
    }

    public void print(int data[]) { 
        int pageNumber = 1;
        for (int firstIndexOnPage = 0 ; 
            firstIndexOnPage < data.length ; 
            firstIndexOnPage += numbersPerPage) { 
            int lastIndexOnPage =  Math.min(firstIndexOnPage + numbersPerPage - 1, data.length - 1);
            printPageHeader(pageHeader, pageNumber); 
            printPage(firstIndexOnPage, lastIndexOnPage, data); 
            printStream.println("\f");
            pageNumber++;
        } 
    }

    private void printPage(int firstIndexOnPage, int lastIndexOnPage, int[] data) { 
        int firstIndexOfLastRowOnPage =
        firstIndexOnPage + rowsPerPage - 1;
        for (int firstIndexInRow = firstIndexOnPage ; 
            firstIndexInRow <= firstIndexOfLastRowOnPage ;
            firstIndexInRow++) { 
            printRow(firstIndexInRow, lastIndexOnPage, data); 
            printStream.println("");
        } 
    }

    private void printRow(int firstIndexInRow, int lastIndexOnPage, int[] data) {
        for (int column = 0; column < columnsPerPage; column++) {
            int index = firstIndexInRow + column * rowsPerPage; 
            if (index <= lastIndexOnPage)
                printStream.format("%10d", data[index]); 
        }
    }

    private void printPageHeader(String pageHeader, int pageNumber) {
        printStream.println(pageHeader + " --- Page " + pageNumber);
        printStream.println(""); 
    }

    public void setOutput(PrintStream printStream) { 
        this.printStream = printStream;
    } 
}
```
```
public class PrimeGenerator {
    private static int[] primes;
    private static ArrayList<Integer> multiplesOfPrimeFactors;

    protected static int[] generate(int n) {
        primes = new int[n];
        multiplesOfPrimeFactors = new ArrayList<Integer>(); 
        set2AsFirstPrime(); 
        checkOddNumbersForSubsequentPrimes();
        return primes; 
    }

    private static void set2AsFirstPrime() { 
        primes[0] = 2; 
        multiplesOfPrimeFactors.add(2);
    }

    private static void checkOddNumbersForSubsequentPrimes() { 
        int primeIndex = 1;
        for (int candidate = 3 ; primeIndex < primes.length ; candidate += 2) { 
            if (isPrime(candidate))
                primes[primeIndex++] = candidate; 
        }
    }

    private static boolean isPrime(int candidate) {
        if (isLeastRelevantMultipleOfNextLargerPrimeFactor(candidate)) {
            multiplesOfPrimeFactors.add(candidate);
            return false; 
        }
        return isNotMultipleOfAnyPreviousPrimeFactor(candidate); 
    }

    private static boolean isLeastRelevantMultipleOfNextLargerPrimeFactor(int candidate) {
        int nextLargerPrimeFactor = primes[multiplesOfPrimeFactors.size()];
        int leastRelevantMultiple = nextLargerPrimeFactor * nextLargerPrimeFactor; 
        return candidate == leastRelevantMultiple;
    }

    private static boolean isNotMultipleOfAnyPreviousPrimeFactor(int candidate) {
        for (int n = 1; n < multiplesOfPrimeFactors.size(); n++) {
            if (isMultipleOfNthPrimeFactor(candidate, n)) 
                return false;
        }
        return true; 
    }

    private static boolean isMultipleOfNthPrimeFactor(int candidate, int n) {
        return candidate == smallestOddNthMultipleNotLessThanCandidate(candidate, n);
    }

    private static int smallestOddNthMultipleNotLessThanCandidate(int candidate, int n) {
        int multiple = multiplesOfPrimeFactors.get(n); 
        while (multiple < candidate)
            multiple += 2 * primes[n]; 
        multiplesOfPrimeFactors.set(n, multiple); 
        return multiple;
    } 
}

```


- 바뀐점
  - 변수명
    - 좀 더 길고 서술적인 변수 이름
    - 상수와 변수(카멜표기법)를 분리
    - System.out.println();을 printStream.println("");으로 변경 ???
  - 함수
    - 접근지정자가 생김
  - 함수 선언과 클래스 선언으로 코드를 설명함
  - 가독성을 위해 공백을 추가하고 형식을 맞춤
  - 각각의 클래스의 책임은 아래와 같습니다.

- 함수 설명
  - PrimePrinter
    - main 함수 하나를 포함하며 실행 환경을 책임짐
  - RowColumnPagePrinter
    - 숫자 목록을 주어진 행과 열에 맞춰 페이지에 출력
  - PrimeGenerator
    - 소수 목록을 생성



### 변경하기 쉬운 클래스
확장에 개방적이고 수정에 폐쇄적이어야 한다는 원칙,
목록 10-9 에서 10-10 가장 큰 차이는
변경 시에 클래스 에 손을 안대도 된다는 것
함수가 아니라 클.래.스.임 그래서 공통부분은 추상 클래스로 묶고, 계속 클래스를 만드는 걸로 바꾼 것 테스트 하기도 쉬움. 클래스별로 나뉘어 있으니깐
혹시 추가할게 있다면(update) 상속을 받아서 만들면 됨
새 기능을 수정하거나 기존 기능을 변경할 때 건드릴 코드가 최소인 시스템 구조가 바람직하다. 이상적 인 시스템 이라면 새 기능을 추가할 때 시스템을 확장할 뿐 기존 코드를 변경하지는 않는다





### 변경으로부터 격리


