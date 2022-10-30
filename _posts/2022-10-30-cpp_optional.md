---
layout: post
title: std::optional
subtitle:
categories: c++
tags: [c++, c++17, stl, std::optional, optional]
---

### Motivation

개발을 하다보면 `유효하지 않은 값`을 표현하는 방식에 대해 고민하게 된다.

가장 쉬운 방법은 유효하지 않은 값을 약속하고 해당 값을 대입하는 방식이다.
이를테면, 음수가 될 수 없는 변수에 음수 값이 대입되어 있으면 이 값을 유효하지 않다고 판단하면 된다.

```cpp
double distance = ComputeDistanceToSomething();
if (distance < 0.0) {
  std::cout << "Distance is invalid" << std::endl;
  return;
}
```

그러나 이 방식은 `유효하지 않은값`에 대한 개발자 간의 약속이 필요한 경우가 많고, 코드의 가독성이 저해될 우려가 있다.
또한 이런 방식은 모든 형식의 변수에 대입해서 사용할 수는 없다.

별도의 `bool` 변수로 값의 유효성을 표현한다면 예외 없이 모든 변수에 유효성을 표현할 수 있다.

```cpp
struct Point {
  double x;
  double y;
};

std::pair<Point, bool> location = GetLocationOfSomething();
if (!location->second) {
  std::cout << "Location is invalid" << std::endl;
  return;
}
```

그러나 이러한 경우의 `std::pair`의 사용은 코드 자체만으로 개발자의 의도를 이해하기 어렵기 때문에 코드의 가독성이 크게 떨어진다.
물론, bool 변수를 별도의 멤버변수를 가지는 struct를 정의할 수도 있다.

```cpp
struct PointWithValidity {
  bool valid;
  Point point;
};
```

유효성 판단이 필요한 모든 변수를 위해 별도의 type definition이 추가되는 방식 역시 바람직해 보이지는 않는다.

### std::optional<T>

c++17 부터 이러한 상황에 사용할 수 있는 `vocabulary type`인 `std::optional<T>`이 추가되었다.
`std::optional<T>`은 위의 `std::pair<T, bool>`과 비슷한 역할을 할 수 있으나 instance construction에 필요한 시간을 절약하는 효과가 있고, 무엇보다도 코드의 가독성을 크게 개선해준다.

간단한 예제로 `std::optional`의 기본적인 사용법을 살펴보자.

```cpp
#include <optional>

std::optional<Point> GetLocation(const std::string& target){
  if (!CanFindTarget(target))
    return std::nullopt;

  Point location = location_list_[target];
  return location;
}

auto a_location = GetLocation("a");
// has_value() 함수로 값의 유효성을 판단한다.
if (a_location.has_value()) {
  // 포인터와 같은 방식으로 instance에 접근 가능하다.
  std::cout << "x=" << a_location->x << ", y=" << a_location.value().y << std::endl;
} else {
  std::cout << "Can't find target a" << std::endl;
}
```

위의 코드는 a라는 타겟을 찾을 수 있을 경우 a의 위치를 출력해주고, 그렇지 못할 경우 에러를 출력한다.

```cpp
std::optional<Point>
```

`std::optional`은 템플릿 인자로 보관하고자 하는 객체의 타입을 입력받는다.
이렇게 생성된 optional 객체는 `Point`를 보관하고 있거나, 아무 것도 가지고 있지 않은(`nullptr`) 상태가 된다.

```cpp
return std::nullopt;
```

`std::nullopt`은 미리 정의되어 있는 빈 `optional` 객체를 의미한다.
유효하지 않은 객체를 표현하고자 한다면 `optional` 객체에 `std::nullopt`를 대입하면 된다.

```cpp
Point location = location_list_[target];
return location;
```

값이 유효할 경우, `optional` 타입으로의 변환없이 해당 객체를 바로 대입해줘도 된다.
`optional`의 생성자에 보관하고자 하는 타입을 받는 생성자가 정의되어 있기 때문에 자동으로 `optional`객체가 생성된다.

```cpp
if (a_location.has_value())
```

`optional` 객체의 유효성은 has_value() 함수로 판단 가능하다.
이 값이 true일 경우 보관하는 객체가 있는 상태이며, false일 경우 보관하는 객체가 없는(`nullopt`) 상태이다.

WIP
