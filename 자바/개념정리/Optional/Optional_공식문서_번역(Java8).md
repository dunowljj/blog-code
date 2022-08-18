[4. 블랙잭](evernote:///view/227999746/s692/4068c438-7fa5-e7cc-1324-d18aef0b5a48/edd7c9dd-7d91-3443-02a8-9ed1984a5e07/)

# 공식문서 번역

[Optional 사용에 대한 문서](https://www.baeldung.com/java-optional)

## 1. Overview

이 튜토리얼에서는 Java 8에서 소개하는 Optional 클래스를 설명한다.

Optional class의 목적은 null 참조 대신에 Optional 값들을 나타내는 방법으로 타입 수준의 해결책을 제공하는 것이다. *Optional* class에 대해 깊은 이해를 위해서는 공식문서를 참조하자. [자바 공식 문서](https://www.oracle.com/technical-resources/articles/java/java8-optional.html)

## 2. Creating Optional Objects

Optional 객체를 만드는 여러 방법들이 있는데, 빈 Optional 객체를 만들려면,  Optional의 static 메서드인* empty()*를 사용하면 된다.

```
@Test
public void whenCreatesEmptyOptional_thenCorrect() {
    Optional<String> empty = Optional.empty();
    assertFalse(empty.isPresent());
}
```

*isPresent()* 메서드는 Optional 객체안에 값이 있는지 확인한다. 값은 Optional을 a non-*null* value와 함께 생성할때만 존재한다. *isPresent()* 메서드는 다음 섹션에서 살펴볼 것이다.

Optional object는* s*tatic 메서드인* of()*로도 만들 수 있다.

```
@Test
public void givenNonNull_whenCreatesNonNullable_thenCorrect() {
    String name = "baeldung";
    Optional<String> opt = Optional.of(name);
    assertTrue(opt.isPresent());
}
```

그러나 *of()*의 인수는 null을 통과시키지 못한다. NullpointerException을 발생시킨다.

```
@Test(expected = NullPointerException.class)
public void givenNull_whenThrowsErrorOnCreate_thenCorrect() {
    String name = null;
    Optional.of(name);
}
```

null값이 예상되는 경우에는 *ofNullable()*메서드를 사용할 수 있다.

```
@Test
public void givenNonNull_whenCreatesNullable_thenCorrect() {
    String name = "baeldung";
    Optional<String> opt = Optional.ofNullable(name);
    assertTrue(opt.isPresent());
}
```

이와 같이 사용하면, 예외를 던지지 않는다. 대신에 빈 Optional 객체를 리턴한다

```
@Test
public void givenNull_whenCreatesNullable_thenCorrect() {
    String name = null;
    Optional<String> opt = Optional.ofNullable(name);
    assertFalse(opt.isPresent());
}
```

##

## 3. Checking Value Presence:* isPresent() *and *isEmpty()*

(값의 존재 체크하기)

*isPresent() *메서드를 사용하면 Optional 객체를 생성하거나 메서드에서 리턴받을 때, Optional 객체 안에 값이 있는지 없는지를 체크할 수 있다.

```
@Test
public void givenOptional_whenIsPresentWorks_thenCorrect() {
    Optional<String> opt = Optional.of("Baeldung");
    assertTrue(opt.isPresent());

    opt = Optional.ofNullable(null);
    assertFalse(opt.isPresent());
}
```

이와같이 포장된 값이 null이 아니면 true를 반환한다.

자바 11에서는 isEmpty() 메서드로 반대의 기능을 할 수 있다.

```
@Test
public void givenAnEmptyOptional_thenIsEmptyBehavesAsExpected() {
    Optional<String> opt = Optional.of("Baeldung");
    assertFalse(opt.isEmpty());

    opt = Optional.ofNullable(null);
    assertTrue(opt.isEmpty());
}
```

## 4. Conditional Action With *IfPresent()*

*ifPresent() *메서드는 포장된 값이 null이 아닌것으로 알려지면, 실행할 수 있게 해준다. Optional을 사용하기 전에는 다음과 같은 코드를 쓰곤 했다.

```
if(name != null) {
    System.out.println(name.length());
}
```

특정 코드를 실행하기전에 name변수가 null인지 체크하는 내용이다. 이런 방법은 내용이 길 뿐더러 오류를 발생시키기 쉽다.

게다가 위의 변수를 출력시킨 후,  **다시는 사용하지 않는다는 보장이 있을까?** 그리고 나서 null 체크를 잊지 않을 수 있을까?

코드에서 null 값이 발견된다면, NullpointerException을 발생시킬 수 있다. 입력 이슈때문에 프로그램이 실패하는 것은 주로 나쁜 프로그래밍 prcatice의 결과이다.

Optional은 명쾌하게 nullable 값을 다루게 해줌으로써, 좋은 프로그래밍 practice를 강제한다.

다음은 일반적인 함수형 프로그래밍 스타일이다. 확실하게 존재하는 객체를 이용해 행동할 수 있다.

```
@Test
public void givenOptional_whenIfPresentWorks_thenCorrect() {
    Optional<String> opt = Optional.of("baeldung");
    opt.ifPresent(name -> System.out.println(name.length()));
}
```

위의 예시는 라인 수를 줄여준다. 한 라인은 객체를 Optional객체로 포장하기 위함이며, 다음 라인은 코드의 실행 뿐만 아니라 validation까지 내포하고 있다.

##

## 5. Default Value With orElse()

*orElse()* 메서드는 Optional 인스턴스의 내부에 포장된 값을 반환할때 사용된다. 이 메서드는 default value(기본값)으로 사용하는 인수 하나만을 가진다. 포장된 값이 존재하면 반환하고, 없다면 지정한 default value를 반환한다.

```
@Test
public void whenOrElseWorks_thenCorrect() {
    String nullName = null;
    String name = Optional.ofNullable(nullName).orElse("john");
    assertEquals("john", name);
}
```

## 6. Default Value With orElseGet()

*orElseGet() *메서드는 *orElse()* 메서드와 유사하다. 하지만 Optional 값이 없을 때, 리턴할 값을 가져가는 대신에  함수형 인터페이스인 supplier을 가져간다. supplier 함수형 인터페이스는 실행된 값을 리턴한다.

```
@Test
public void whenOrElseGetWorks_thenCorrect() {
    String nullName = null;
    String name = Optional.ofNullable(nullName).orElseGet(() -> "john");
    assertEquals("john", name);
}
```

## 7. Difference Between orElse and orElseGet()

Optional이나 자바 8에 익숙하지 않은 많은 프로그래머들에게  orElse()와 orElseGet()의 차이는 분명하지 않게 느껴진다. 사실상 두 메서드는 서로 기능상 중복되는 것처럼 보인다.

그러나, 두 메서드 사이에는 미묘하지만 중요한 차이가 존재한다. 그 차이는 우리 코드의 성능을 대폭 영향을 끼칠 수 있다.

테스트 클래스 안에* getMyDefault()* 라고 불리는 메서드를 만들어보자. 해당 함수는 인수도 없으며 default 값를 리턴한다.

```
public String getMyDefault() {
    System.out.println("Getting Default Value");
    return "Default Value";
}
```

두 메서드가 **어디서 중복**되고, **어디서 다른지** 확인하기 위해서, 아래 두 개의 테스트의 side effect를 관찰해보자.

```
@Test
public void whenOrElseGetAndOrElseOverlap_thenCorrect() {
    String text = null;

    String defaultText = Optional.ofNullable(text).orElseGet(this::getMyDefault);

    assertEquals("Default Value", defaultText);

    defaultText = Optional.ofNullable(text).orElse(getMyDefault());
    assertEquals("Default Value", defaultText);
}
```

side effect:

```
Getting default value...
Getting default value...
```

*getMyDefault()* 메서드가 각 케이스에서 호출되었다. **포장된 값이 존재하지 않을 때, **두 메서드는 정확히 같은 일을 수행한다.

다른 테스트를 확인해보자. 이번에는 값이 존재하고, 이상적으로 default 값이 생성되지도 않는다.

```
@Test
public void whenOrElseGetAndOrElseDiffer_thenCorrect() {
    String text = "Text present";

    System.out.println("Using orElseGet:");
    String defaultText
      = Optional.ofNullable(text).orElseGet(this::getMyDefault);
    assertEquals("Text present", defaultText);

    System.out.println("Using orElse:");
    defaultText = Optional.ofNullable(text).orElse(getMyDefault());
    assertEquals("Text present", defaultText);
}
```

null 값에 대한 포장을 하지 않았으며,나머지 코드도 동일하다.

side effect:

```
Using orElseGet:
Using orElse:
Getting default value...
```

포장된 값을 반환하기 위해서* orElseGet() *를 사용할 때, 포함된 값이 존재하기 때문에 getMyDefault()메서드는 호출되지 않는다는 것에 주목하자.

그러나 *orElse() *을 사용할때는 포장된 값이 존재하든 안하든 default 객체가 생성된다. 그래서 이 경우에는 절대 사용되지 않을 불필요한 객체 하나를 만들게 된다.

이 예시에서, default 객체하나를 만드는데 의미있는 비용이 들지는 않는다. JVM이 이에 대한 대처법을 알고 있기 때문이다.

**그러나, *getMyDefault() *같은 메서드가 웹 서비스를 호출을 만들거나, 데이터베이스를 사용한다면 비용이 어떻게 될지는 뻔하다.**

## 8. Exceptions With orElseThrow()

orElseThrow() 메서드는 *orElseGet() , orElse() *에 부재한 값을 다루는 새로운 접근을 더했다. 포장된 값이 없을때, default값을 반환하는 대신에 예외를 던진다.

```
@Test(expected = IllegalArgumentException.class)
public void whenOrElseThrowWorks_thenCorrect() {
    String nullName = null;
    String name = Optional.ofNullable(nullName).orElseThrow(
      IllegalArgumentException::new);
}
```

예외 생성자를 통과하는데 있어서 자바 8의 메서드 참조가 유용하다.

**자바 10에서는 orElseThrow() 메서드의 간편한 no-arg version이 있다. 빈 Optional**의 경우** NoSuchElementException**을 던진다.

```
@Test(expected = NoSuchElementException.class)
public void whenNoArgOrElseThrowWorks_thenCorrect() {
    String nullName = null;
    String name = Optional.ofNullable(nullName).orElseThrow();
}
```

## 9. Returning Value With get()

포장된 값에 접근하는 마지막 접근법은 get()이다.

```
@Test
public void givenOptional_whenGetsValue_thenCorrect() {
    Optional<String> opt = Optional.of("baeldung");
    String name = opt.get();
    assertEquals("baeldung", name);
}
```

그러나 앞에 3가지 접근법과 다르게,  get()은 포장된 객체가 null이 아니라면 값을 리턴하는게 전부이다. null이면 NoSuchElementException을 던지는 것이다.

```
@Test(expected = NoSuchElementException.class)
public void givenOptionalWithNull_whenGetThrowsException_thenCorrect() {
    Optional<String> opt = Optional.ofNullable(null);
    String name = opt.get();
}
```

이것이 get()의 주된 결함이다. 이상적으로, Optional은 예견되지 않은 예외를 피하도록 돕는 것이 목적이다. 이 접근법은 Optional의 목적에 부합하지 않으며,  후에 release때 deprecated 될 것이다.

get() 을 사용하기보다, null case를 대비하고 경쾌하게 다루도록 해주는 다른 나머지 방법들을 사용하기를 권장한다.

## 10. Conditonal Return With filter()

filter() 메서드를 활용하면, 포장된 값을 inline test 할 수 있다. 인수로 predicate를 가지며, Optional 객체를 리턴한다. 포장된 값이 predicate에 의한 테스트를 통과하면, Optional은 통과한값들을 그대로 반환한다.

그러나 predicate가 false를 반환하면, 빈 Optional이 반환될 것이다.

```
@Test
public void whenOptionalFilterWorks_thenCorrect() {
    Integer year = 2016;
    Optional<Integer> yearOptional = Optional.of(year);
    boolean is2016 = yearOptional.filter(y -> y == 2016).isPresent();
    assertTrue(is2016);
    boolean is2017 = yearOptional.filter(y -> y == 2017).isPresent();
    assertFalse(is2017);
}
```

filter() 메서드는 보통 미리 정해논 규칙에 근거해 포장된 값을 거절할때 사용한다. 예시로 잘못된 이메일 형식이나, 충분히 강력하지 않은 패스워드에 대해 거부할때 사용할 수 있다.

다른 예시를 들면, 우리가 모뎀을 사기를 원하고, 우리는 모뎀의 가격만 고려한다고 가정하자. 특정 사이트나 가게에서 모뎀 가격에 대한 푸쉬 알림을 아래 객체들에 받을 수 있다.

```
public class Modem {
    private Double price;

    public Modem(Double price) {
        this.price = price;
    }
    // standard getters and setters
}
```

정한 예산 범위 내의 모뎀 가격을 체크하기위한 단일 목적을 가진 코드가 있다고 하자. 그 코드에 이 객체들을 제공하는 상황이다.
먼저 Optional을 사용하지 않는 코드이다.

```
public boolean priceIsInRange1(Modem modem) {
    boolean isInRange = false;

    if (modem != null && modem.getPrice() != null
      && (modem.getPrice() >= 10
        && modem.getPrice() <= 15)) {

        isInRange = true;
    }
    return isInRange;
}
```

코드가 특히 If 조건에서 상당히 길어진다.  어플리케이션에서 중요한 부분은 마지막에 가격의 범위를 체크하는 것이다. 나머지 체크들은 수비적인 기능이다.

```
@Test
public void whenFiltersWithoutOptional_thenCorrect() {
    assertTrue(priceIsInRange1(new Modem(10.0)));
    assertFalse(priceIsInRange1(new Modem(9.9)));
    assertFalse(priceIsInRange1(new Modem(null)));
    assertFalse(priceIsInRange1(new Modem(15.5)));
    assertFalse(priceIsInRange1(null));
}
```

그것 외에도, 긴 하루동안 어떠한 compile-time 에러들 없이 null 체크를 잊어버릴 가능성도 있다.

이제 Optional#filter를 이용한 경우의 변화를 보자.

```
public boolean priceIsInRange2(Modem modem2) {
     return Optional.ofNullable(modem2)
       .map(Modem::getPrice)
       .filter(p -> p >= 10)
       .filter(p -> p <= 15)
       .isPresent();
 }
```

map은  다른 값들로 값들을 변환시킬때 사용된다. 이 연산이 원본 값을 수정하지 않는다는 것을 기억하자.

위에 케이스에서 가격객체를 Model 클래스로부터 얻을 수 있다. map() 메서드에 대해서는 다음 섹션에서 살펴볼 것이다.

첫번째로, null 객체가 이 메서드를 통과할때 문제가 발생하지 않을 것임을 예상할 수 있다.
둘째로, 메서드의 내부 body에  쓴 내용은 가격-범위 체크를 서술하는 내용을 가진 메서드의 이름이다.

```
@Test
public void whenFiltersWithOptional_thenCorrect() {
    assertTrue(priceIsInRange2(new Modem(10.0)));
    assertFalse(priceIsInRange2(new Modem(9.9)));
    assertFalse(priceIsInRange2(new Modem(null)));
    assertFalse(priceIsInRange2(new Modem(15.5)));
    assertFalse(priceIsInRange2(null));
}
```

이전의 접근은 가격범위를 보장하지만 내부의 부실함을 방어하기 위해서는 추가 작업이 필요하다. 불필요한 if문을 제거하고, 원하지 않는 값들을 거절하기 위해 filter를 활용한다.

## 11. Transforming Value With map()

이전 섹션에서 filter기반으로 어떻게 값을 거절하고 받아들이는지 알아봤다. map() 메서드를 사용해서  Optional 값을 변환시켜서 유사한 구문을 사용할 수 있다.

```
@Test
public void givenOptional_whenMapWorks_thenCorrect() {
    List<String> companyNames = Arrays.asList(
      "paypal", "oracle", "", "microsoft", "", "apple");
    Optional<List<String>> listOptional = Optional.of(companyNames);

    int size = listOptional
      .map(List::size)
      .orElse(0);
    assertEquals(6, size);
}
```

위의 예시에서, Optional 객체로 문자열의 list를 포장했으며, map 메서드를 사용해서 포함된 list를 동작시켰다. 동작시킨 내용은 list의 사이즈를 반환하는 것이다.

map 메서드는 Optional 내부에 포장된 결과를 반환한다. 그렇기에 Optional의 값을 찾아오기 위해 반환되는 Optional에 적절한 메서드를 호출해야한다.

filter 메서드는 단순히 값을 체크하고 Optional을 반환한다.  이 때 Optional은 주어진 predicate와 일치하는 경우에만 해당 값을 나타낸다. 다르게 말해서 빈 Optional을 반환한다. 그러나 map 메서드는 존재하는 값을 가진다. 이 값으로 연산하고 그 결과를 Optional 객체에 포장해서 반환한다.

```
@Test
public void givenOptional_whenMapWorks_thenCorrect2() {
    String name = "baeldung";
    Optional<String> nameOptional = Optional.of(name);

    int len = nameOptional
     .map(String::length)
     .orElse(0);
    assertEquals(8, len);
}
```

map과 filter를 함께 연결해서 더 큰 효과를 낼 수 있다.

유저로부터 입력받은 패스워드의 일치를 체크해야한다고 가정하자. map으로 변환하여 password를 clean할 수 있고, filter를 사용하여 일치여부를 체크할 수 있다.

```
@Test
public void givenOptional_whenMapWorksWithFilter_thenCorrect() {
    String password = " password ";
    Optional<String> passOpt = Optional.of(password);
    boolean correctPassword = passOpt.filter(
      pass -> pass.equals("password")).isPresent();
    assertFalse(correctPassword);

    correctPassword = passOpt
      .map(String::trim)
      .filter(pass -> pass.equals("password"))
      .isPresent();
    assertTrue(correctPassword);
}
```

위와 같이, 입력을 clean하지 않고는 값은 필터링되어 버려질 것이다.  아마 유저들은 앞 뒤 공백들이 입력의 구성이라고 당연하게 여길 것이다. 그래서 일치하지 않는 것을 필터링하기 전에 map을 사용하여 지저분한 패스워드를 깨끗하게 바꿔야 한다.

## 12. Transforming Value With flatMap()

map() 메서드와 같이, 값을 변환하는 대안으로 flatMap()도 존재한다. map 은 포장되지 않은 값들만 변환하는 반면에 flatMap은 포장된 값을 가지며, 그것을 변환하기 전에 포장을 벗겨낸다.

이전에, 간단하게 Optional 인스턴스에 포장된 String객체와 Integer객체를 만들었다. 그러나 빈번하게 복잡한 객체의 접근자를 통해 이 객체들을 받아야 했다.

차이를 명확하게 보기 위해, 사람의 name, age, password 같은 디테일을 가진 Person객체를 살펴보자

```
public class Person {
    private String name;
    private int age;
    private String password;

    public Optional<String> getName() {
        return Optional.ofNullable(name);
    }

    public Optional<Integer> getAge() {
        return Optional.ofNullable(age);
    }

    public Optional<String> getPassword() {
        return Optional.ofNullable(password);
    }

    // normal constructors and setters
}
```

일반적으로 이러한 객체를 만들고, String으로 해왔듯이 Optional 객체에 각 객체를 포장한다.

대안적으로, 다른 메서드 호출을 통해 반환될 수 있다.

```
Person person = new Person("john", 26);
Optional<Person> personOptional = Optional.of(person);
```

Person객체를 포장할때, Person객체가 Optional 중첩된 인스턴스들을 포함하고 있다.

```
@Test
public void givenOptional_whenFlatMapWorks_thenCorrect2() {
    Person person = new Person("john", 26);
    Optional<Person> personOptional = Optional.of(person);

    Optional<Optional<String>> nameOptionalWrapper
      = personOptional.map(Person::getName);
    Optional<String> nameOptional
      = nameOptionalWrapper.orElseThrow(IllegalArgumentException::new);
    String name1 = nameOptional.orElse("");
    assertEquals("john", name1);

    String name = personOptional
      .flatMap(Person::getName)
      .orElse("");
    assertEquals("john", name);
}
```

assertion을 사용하기 위해, Person 객체의 name 속성을 반환하려고 시도하는 내용이다.

map() 메서드를 사용하는 방법, 그 후에는 flatMape()을 사용하는 방법이다.

Person::getName 메서드 참조는 이전 섹션에서 패스워드를 정리하기 위해 사용했던 String::trim 호출과 유사하다.

유일한 차이는  getName()은  trim()연산 처럼 String을 반환하지 않고,Optional을 반환하는 것이다. map 변환이 Optional 객체로 결과를 포장한다는 사실이 중첩 Optional에도 이어진다.

그러므로 map() 메서드를 사용하는 동안, 변환된 값을 사용하기 전에 값을 받기 위해 추가적인 호출이 필요하다. 이 방법으로 Optional wrapper는 제거될 것이며, 이 명령은 flatMap을 사용할때 내부적으로 실행된다.

## 13. Changing Optionals in Java 8

때때로, 다수의 Optional들로부터 비어있지 않은 Optional 객체를 가져올 필요가 생길 수 있다. 이런 경우들에서, orElseOptional()과 같은 메서드는 매우 편리하다. 불행하게도, Java 8에서는 직접적으로 지원하지 않는다.

이 섹션에서 사용할 몇 개의 메서드를 소개한다.

```
private Optional<String> getEmpty() {
    return Optional.empty();
}

private Optional<String> getHello() {
    return Optional.of("hello");
}

private Optional<String> getBye() {
    return Optional.of("bye");
}

private Optional<String> createOptional(String input) {
    if (input == null || "".equals(input) || "empty".equals(input)) {
        return Optional.empty();
    }
    return Optional.of(input);
}
```

자바 8에서 몇 개의 Optional 객체들을 연결하고 첫 non-empty인 것을 가져오기 위해서, Stream API를 사용할 수 있다.

```
@Test
public void givenThreeOptionals_whenChaining_thenFirstNonEmptyIsReturned() {
    Optional<String> found = Stream.of(getEmpty(), getHello(), getBye())
      .filter(Optional::isPresent)
      .map(Optional::get)
      .findFirst();

    assertEquals(getHello(), found);
}
```

이 접근법의 불리한 면은, 스트림의 어디서 non-empty Optional이 나타나는지 상관없이 get 메서드가 언제나 실행된다는 것이다.

만약 Stream.of()를 지나는 메서드를 lazily하게 계산하기를 원한다면, 메서드 참조와 Supplier 인터페이스를 사용할 수 있다.

```
@Test

public void givenThreeOptionals_whenChaining_thenFirstNonEmptyIsReturnedAndRestNotEvaluated() {

    Optional<String> found =

      Stream.<Supplier<Optional<String>>>of(this::getEmpty, this::getHello, this::getBye)

        .map(Supplier::get)
        .filter(Optional::isPresent)
        .map(Optional::get)
        .findFirst();

    assertEquals(getHello(), found);
}
```

이 경우에는 인수를 받는 메서드를 사용할 필요가 있다면, 람다 표현식에 의존해야 한다.

```
@Test

public void givenTwoOptionalsReturnedByOneArgMethod_whenChaining_thenFirstNonEmptyIsReturned() {

    Optional<String> found = Stream.<Supplier<Optional<String>>>of(
      () -> createOptional("empty"),
      () -> createOptional("hello")
    )
      .map(Supplier::get)
      .filter(Optional::isPresent)
      .map(Optional::get)
      .findFirst();

    assertEquals(createOptional("hello"), found);
}
```

보통, 연결된 모든 Optional들이 비어있는 경우, 기본 값을 반환하기를 원한다. 단지 orElse()와 orElseGet()을 추가함으로써 해결할 수 있다.

```
@Test
public void givenTwoEmptyOptionals_whenChaining_thenDefaultIsReturned() {
    String found = Stream.<Supplier<Optional<String>>>of(
      () -> createOptional("empty"),
      () -> createOptional("empty")
    )
      .map(Supplier::get)
      .filter(Optional::isPresent)
      .map(Optional::get)
      .findFirst()
      .orElseGet(() -> "default");

    assertEquals("default", found);
}
```

## 14. JDK 9 Optional API

Java 9에서 새로운 메서드를 추가했다.

- or() 메서드: 대안적인 Optional을 생성하는 supplier을 제공하기 위해
- ifPresentOrElse() 메서드 : Optional이 존재하면 action을 실행하고, 존재하지 않는다면 실행하지 않는 메서드
- stream() 메서드 : Optional을 Stream으로 변환해준다.

[further reading](https://www.baeldung.com/java-9-optional)

## 15. Misuse of Optionals

마지막으로,  Optional들을 사용하는위험한 시도, Optional 매개변수를 메서드로 넘겨주는 상황을 살펴보자.

Person의 목록을 가지고 있고, 해당 리스트에서 주어진 사람들의 이름에 따라 검색하기를 원한다고 상상해보자. 또한,  엔트리들을 존재한다면, 최소 특정 age가 되도록 하고싶은 상황이다.

```

public static List<Person> search(List<Person> people, String name, Optional<Integer> age) {

    // Null checks for people and name
    return people.stream()
            .filter(p -> p.getName().equals(name))
            .filter(p -> p.getAge().get() >= age.orElse(0))
            .collect(Collectors.toList());
}
```

그리고 나서 해당 메서드를 배포하고, 다른 개발자가 이것을 사용하려고 시도한다.
`someObject.search(people, "Peter", null);`

이제 그 개발자는 코드를 실행하고 NullPointerException을 받는다.  **optional 매개변수에 null 체크를 해야되는 상황이 온 것이다. 이런 상황을 피하는 것이 목적인데 그것을 파괴시킨다.**

처리하는 더 나은 방법들이다.

```

public static List<Person> search(List<Person> people, String name, Integer age) {

    // Null checks for people and name
    final Integer ageFilter = age != null ? age : 0;

    return people.stream()
            .filter(p -> p.getName().equals(name))
            .filter(p -> p.getAge().get() >= ageFilter)
            .collect(Collectors.toList());
}
```

매개변수는 여전히 optional이지만, 유일한 체크를 통해 처리한다.

다른 가능성은 **두 개의 오버로딩된 메서드를 사용**하는 것이다.

```
public static List<Person> search(List<Person> people, String name) {
    return doSearch(people, name, 0);
}

public static List<Person> search(List<Person> people, String name, int age) {
    return doSearch(people, name, age);
}

private static List<Person> doSearch(List<Person> people, String name, int age) {

    // Null checks for people and name
    return people.stream()
            .filter(p -> p.getName().equals(name))
            .filter(p -> p.getAge().get().intValue() >= age)
            .collect(Collectors.toList());
}
```

이 경우 명확한 두 메서드들이 (비록 구현을 공유하지만)다른 일을 하면서 API를 제공한다.

Optional들을 메서드 매개변수로 사용하는 것을 피하는 해결책들이 있다. **자바에서 Optional을 출시한 의도는 반환 타입으로 사용하기 위해서이다.** 따라서 메서드가 빈 값을 반환할 수 있음을 시사한다. 사실상,  메서드 매개변수로서의 Optional은 [코드를 분석한 일부 사람들](https://rules.sonarsource.com/java/RSPEC-3553)이 장려하지 않는다고 말했다.

## 16. Optional and Serialization

위에서 말했듯이, Optional은 반환타입으로 사용되도록 의도되었다.field 타입으로 사용하는 건 추천하지 않는다.

더해서 Optional을 serialzable 클래스에서 사용하는 것은 NotSeiralizableException을 발생시킨다. [Java Optional as Return Type](https://www.baeldung.com/java-optional-return) 글에서 직렬화 이슈에 대해 더 깊게 다룬다.

그리고, [Using Optional With Jackson](https://www.baeldung.com/jackson-optional)에서 목표 결과를 위한 몇 가지 차선책과 함께 Optional 필드들이 직렬화될때 무엇이 일어나는지 설명한다.

## 17. Conclusion

이 글에서 우리는 Java 8 Optional 클래스의 중요한 특징들을 다뤘다.

간결하게 null 체크나 입력 validation 대신 Optional을 선택해야하는지 이유를 알아보았다.

또한 get(), orElse(), orElseGet() 메서드들을 통해서 어떻게 Optional의 값, 또는 값이 비어있다면 기본값을 받아오는지를 배웠다.

그리고 나서 어떻게 map(), flatMap(), filter()와 함께 Optionals를 어떻게 변환하고 필터링하는지 보았다.  Optional API가 제공하는 것들이 얼마나 능수능란한지, 다른 메서드들을 쉽게 chain할 수 있는 것을 이야기했다.

마지막으로 왜 Optional 매개변수의 사용이 좋지 않은 생각인지 그리고 어떻게 그것을 피하는지를 알아보았다.