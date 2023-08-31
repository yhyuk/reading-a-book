# 3장 함수(Functions)

3장은 함수를 어떤식으로 구현해야 클린하게 작성할 수 있는지 방법을 제시하고 있다.

### 1. 작게 만들어라
해당 장에서 계속해서 나오는 문구이며 가장 중요한 규칙이다. 
> 작게 만들어라!

if-else, while 등에 들어가는 블록은 1줄 이어야한다.    
어떻게든 작고 또 작게 만드는것을 강조 하고있다.

<br>

### 2. 한가지만 해라
> 함수는 한가지만 해야 하고 그걸 잘 해야 한다.   

한가지만 하는지 판단하는 방법은 그렇다면 어떤것인가?
  1. 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행하는가?
  2. 함수 내 로직을 의미있는 이름으로 다른 함수를 추출할 수 있는가?
  3. 함수 내 섹션이 나눠지는가?

위 방법을 적용해서 생각해보도록 하자.

<br>

### 3. 함수 추상화 수준은 하나로 하라
> 함수 내 모든 문장의 추상화 수준이 동일해야한다.

한 함수 내에 추상화 수준을 여러개 섞으면 코드를 읽는 사람은 헷갈린다.    
그럼 어떻게 추상화 수준이 동일하게 작성할 수 있을까?    
➡️ 내려가기 규칙   
- 코드는 위에서 아래로 코드를 읽는것이 편하고 좋다. 
- 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.
- 함수를 타고 들어가서 나오는 함수는 추상화 수준이 한 단계 낮은 함수여야 한다.
- 내려가기 규칙을 적용해서 추상화 수준이 하나인 함수를 작성하자.

<br>

### 4. 서술적인 이름을 사용하라
> 서술적이고 이해가 쉬운 긴 이름 > 짧고 난해한 이름, 서술적인 주석

예시로 toOrder() 보다 isOrderStatus(), hasAvailableProduct() 등이 바람직하다

<br>

### 5. 적절한 함수 인자를 사용하라
본문에서 가장 적합한 인자 수로 0개 > 1개 > 2개 > 3개 > 4개이상 으로 나타내고있다.

#### 5-1  단항 함수(1개)
- 좋은 단항 함수 
  - case1) 질문을 던지는 경우: boolean fileExists("FileData");
  - case2) 인자를 변환해 리턴하는 경우(변환함수): inputStream fileOpen("FileData");   
  - 위에 두가지 경우가 아니라면 단항 함수는 가급적으로 쓰지말자.

- 나쁜 단항 함수
  - case1) 변환 함수에서 출력 인수 사용 지양: void includeSetupPageInto(StringBuffer pageTest)
  - case2) 입력 인수를 변환하는 함수라면 변환 결과는 변환값으로 돌려준다 :   
    void transform(StringBuffer out) 보다 StringBuffer transform(StringBuffer in)이 좋다.

- 플래그 인자
  - 플래그 인자는 boolean 타입 인자를 말한다. ex) render(true)
  - 사용을 지양해야 하는 이유로 true면 a, false면 b를 처리 하므로 2가지 일을 하기 때문이다.
  - 해결법으로 ture, false 로직을 적합한 함수로 호출하는 것이다.

#### 5-2 이항 함수(2개)
당연히 이항 함수는 단항 함수보다 이해하기가 어렵다.   
예를들어 assertEquals(expected, actual) 메소드만 보더라도 expected가 먼저인지, actual가 먼저 인지 헷갈린다.    
그렇다고 무조건 이항함수가 나쁘다는건 아니다. 그래도 가능하면 단항 함수로 변경시키려고 노력하자.
- 좋은 이항 함수
  - case1) Point p = new Point(0,0)
  - 직계 좌표는 일반적으로 x,y를 갖기 때문에 위와 같이 쓰면 좋다.
  
- 나쁜 이항 함수
  - case1) writeField(outputStream, name)
  - outputStream에 name을 쓴다는 건지, name에 outputStream에 있는 값을 넣는다는 건지 정확히 알 수가없다.
  - 해결법으로 outputStream.writeField(name) 형태로 변경하면 정확하게 이해할 수 있다.

#### 5-3 삼항 함수(3개), 다항 함수(4개 이상)
삼항, 다항은 최대한 신중하게 생각해야 한다.   
이항 함수보다 배는 이해하기 어렵기 때문이다.
- 삼항, 다항 함수 해결법
  - case1) Circle makeCircle(double x, double y, double radius) -> Circle makeCircle(Point center, double radius)
  - x, y를 Pointer 객체로 만들어서 인자로 받으면 좀 더 이해가 쉽다.
  - case2) String.format("%s  %s %s")
  - 위 함수를 호출하려면 인자를 (String method, String arg) 라고 써야 하는데 이를 (String... args)로 한번에 받을 수 있다.

