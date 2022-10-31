# 스트림 활용

- 내부 반복 뿐 아니라 코드를 병렬로 실행할지 여부도 결정할 수 있다
- 필터링, 슬라이싱, 매핑, 검색, 매칭, 리듀싱 등 다양한 데이터 처리 질의를 표현할 수 있다

# 5.1 필터링
- 스트림의 요소를 선택하는 방법
  - 프레디케이트 필터링
  - 고유 요소 필터링

## 5.1.1 프레디케이트로 필터링
- filter
  - 프레디케이트를 인수로 받아서 프레디케이트와 일치하는 모든 요소를 포함하는 스트림을 리턴

```java
List<Dish> vegetarianMenu = menu.stream()
        .filter(Dish::isVegetarian)
        .collect(toList());
```

## 5.1.2 고유 요소 필터링
- distinct
  - 고유 요소로 이루어진 스트림을 리턴
  - 고유 여부는 스트림에서 만든 객체의 hashCode, equals로 결정

```java
// 리스트의 모든 짝수 선택, 중복 필터링
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
numbers.stream().filter(i -> i%2 == 0)
    .distinct().forEach(System.out::println);
```

# 5.2 스트림 슬라이싱
- 스트림의 요소를 선택하거나 스킵하기
  - 프레디케이트 이용
  - 처음 몇 개의 요소 무시하기
  - 특정 크기로 스트림을 줄이기

## 5.2.1 프레디케이트를 이용한 슬라이싱
### TAKEWHILE
```java
List<Dish> specialMenu = Array.asList(
  new Dish("seasonal fruit", false, 120, Dish.Type.OTHER),
  new Dish("prawns", false, 140, Dish.Type.OTHER),
  new Dish("rice", false, 350, Dish.Type.OTHER),
  new Dish("french fries", false, 360, Dish.Type.OTHER),
);

List<Dish> filteredMenu = specialMenu.stream()
    .filter(dish -> dish.getCalories() < 320)
    .collect(toList());
```
- 위 리스트는 이미 칼로리 순으로 정렬되어 있다
- filter 이용시 전체 스트림을 반복하면서 각 요소에 프레디케이트를 적용
- 리스트가 이미 정렬되어 있으므로, 320칼로리보다 크거나 같은 요리가 나오면 반복 작업을 중단할 수 있다

```java
List<Dish> sliceMenu1 = specialMenu.stream()
    .takeWhile(dish -> dish.getCalories() < 320)
    .collect(toList());
```
- takeWhile을 이용하면 무한스트림을 포함한 모든 스트림에 프레디케이트를 적용해 스트림을 슬라이스 할 수 있다

### DROPWHILE
```java
List<Dish> sliceMenu2 = specialMenu
  .stream().dropWhile(dish -> dish.getCalories() < 320)
  .collect(toList());
```
- dropWhile은 프레디케이트가 처음으로 거짓이 되는 지점까지 발견된 요소를 버린다
- 프레디케이트가 거짓이 되면 그 지점에서 작업을 중단하고 남은 요소를 리턴한다
- 무한한 남은 요소를 가진 무한 스트림에서도 동작한다

## 5.2.2 스트림 축소
- limit(n)
  - 주어진 값 이하의 크기를 갖는 새로운 스트림을 리턴
  - 정렬되어 있으면 최대 요소 n개 리턴

```java
List<Dish> dishes = specialMenu.stream()
  .filter(dish -> dish.getCalories() > 300)
  .limit(3)
  .collect(toList());
```

- 정렬되지 않은 스트림에도 limit 사용 가능
- 정렬되어 잇지 않다면 limit의 결과도 정렬되지 않은 상태로 리턴

## 5.2.3 요소 건너뛰기
- skip(n)
  - 처음 n개의 요소를 제외한 스트림을 반환
  - n개 이하의 요소를 포함하는 스트림에 skip(n)을 호출하면 빈 스트림 리턴

```java
// 300 칼로리 이상의 처음 두 요리를 건너뛴 다음에 300칼로리라 넘는 나머지 요리를 반환
List<Dish> dishes = menu.stream()
  .filter(d -> d.getCalories() > 300)
  .skip(2)
  .collect(toList());
```


# 5.3 매핑
- 특정 데이터 선택
  - map
  - flatMap

## 5.3.1 스트림의 각 요소에 함수 적용하기
- 스트림은 함수를 인수로 받는 map 메서드를 지원한다
  - 인수로 제공된 함수는 각 요소에 적용
  - 함수를 적용한 결과가 새로운 요소로 매핑
  - 변환에 가가운 매핑

```java
List<String> dishNames = menu.stream()
  .map(Dish::getName)
  .collect(toList());

List<String> words = Arrays.asList("Modern", "Java", "Action");
List<String> wordLengths = words.stream()
  .map(String::length)
  .collect(toList());
```

## 5.3.2 스트림 평면화
- 리스트에서 고유 문자로 이루어진 리스트 리턴하기
- ["Hello", "World"]

#### List<String[]>
```java
List<String[]> temp = words.stream()
  .map(word -> word.split(""))
  .distinct()
  .collect(toList());
```

