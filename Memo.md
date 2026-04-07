- #### 스위치(switch)는 모든 종류의 데이터와 다양한 비교 작업을 지원합니다 — 스위치는 정수(integer) 및 동등성 비교로 제한되지 않습니다.

```swift
let vegetable = "red pepper"
switch vegetable {
case "celery":
    print("Add some raisins and make ants on a log.")
case "cucumber", "watercress":
    print("That would make a good tea sandwich.")
case let x where x.hasSuffix("pepper"):
    print("Is it a spicy \(x)?")
default:
    print("Everything tastes good in soup.")
}
// Prints "Is it a spicy red pepper?".
```
<code>*case let x where x.hasSuffix("pepper")*</code></br>
<code>*let x*</code>는 비교를 하는 것이 아니라, 현재 평가 중인 값("red pepper")을 새로운 지역 상수 x에 무조건 대입하겠다는 선언입니다. 이 시점에서 x는 "red pepper"라는 문자열 데이터를 갖게 됩니다.