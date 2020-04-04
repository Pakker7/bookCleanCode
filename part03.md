### 함수

* 함수의 인수
- 함수에서 이상적인 인수 갯수는 0개(무항) 그다음은 1개(단항) 그다음은 2개(이항) 그 이상은 별로임
-> ? 이해가 어려움, 테스트 케이스가 많아져서 프로그램이 불완전해짐

1. 단항
 1) 인수에게 질문을 던지는 경우  ex. boolean fileExists("myfile")
 2) 인수를 뭔가로 변환해서 결과를 return 하는 경우(변환 함수)

 참고 사항
 변환함수에서 출력 인수를 사용하지 말길
 ex. void includeSetupPageInfo(StirngBuffer pageText) --> void인데 Stringbuffer

2. 이항
 이항함수는 최대한 단항으로 만들어보자
 ex. writeField(outputStream, name) 메서드는
	1) writeField 메서드를 outputStream 클래스 구성원으로 만들어 outputStream,writeField(name)으로 호출
	2) outputStream을 현재 클래스 구성원 변수로 만들어서 인수로 넘기지 말기
	3) FieldWriter라는 새 클래스를 만들어 outputStream을 받고 write메서드를 구성

? 그럼에도 불구하고 인수가 2-3개 필요하다면?
 -> 그냥 클래스를 만들어서, 객체화를 해서 합쳐서 한번에 넘기기
 -> 인수 형태가 동일하다면 List<>로 만들어서 넘기기

참고 사항
 단항 함수는 함수와 인수가 동사/명사 쌍을 이루게, write
Field(name)
 함수 이름에 인수를 순서대로 넣으면 헷갈리지 않는다. ex. assertEquals -> assertExpectedEqualsActual(expected,actual)

* 부수효과
부수효과를 일으키지 말아라
ex. 하나의 기능을 수행하는데 이 기능이 수행완료되면 ~한 기능을 연달아 수행. 이런거 따로 함수로 무조건 빼기
여러개면 함수를 빼서만들어라

* 명령과 조회 분리
정확하게 함수명을 바꾸던지해서 헷갈리지 않게 하기

* try/catch
이것도 기능이니 블록을 빼서 함수를 만들기

spring try/catch를 aop사용하면 짧아진다?!?!라고함
