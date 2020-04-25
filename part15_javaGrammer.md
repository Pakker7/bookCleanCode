### LocalDate
- LocalDate 를쓰면, new Date() 보다 plus, minus가 엄청 쉽게됨. 프린트는 YYYYMMDD로 출력 됨
- LocalDateTime 도 있음 이건 전체 출력

### 신기한 for문 2
```java
		for (; this.fPrefix < end; this.fPrefix++) {

			if (this.fExpected.charAt(this.fPrefix) != this.fActual.charAt(this.fPrefix)) {
				break;
			}
		}
```

### for문에 이런 긴 조건 주는 것도 청봄
```java
    for (; (this.fPrefix <= actualSuffix) && (this.fPrefix <= expectedSuffix); actualSuffix--, expectedSuffix--) {

        if (this.fExpected.charAt(expectedSuffix) != this.fActual.charAt(actualSuffix)) {
          break;
        }
      }
```
