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
*C, C++, Java 등 과거의 언어들에서는 switch 구문에 default를 생략해도 정상적으로 실행됩니다. </br>
만약 일치하는 case가 없다면, 시스템은 아무 작업도 수행하지 않고 조건문을 조용히 빠져나갑니다.</br>
Swift는 이를 심각한 시스템 결함 유발 요소로 간주합니다. 평가 대상이 가질 수 있는 모든 범위에 대해 명시적인 대처가 없다면, 컴파일러는 다음과 같이 명확하게 틀렸다고 지적하고 수정을 요구합니다.*

```
case let x where x.hasSuffix("pepper")
```
<code>*let x*</code>는 비교를 하는 것이 아니라, 현재 평가 중인 값("red pepper")을 새로운 지역 상수 x에 무조건 대입하겠다는 선언입니다. 이 시점에서 x는 "red pepper"라는 문자열 데이터를 갖게 됩니다.