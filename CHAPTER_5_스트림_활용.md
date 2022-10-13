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