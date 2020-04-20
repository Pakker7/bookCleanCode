### JUnit 들여다보기

- junit3,4,5
  - JUnit3 에서는 TestCase 클래스를 상속해서 test로 시작하는 메소드를 만들면 됩니다.
  - JUnit4,Junit5 메소드에 @Test 어노테이션을 붙이면 됩니다.
  
 - 테스트 코드
 ```java
 public class ComparisonCompactorTest extends TestCase{ 	

	public void testMessage() {
		String failure = new ComparisonCompactor(0, "b", "c").compact("a");
		assertTrue("a expected:<[b]> but was:<[c]>".equals(failure));
	}

	public void testStartSame() {
		String failure = new ComparisonCompactor(1, "ba", "bc").compact(null);
		assertEquals("expected:<b[a]> but was:<b[c]>", failure);
	}

	public void testEndSame() {
		String failure = new ComparisonCompactor(1, "ab", "cb").compact(null);
		assertEquals("expected:<[a]b> but was:<[c]b>", failure);
	}

	public void testSame() {
		String failure = new ComparisonCompactor(1, "ab", "ab").compact(null);
		assertEquals("expected:<ab> but was:<ab>", failure);
	}

	public void testNoContextStartAndEndSame() {
		String failure = new ComparisonCompactor(0, "abc", "adc").compact(null);
		assertEquals("expected:<...[b]...> but was:<...[d]...>", failure);
	}

	public void testStartAndEndContext() {
		String failure = new ComparisonCompactor(1, "abc", "adc").compact(null);
		assertEquals("expected:<a[b]c> but was:<a[d]c>", failure);
	}

	public void testStartAndEndContextWithEllipses() {
		String failure = new ComparisonCompactor(1, "abcde", "abfde").compact(null);
		assertEquals("expected:<...b[c]d...> but was:<...b[f]d...>", failure);
	}

	public void testComparisonErrorStartSameComplete() {
		String failure = new ComparisonCompactor(2, "ab", "abc").compact(null);
		assertEquals("expected:<ab[]> but was:<ab[c]>", failure);
	}

	public void testComparisonErrorEndSameComplete() {
		String failure = new ComparisonCompactor(0, "bc", "abc").compact(null);
		assertEquals("expected:<[]...> but was:<[a]...>", failure);
	}

	public void testComparisonErrorEndSameCompleteContext() {
		String failure = new ComparisonCompactor(2, "bc", "abc").compact(null);
		assertEquals("expected:<[]bc> but was:<[a]bc>", failure);
	}

	public void testComparisonErrorOverlapingMatches() {
		String failure = new ComparisonCompactor(0, "abc", "abbc").compact(null);
		assertEquals("expected:<...[]...> but was:<...[b]...>", failure);
	}

	public void testComparisonErrorOverlapingMatchesContext() {
		String failure = new ComparisonCompactor(2, "abc", "abbc").compact(null);
		assertEquals("expected:<ab[]c> but was:<ab[b]c>", failure);
	}

	public void testComparisonErrorOverlapingMatches2() {
		String failure = new ComparisonCompactor(0, "abcdde", "abcde").compact(null);
		assertEquals("expected:<...[d]...> but was:<...[]...>", failure);
	}

	public void testComparisonErrorOverlapingMatches2Context() {
		String failure = new ComparisonCompactor(2, "abcdde", "abcde").compact(null);
		assertEquals("expected:<...cd[d]e> but was:<...cd[]e>", failure);
	}

	public void testComparisonErrorWithActualNull() {
		String failure = new ComparisonCompactor(0, "a", null).compact(null);
		assertEquals("expected:<a> but was:<null>", failure);
	}

	public void testComparisonErrorWithActualNullContext() {
		String failure = new ComparisonCompactor(2, "a", null).compact(null);
		assertEquals("expected:<a> but was:<null>", failure);
	}

	public void testComparisonErrorWithExpectedNull() {
		String failure = new ComparisonCompactor(0, null, "a").compact(null);
		assertEquals("expected:<null> but was:<a>", failure);
	}

	public void testComparisonErrorWithExpectedNullContext() {
		String failure = new ComparisonCompactor(2, null, "a").compact(null);
		assertEquals("expected:<null> but was:<a>", failure);
	}

	public void testBug609972() {
		String failure = new ComparisonCompactor(10, "S&P500", "0").compact(null);
		assertEquals("expected:<[S&P50]0> but was:<[]0>", failure);
	}
}
```