<br>

### 6. 부수 효과를 일으키지 마라
```java
public class UserValidator {
    private Cryptographer cryptographer;
    
    public boolean checkPasswd(String id, String passwd) {
    	User user = UserGateway.findById(id);
        if (user != null) {
            String codedPhrase = user.getPhraseEncodedByPasswd();
            String phrase = cryptographer.decrypt(codePhrase, passwd);
            
            if ("Valid Passwd".equals(phrase)) {
            	Session.init();
                return true;
            }
        }
        
        return false;
    }
}
```

해당 함수를 살펴 보면 checkPassword 라는 이름만 봐도 비밀번호를 확인 해주는 함수로 보인다.   
하지만 내부 로직을 보면 부수효과를 일으키는 로직이 존재한다.

```java
if ("Valid Passwd".equals(phrase)) {
	Session.init();
	return true;
}
```

위와 같은 세션을 초기화(부수효과) 시키는 로직은 함수 이름만 보면 알 수가 없다.
함수 이름을 checkedPassword 보다 checkPAsswordAndInitSession 라고 변경하는것이 이해하기가 쉬울 것이다.

<br>

### 7. 명령과 조회를 분리하라
```java
public boolean set(String attribute, String value);
```

위 함수는 attribute에 value를 set에 성공하면 true, 실패하는 false를 반환하는 함수이다.

```java
if (set("username", "effortguy"))
        ...
```
set 함수를 사용하면 위와 같은 코드를 만들 수 있는데, set을 하는 함수인지, set이 되어있다는 함수 인지 구분이 힘들다.
```java
if (existAttribute("username")) {
    setAttribute("username", "effortguy");    
}
```

위 처럼 명령(setAttribute)과 조회(existAttribute)를 분리하니 확실히 읽기 편해졌다.

<br>

### 8. 오류 코드보다 예외를 사용하라
```java
if (deletePAge(page) == E_OK)
```
위 처럼 사용하면 '7. 명령과 조회를 분리하라'규칙을 위반한다.
그리고 여러 단계로 중첩되는 코드를 야기한다.

```java
if (deletePage(page) == E_OK) {
    if (registry.deleteRegistry(page.name) == E_OK) {
    	logger.log("Registry deleted");
    } else {
    	logger.log("delete Registry failed");
    }
} else {
    logger.log("delete failed")
    return E_ERROR;
}
```
에러 코드를 사용하면 이상하게 생긴 모양의 중첩문이 나온다.   
에러 코드보다 예외 처리를 사용해서 수정해보도록 하자.
```java
try {
	deletePage(page);
	registry.deleteRegistry(page.name);
} catch (Exception e) {
	logger.log(e.getMessage());
}
```
예외처리로 수정하니 보기 편해졌다.   
그리고 try, catch 블록에 있는 로직을 함수로 빼냈다.

```java
public void delete(Page page) {
    try {
    	deletePageAndRegistry(page);
    } catch(Exception e) {
    	logError(e);
    }
}
 
private void deletePageAndRegistry(Page page) throw Exception {
	deletePage(page);
	registry.deleteRegistry(page.name);
}
 
private void logError(Exception e) {
	logger.log(e.getMessage());
}
```
중첩되고 읽기 힘들었던 코드가 깔끔해졌다.   
여기서 잊지 말아야할 점은 '오류도 한 가지 작업만 한다.' 라는 것이다.   

<br>

### 9. 반복하지 말아라
중복을 최소화하게 만들라는 의미이다.   
1장부터 계속 해온 이야기 이므로 생략한다.   

<br>

### 10. 함수를 어떻게 짜죠?
개발을 하면서 처음부터 잘 짤 수는 없다.    
1. 로직을 짠다
2. 테스트 코드를 짠다.
3. 리팩토링을 한다.

위 순서 처럼 계속 리팩토링을 하다보면 어느새 좋은 함수가 작성되어 있는 것을 볼 수 있다.    


<br>

### 느낀점
개발하면서 리팩토링을 자주 했는데, 그 때마다 로직을 작게 함수화 하는 작업을 많이 했던거 같다.    
당시에는 이렇게 작게 만드는게 의미가 있나? 오히려 읽기 복잡하지 않을까? 라는 생각이였는데, 해당 장을 보고 더 확실해졌다.   
줄줄이 쌓인 코드보다 여러개의 조각 퍼즐처럼 잘게잘게 만든다면 나중에 이야기를 읽듯이 읽기 편할 것같다.