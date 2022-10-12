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