### map과 Arrays.stream 활용
#### List<Stream<String>>
- 문자열 스트림이 필요하다
```java
String[] arrayOfWords = {"GoodBye", "World"};
Stream<String> streamOfWords = Arrays.stream(arrayOfWorlds);
List<Stream<String>> temp = words.stream()
  .map(word -> word.split(""))
  .map(Arrays::stream)
  .distinct()
  .collect(toList());
```

### flatMap 사용
```java
List<String> uniqueCharacters = words.stream()
  .map(word -> word.split(""))
  .flatMap(Arrays::stream)
  .distinct()
  .collect(toList());
```
- flatMap
  - 각 배열을 스트림이 아니라 스트림의 콘텐츠로 매핑한다
  - 하나의 평면화된 스트림을 리턴한다
  - 스트림의 각 값을 다른 스트림으로 만든 다음에 모든 스트림을 하나의 스트림으로 연결하는 기능을 수행한다

# 5.4 검색과 매칭
- 특정 속성이 데이터 집합에 있는지 여부를 검색하는 데이터 처리
- allMatch, anyMatch, noneMatch, findFirst, findAny ...

## 5.4.1 프레디케이트가 적어도 한 요소와 일치하는지 확인
- anyMatch
  - 불리언을 리턴
  - 최종 연산
```java
if(menu.stream().anyMatch(Dish::isVegetarian)) {
  System.out.println("");
}
```

## 5.4.2 프레디케이트가 모든 요소와 일치하는지 검사
- allMatch
```java
boolean isHealthy = menu.stream().allMatch(dish -> dish.getCalories90 < 1000);
```

- noneMatch
  - 일치하는 요소가 없는것을 확인
```java
boolean isHealthy = menu.stream().noneMatch(d -> d.getCalories() >= 1000);
```

- anyMatch, allMatch, noneMatch
  - 스트림 요트서킷 기법
  - 자바의 && || 와 같은 연산을 활용
  - 전체 스트림을 처리하지 않았더라도 결과 리턴 가능
  - 하나라도 거짓이면 전체 결과가 거짓

## 5.4.3 요소 검색
- findAny
  - 현재 스트림에서 임의의 요소 리턴
```java
Optional<Dish> dish = menu.stream()
  .filter(Dish::isVegetarian)
  .findAny();
```

## 5.4.4 첫 번째 요소 찾기
```java
List<Integer> someNumbers = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> firstSquareDivisibleByThree = someNumbers.stream()
  .map(n -> n * n)
  .filter(n -> n % 3 == 0)
  .findFirst();
```

# 5.5 리듀싱
- 리듀싱 연산
  - 모든 스트림의 요소를 처리해서 값으로 도출하는 것

## 5.5.1 요소의 합
```java
int sum = numbers.stream().reduce(0, (a, b) -> a + b);
int sum = numbers.stream().reduce(0, Integer::sum);
```

- reduce는 두 개의 인수를 갖는다
  - 초깃값 0
  - 두 요소를 조합해서 새로운 값을 만드는 BinaryOperator<T>

### 초깃값 없음
```java
Optional<Integer> sum = numbers.stream().reduce((a, b) -> (a+b));
```
- 합계가 없을 수도 있으므로 Optional로 리턴받는다

## 5.5.2 최댓값과 치솟값

```java
Optional<Integer> max = numbers.stream().reduce(Integer::max);
Optional<Integer> min = numbers.stream().reduce(Integer::min);
```

# 5.7 숫자형 스트림

## 5.7.1 기본형 특화 스트림
1. IntStream
2. DoubleStream
3. LongStream

- 각 인터페이스는 sum, max같은 숫자 관련 리듀싱 연산 수행 메서드 제공
- 다시 객체 스트림으로 복원하는 기능 제공
- 특화 스트림은 박싱 과정에서 일어나는 효율성과 관계 O
- 스트림에 추가 기능 제공 X

### 숫자 스트림으로 매핑
- 숫자 -> 특화 스트림
  - mapToInt, mapToDouble, mapToLong 을 가장 많이 사용한다
  - map과 같은 기능을 수행하지만 `Stream<T>` 대한 특화 스트림을 리턴한다
  - 스트림이 비어있으면 sum은 0을 리턴

```java
int calories = menu.stream()
  .mapToInt(Dish::getCalories)
  .sum();
```

### 객체 스트림으로 복원하기
```java
IntStream intStream = menu.stream().mapToInt(Dish::getCalories);
Stream<Integer> stream = intStream.boxed();
```

### 기본값 : OptionalInt
- 스트림에 요소가 없는 상황과 실제 최댓값이 0인 상황을 구별하기 위해서
- Optionalint, OptionalDouble, OptionalLong

```java
OptionalInt maxCalories = menu.stream()
  .mapToInt(Dish::getCalories)
  .max();

// 값이 없을 때ㄱ기본 최댓값을 명시적으로 설정
int max = maxCalories.orElse(1);
```

## 5.7.2 숫자 범위
- IntStream, LongStream
- 첫번째 인수(시작값) / 두번째 인수(종료값)
- range : 시작값과 종료값이 결과에 포함 X
- rangeClosed : 시작값과 종료값이 결과에 포함 O

```java
IntStream evenNumbers = IntStream.rangeClosed(1, 100).filter(n -> n % 2 == 0);
```


