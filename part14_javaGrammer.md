
### Iterator
- 컬렉션에 저장된 각 요소에 접근하는 기능
```java
	@Test
	public void teste() {
		List<String> list = new ArrayList<String>();
		list.add("l");
		list.add("2");
		list.add("3");
		list.add("4");
		
		Iterator<String> i = list.iterator();
		while(i.hasNext()) {
			System.out.println(i.next());
		}
	}
```
```
* 결과
l
2
3
4
```
- 메소드
  - Boolean hasNext() 다음 요소가 있는지 여부
  - Object next() 다음 요소 
  - void remove() 요소 삭제, next()로 출력 후 사용하면 됨

- Map 에서 사용시
  ```java
  	@Test
    public void h() {
      Map map = new HashMap();
      map.put(0, "q");
      map.put(0, "w");
      map.put(9, "e");
      map.put(3, "r");

      Iterator it = map.keySet().iterator();
      while (it.hasNext())
        System.out.println(map.get(it.next()));

      Iterator its = map.entrySet().iterator();
      while (its.hasNext())
        System.out.println(its.next());

    }
  ```
  ```
  * 결과
  w
  r
  e
  0=w
  3=r
  9=e
  ```
  
### ListIterator
- ListIterator는 Iterator를 상속받아서 기능을 추가한 것으로, 컬렉션의 요소에 접근할 때 Iterator는 단방향으로만 이동할 수 있는 데 반해 ListIterator는 양방향으로의 이동이 가능하다. 다만 ArrayList나 LinkedList와 같이 List인터페이스를 구현한 컬렉션에서만 사용할 수 있다.
```java
	@Test
	public void t() {
		List list = new ArrayList();

		list.add("q");
		list.add("w");
		list.add("e");
		list.add("r");
		list.add("t");

		ListIterator it = list.listIterator();

		while (it.hasNext())
			System.out.println(it.next());

		System.out.println("===============");

		while (it.hasPrevious())
			System.out.println(it.previous());
	}
```
```
* 결과
q
w
e
r
t
===============
t
r
e
w
q
===============
```

### 신기한 for문?!
- 이런 for문이 있다!? 처음봄..
```java
	private ListIterator lit;
	private Iterator its;

	@Test
	public void forforIterator() {
		List list = new ArrayList(Arrays.asList("q", "w", "e", "r"));

		for (lit = list.listIterator(); lit.hasNext();) {
			System.out.println(lit.next());
		}

		for (its = list.iterator(); its.hasNext();)
			System.out.println(its.next());

	}
```
### assert
- 단정 메서드로 테스트 케이스의 수행 결과를 판별
- 메소드
	- assertEquals(x, y)
	  - 객체 x와 y가 일치함을 확인합니다.
	  - x(예상 값)와 y(실제 값)가 같으면 테스트 통과
	- assertArrayEquals(a, b);
	  - 배열 A와 B가 일치함을 확인합니다.
	- assertFalse(x)
	  - x가 false 인지 확인합니다.
	- assertTrue(x)
	  - x가 true 인지 확인합니다.
	- assertTrue(message, condition)
	  - condition이  true이면 message표시
	- assertNull(o)
	  - 객체o가 null인지 확인합니다.
	- assertNotNull(o)
	  - 객체o가 null이 아닌지 확인합니다.
	- assertSame(ox, oy)
	  - 객체 ox와 oy가 같은 객체임을 확인합니다.
	  - ox와 oy가 같은 객체를 참조하고 있으면 테스트 통과
	  - assertEquals()메서드는 두 객체의 값이 같은지 확인하고, assertSame()메서드는 두 객체의 레퍼런스가 동일한가를 확인합니다. (== 연산자)
	- assertNotSame(ox, oy)
	  - ox와 oy가 같은 객체를 참조하고 있지 않으면 통과
	- assertfail()
	  - 테스트를 바로 실패처리