```java
public class ComparisonCompactor {
	private static final String ELLIPSIS = "...";
	private static final String DELTA_START = "[";
	private static final String DELTA_END = "]";

	private int fContextLength;  //f는 왜 ...
	private String fExpected;
	private String fActual;
	private int fPrefix;
	private int fSuffix;

	public ComparisonCompactor(final int fContextLength, final String fExpected, final String fActual) {
		this.fContextLength = fContextLength;
		this.fExpected = fExpected;
		this.fActual = fActual;
	}

	public String compact(final String message) { //message의 의미가 뭐지?
		if (this.fExpected == null || this.fActual == null || areStringEqual()) {
			return Assert.format(message, this.fExpected, this.fActual);
		}

		this.findCommonPrefix();
		this.findCommonSuffix();
		final String expected = this.compactString(this.fExpected);
		final String actual = this.compactString(this.fActual);
		return Assert.format(message, expected, actual);
	}

	private String compactString(final String source) {	
										//abce		expected	2			//4-1-2+1 = 2	//[ce]
										//abcde		actual		2			//5-1-2+1 = 3	//[de]
		String result = DELTA_START + source.substring(this.fPrefix, source.length() - this.fSuffix + 1) + DELTA_END;
		
		System.out.println(result);
		
		if (0 < this.fPrefix) {
			result = this.computeCommonPrefix() + result;
			System.out.println("[1] "+result);
		}

		if (0 < this.fSuffix) {
			result = result + this.computeCommonSuffix();
			System.out.println("[2] "+result);
		}

		return result;
	}

	private void findCommonPrefix() {	// abce				//abcde
		this.fPrefix = 0;               // 4				//5
		final int end = Math.min(this.fExpected.length(), this.fActual.length()); // 최솟값반환
		for (; this.fPrefix < end; this.fPrefix++) {
				// 0 		 //4
			if (this.fExpected.charAt(this.fPrefix) != this.fActual.charAt(this.fPrefix)) { // 0,1,2,3,4번째 비교 다르면 break 
				break;																		// 앞에서부터 똑같은 부분까지를 찾는듯
			}
		}
	}

	private void findCommonSuffix() {	// abce				//abcde
		int expectedSuffix = this.fExpected.length() - 1;	//3
		int actualSuffix = this.fActual.length() - 1;		//4
			//첫번째 	위에서들어간 2		//4					//2				//3
			//두번째	2				//3					//2				//2
		for (; (this.fPrefix <= actualSuffix) && (this.fPrefix <= expectedSuffix); actualSuffix--, expectedSuffix--) {
			// outOfBoundException 막으려고
									//첫번째	3								// 4		//값이 같아서 한번더 for문돌음
									//두번째 	2								// 3		// 값 다름 break;
			if (this.fExpected.charAt(expectedSuffix) != this.fActual.charAt(actualSuffix)) {
				break;
			}
		}
			//	2	=	4 - 2
		this.fSuffix = this.fExpected.length() - expectedSuffix;
	}

	private String computeCommonPrefix() {
		String tmp1 = (this.fContextLength < this.fPrefix ? ELLIPSIS : "");
		String tmp2 = this.fExpected.substring(Math.max(0, this.fPrefix - this.fContextLength), this.fPrefix);
		
				
		return tmp1+tmp2;
	}

	private String computeCommonSuffix() {
		final int end = Math.min(this.fExpected.length() - this.fSuffix + 1 + this.fContextLength,
				this.fExpected.length());
		return this.fExpected.substring(this.fExpected.length() - this.fSuffix + 1, end)
				+ (this.fExpected.length() - this.fSuffix + 1 < this.fExpected.length() - this.fContextLength ? ELLIPSIS
						: "");
	}

	private boolean areStringEqual() {
		return this.fExpected.equals(this.fActual);
	}
}
```
- 주석은 이해해 보려는 나의흔적... 이지만 클래스를 계속 봐도 뭔가 모르겠는 부분들이 있다.
  - 1. int 형 1의 용도는..?
  - 2. compact() 의 String message 용도는..?
  ```java
  String failure = new ComparisonCompactor(1, "abcf", "abcdef").compact("de");
  ```
  
- 오늘은 모르겠지만 내일 다시보면 이해가 되겠지...
- 참고할 곳 https://github.com/ivvve/What-I-study/commits/master/book/CleanCode/code_examples/src/main/java/org/junit/ComparisonCompactor.java

- p.334 숨겨진 시간적인 결합(p.390 참고)
  - 함수를 짤 때는 함수 **인수**를 적절히 배치해 *함수가 호출되는 순서*를 명백히 드러낸다.
  - 시간적인 결합을 강제하지 않으면, 에러가 일어날 수 있는 함수에서만 **숨겨진 시간적인 결합**을 사용한다.
  - 인수를 일부러 만드는 것이므로, 시각적으로 함수가 지저분해 보일 수 있다. 그러나 시간적 결합이 필요하다면 사용하라
  
- p.334 일관성을 유지(p.391 참고)
  - 코드 구조를 잡을 때는 이유를 고민할 것 
  - *코드에 일관성이 없으면* 남들은 *맘대로 바꿔도 된다*라고 생각한다.
  
- p.336 경계 조건을 캡슐화 하라(p.392 참고)
  - 결계 조건은 빼먹거나 놓치기 쉽다. 경계 조건은 한곳에서 별도로 처리한다. 
  - ex. length +1 이런거..

- 전체를 완벽히 이해한 건 아니지만, 리펙토링 과정을 따라간 것으로 충분히 도움이 된다. 나중에 한번더 봐야지
