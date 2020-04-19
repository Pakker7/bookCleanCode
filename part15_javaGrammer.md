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
