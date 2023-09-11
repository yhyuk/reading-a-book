# 4장 주석(Comments)

개발을 하면서 주석은 뺴놓을 수 없는 요소이며, 작성을 올바르게 하는 방법을 제시하고있다.

### 1. 코드로 의도를 표현하라
대부분 주석을 생각하면 코드로 설명이 안되니 이해 시키려고 작성했던 적이 많이 있다.   
주석을 작성할 생각보다 먼저 개발자의 의도를 표현하는 방법을 사용하려고 노력하자.
- 안좋은 예시)
```java
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
if ((employee.flags & HOULY_FLAG) && (employee.age > 65))
```

if 조건이 길고 어렵기 때문에 주석을 달았다.
- 좋은 예시)
```java
if (employee.isEligibleForFullBenefits())
```

주석을 제거하고 위처럼 의도가 분명하게 나타나는 코드로 변경하니 주석이 없어도 읽기 편하다.

<br>

### 2. 좋은 주석

#### 2-1. 법적인 주석
코드 LICENSE를 명시할 때 사용하는 주석은 소유권 정보로 주석으로 타당하다.
```java
// Copyright (c) 2019, 2020, 2021 by Effort Guy, All rights reserved.
// GNU General Public License 버전 2 이상을 따르는 조건으로 배포한다.
```

#### 2-2. 정보를 제공하는 주석
주석이 없어도 이해를 할 수 있지만, 바로 이해하기 힘들기 때문에 좋은 주석이다.
```java
// kk:mm:ss EEE, MMM dd, yyyy 형식이다.
Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*");
```
만약 주석을 사용하고 싶지 않다면, 함수형태로 따로 추출해서 변경하면 될 것 같다.

####  2-3. 의미를 명료하게 밝히는 주석
표준 라이브러리나 변경하지 못하는 코드에 속하는 모호한 인수나 반환값의 의미를 명료하게 밝히는 주석은 좋은 주석이다.
```java
assertTrue(a.compareTo(a) == 0); // a == a
assertTrue(a.compareTo(b) != 0); // a != b
assertTrue(a.compareTo(b) == -1); // a < b
assertTrue(a.compareTo(b) == 1); // a > b
```

#### 2-4. 결과를 경고하는 주석
실행 결과를 미리 경고하는 주석은 좋은 주석이다.
```java
// 테스트 수행시간은 10분입니다.
public void testCalculdate() ...
```

#### 2-5. TODO, FIXME 주석
IDE에서 인식할 수 있는 주석을 사용하는 것도 괜찮은 방법이다.   
TODO: 해야 할 일    
FIXME: 수정할 작업   
```java
// TODO 필요시 작업해야함
private Order getOrder() ...

// FIXME 버그발생으로 수정작업
private void addOrder() ...
```

#### 2-6. 중요성을 강조하는 주석
대수롭지 않다고 여겨서 지우거나 수정하면 절대 안 되는 부분에 강조를 위한 주석을 사용한다.
```java
// 여기서 trim은 반드시 필요하다. 
// 공백으로 시작하는 문자열이 가끔 들어오기 때문이다.
String listItemContent = match.group(0).trim();
```

#### 2-7. Javadocs
공개 API 또는 회사 내에서 공통으로 쓰고 있는 모듈에 달아 놓으면 좋다.   
대표적인 라이브러리 코드를 살펴보면 확인할 수 있다.

#### 2-8. 주석 같은 Annotation
함수, 변수가 변경되어서 삭제된다는 걸 주석으로도 남길 수 있지만, 주석보단 annotation이 더 눈에 띄고 좋다.   
- @Deprecated : '어느 시점 이후로 사라지게 될 부분이다.'라는 뜻의 어노테이션   
- @NotThreadSafe : 쓰레드 세이프하지 않다.   
- @Nullable : Null 허용   
- @NonNull : Null이 허용 되지 않음   

<br>

### 3. 나쁜 주석

#### 3-1. 주절거리는 주석
- 개발한 당사자만 알 수 있는 주석
```java
public void loadProperties() {
    try {
        loadedProperties.load(propertiesStream);
    } catch (IOException e) {
        // 속성 파일이 없다면 기본 값을 모두 메모리로 읽어 들였다는 의미다.
    }
}
```

catch에 어떤 작업을 하려고 주석을 달아 놓은 건지, 나중에 작업을 하려고 주석을 한건지,   
확실하지 않기 때문에 로직을 계속 살펴볼 수 밖에 없다.   


#### 3-2. 코드와 동일한 내용을 써놓은 주석
```java
// this.closed가 true일 때 반환되는 유틸
// 타임아웃에 도달하면 예외를 던짐
public synchronized void waitForClose(final long millis) throws Exception {
    if(!closed) {
        wait(millis);
        if(!closed) throw new Exception();
    }
}
```
주석 읽을 시간에 코드를 읽는게 더 빠르다.

#### 3-3. 애매한 정보가 있는 주석
주석이 달린 로직을 적지 않고 애매하게 일부만 적은 쓸모없는 주석을 의미한다.

#### 3-4. 의무적으로 다는 주석
```java
/**
 * 
 * @param title CD 제목
 * @param author CD 저자             
 */
public void addCD(String title, String author) {
    CD cd = new CD();
    cd.title = title;
    cd.author = author;
    cds.add(cd);
}
```
모든 메소드의 인자를 그냥 아무 의미없이 의무적으로 다는 주석을 필요가없다.   

#### 3-5. 이력을 기록하는 주석
git 등으로 관리가 되기에 소스에 주석으로 이력을 관리하는 주석은 별로다.
```java
/**
 *
 * 2023년 10월 01일 : 서비스 구현
 * 2023년 10월 05일 : 버그로 인한 이슈 해결
 * 2023년 12월 22일 : 추가 요청사항으로 인한 클래스 추가
 */
```

#### 3-6. 함수나 변수로 표현할 수 있다면 주석을 달지 마라.
```java
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))
```

해당 조건문에 주석을 달지 않고 함수화 하여 표현하자.
```java
if (employee.isEligibleForFullBenefits())
```

#### 3-7. 주석으로 처리한 코드
```java
return getMember();
 
// updateMember();
// return member;
```
해당 코드는 개발자가 쓸모없어서 한건지, 잠시 해둔건지 무슨의미로 주석을 했는지 알 수가 없어 오해가 생긴다.


<br>

### 느낀점
나도 개발을 하면서 주석을 많이 작성하지만, 이번 장을 보면서 느낀건 여태껏 불필요하게 주석을 '형식상' 많이 작성 했던 것 같다.   
해당 장에서 얘기하는 '부정확하고 코드로 나타낼 수 있는 주석은 필요없다' 라는 것처럼   
주석을 무조건적인 필수가 아닌 정말 필요할 때만 넣어야 될 것같다.
