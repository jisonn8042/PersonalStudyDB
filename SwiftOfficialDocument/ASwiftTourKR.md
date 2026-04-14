# Swift 둘러보기

Swift에서 이 코드의 라인은 완벽한 프로그램입니다. 문자 출력 또는 문자열 처리와 같은 기능을 위한 별도의 라이브러리를 가져올 필요는 없습니다.</br>
전역 범위로 작성한 코드는 프로그램의 전체에서 사용되기 때문에 main() 함수가 필요하지 않습니다.</br>
모든 구문에 끝에 세미콜론을 작성할 필요도 없습니다.

## 세부설명

### 1. "별도의 라이브러리를 가져올 필요는 없습니다." (표준 라이브러리 내장)
C언어의 <code>#include <stdio.h></code>나 C++의 <code>#include <iostream></code>과 같이, 기존 언어들은 콘솔에 텍스트를 출력하는 단순한 작업을 위해서도 입출력 라이브러리를 명시적으로 선언해야 했습니다.</br>
반면, Swift 컴파일러는 <code>String</code>, <code>Int</code>, <code>Array</code>와 같은 기본 자료형과 <code>print()</code> 등의 핵심 함수가 포함된 'Swift 표준 라이브러리(Standard Library)'를 모든 소스 파일에 암시적으로(Implicitly) 임포트합니다.</br>
이는 개발자의 타이핑을 줄일 뿐만 아니라, 언어의 기본 구성 요소에 대한 접근성을 높여줍니다. (단, UI 구성이나 네트워킹 같은 확장 기능은 여전히 <code>import UIKit</code> 또는 <code>import Foundation</code> 등으로 명시해야 합니다.)

### 2. "main() 함수가 필요하지 않습니다." (진입점의 추상화)

#### 2-1. 운영체제가 요구하는 것 (진입점의 필요성)
macOS, Linux 등 대부분의 운영체제는 프로그램을 실행할 때 메모리에 프로세스를 할당한 후, 바이너리 내부에 약속된 특정 위치부터 CPU 명령어를 읽어 들입니다. 이 시작점을 진입점(Entry Point)이라고 부르며, 운영체제(정확히는 동적 링커/로더)는 관습적으로 C 언어 규격의 main 함수(어셈블리 레벨에서는 <code>_main</code> 심볼)를 찾아서 실행하도록 설계되어 있습니다.</br>
만약 이 규격을 따르지 않고 진입점이 없는 바이너리를 만들면 운영체제는 "어디서부터 프로그램을 실행해야 할지 모른다"며 실행을 거부합니다.

#### 2-2.  컴파일러의 래핑(Wrapping) 및 합성 과정
개발자가 <code>main.swift</code> 파일의 전역 범위에 아래와 같이 단 한 줄의 코드를 작성했다고 가정합니다.

```swift
//main.swift
print("Hello, World!")
```

이 코드가 기계어로 번역될 때, Swift 컴파일러(swiftc)의 프론트엔드는 이 코드를 그대로 기계어로 바꾸는 것이 아니라, 내부적으로 다음과 같은 형태의 C 언어 호환 구조로 코드를 변환(Wrapping)합니다.

```c
// 컴파일러가 내부적으로 합성한(Synthesized) 실제 코드의 형태@_cdecl("main")
func main(_ argc: Int32, _ argv: UnsafeMutablePointer<UnsafeMutablePointer<Int8>?>) -> Int32 {
    // 1. 전역 변수 등의 초기화 작업 수행
// 2. 개발자가 main.swift 전역에 작성한 코드 삽입
print("Hello, System!")
    
// 3. 정상 종료(0) 반환
return 0
}
```

컴파일러는 main.swift에 흩어져 있는 전역 변수 선언, 함수 호출, 반복문 등의 로직을 모조리 수집하여 자신이 몰래 만든 main 함수의 중괄호 { } 내부에 순서대로 밀어 넣습니다.

#### 2-3. 이것이 의미하는 공학적 가치
C, C++, Java 등의 언어에서는 이 진입점을 만들기 위해 개발자가 직접 클래스를 정의하고 <code>public static void main(String[] args)</code>나 <code>int main(void)</code> 같은 보일러플레이트(Boilerplate) 코드를 의무적으로 작성해야 했습니다.</br>
Swift는 이 반복적이고 기계적인 저수준 작업을 컴파일러에게 전가했습니다. 그 결과, 개발자는 불필요한 시스템 규약을 신경 쓸 필요 없이 핵심 로직에만 집중할 수 있게 되었으며, 스크립트 언어(Python, JS 등)처럼 작성 즉시 위에서 아래로 실행되는 직관적인 흐름을 통제할 수 있게 된 것입니다.

### 3. "세미콜론을 작성할 필요도 없습니다." (구문 종료 인식의 자동화)
C, C++, Java, JavaScript 등 많은 프로그래밍 언어에서 세미콜론(;)은 '이 문장은 여기서 끝났다'는 것을 명시적으로 알리는 역할을 합니다.</br>
이 기호가 없으면 컴파일러는 문장의 끝을 정확히 판단하지 못해 문법 오류를 일으킵니다. 따라서 개발자는 한 줄에 여러 문장을 쓰거나 코드를 편집할 때마다 세미콜론을 빠뜨리지 않도록 주의해야 하는 번거로움이 있었습니다.</br>
Swift는 줄바꿈(Newline)을 문장의 자연스러운 구분자로 인식합니다. 즉, 한 줄에 하나의 구문만 작성한다면 세미콜론을 생략해도 컴파일러가 줄바꿈을 통해 문장의 끝을 완벽하게 파악할 수 있습니다.</br>
이러한 설계 덕분에 개발자는 문법적 강박에서 벗어나 코드의 가독성과 작성 속도에만 집중할 수 있게 됩니다. (물론, 한 줄에 여러 개의 구문을 작성하고 싶다면 C 계열 언어처럼 세미콜론을 사용하여 구분할 수도 있습니다.)

## 간단한 값

### 상수 - let, 변수 - var
- #### 상수는 컴파일 때 알 필요는 없지만 반드시 한번은 할당 해야 합니다.(값의 할당을 미룰수 있음)
```swift
let serverResponse: String

// 특정 조건에 따라 런타임에 최초 1회 값을 할당합니다.
if isNetworkConnected {
    serverResponse = "Connected"
} else {
    serverResponse = "Disconnected"
}
```

- #### 값할당시 할당하려는 값과 동일한 타입이어야합니다
초기값이 충분한 정보를 제공하지 않을 경우 변수뒤에 콜론으로 구분하여 타입지정

```swift
let implictInteger = 70
let implictDouble = 70.0
let explicitDouble: Double = 70
```

- #### 값은 다른 타입의 값으로 절대 변경되지 않습니다. 
값을 다른 타입으로 변경해야 한다면 원하는 타입의 인스턴스를 명시적으로 만들어야 합니다.

```swift
let pi = 3.14159
let piDouble = pi + 0.01
let piFloat = pi + 0.01 // 오류: Double을 Float으로 변환할 수 없습니다.
let piFloatExplicit: Float = Float(pi) + 0.01 // 명시적 변환으로 해결
```

- #### 문자열에 값을 포함하는 방법
소괄호 안에 값ㅇ르 작성하고 소관호 전에 역슬래시(\)를 작성하면 됩니다.

```swift
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
```

여러줄의 문자열은 쌍따옴표3개(""") 닫는 따옴표의 들여쓰기와 일치하는 한 각 인용된 줄의 시작부분에 있는 들여쓰기는 제거됩니다.

```swift
let quotation = """
        Even though there's whitespace to the left,
        the actual lines aren't indented.
            Except for this line.
        Double quotes (") can appear without being escaped.

        I still have \(apples + oranges) pieces of fruit.
        """
```

### 배열 - [], 딕셔너리 - [:]
- #### 대괄호([ ])를 사용하여 배열과 딕셔너리를 생성하고 대괄호에 인덱스 또는 키를 작성하여 해당요소에 접근할 수 있습니다.</br>마지막 요소뒤에 쉽표도 허용 합니다.

```swift
var fruits = ["strawberries", "limes", "tangerines"]
fruits[1] = "grapes"

var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
]
occupations["Jayne"] = "Publi Relations"
```
- ##### 새로운 변수 또는 다른 장소의 타입 정보가 없는 곳에 빈 배열 또는 빈 딕셔너리를 할당하려면 타입을 명시해야 합니다.

```swift
let emptyArray: [String] = []
let emptyDictionary: [String: Float] = [:]
```

- #### 배열은 요소를 추가함에 따라 자동으로 크기가 늘어납니다.

```swift
fruits.append("blueberries")
print(fruits)
// Prints "["strawberries", "grapes", "tangerines", "blueberries"]".
```

- #### 빈 배열 또는 딕셔너리를 작성할 때도 괄호를 사용합니다. 배열을 작성할 때는 [], 딕셔너리는 [:]로 작성합니다.

```swift
fruits = []
occupations = [:]
```

## 제어 흐름(Control Flow)

### 조건문 - if, switch, 루프 - for-in, while, repeat-while

- #### 조건문이나 루프 변수를 둘러싼 소괄호는 선택 사항 입니다. 본문을 둘러싼 중괄호는 필수사항 입니다.

```swift
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in individualScores {
    if score > 50 {
        teamScore += 3
    } else {
        teamScore += 1
    }
}
print(teamScore)
// Prints "11".
```
- #### if 문에서는 조건부가 반드시 Boolean으로 표현되어야 합니다</br>if score { ... }와 같은 코드는 암시적으로 0과 비교하는 것이 아니라 오류를 의미합니다.

- #### 🌟조건을 기준으로 값을 선택하기위해 할당의 동등 사인(=) 뒤나 return 뒤에 if 또는 switch를 작성할 수 있습니다.

```swift
let scoreDecoration = if teamScore > 10 {
    "🎉"
} else {
    ""
}
print("Score", teamScore, scoreDecoration)
  // Prints "Score: 11🎉".
```

<b>1. 구문(Statement)과 표현식(Expression)의 본질적 차이</b>

프로그래밍에서 '구문'은 작업을 수행하라는 명령(예: 반복해라, 분기해라)이며 자체적인 값을 가지지 않습니다.</br>
반면 '표현식'은 평가(Evaluation) 과정을 거쳐 단일한 '값'으로 치환되는 코드입니다.</br>
과거의 if와 switch는 오직 구문으로만 동작했으나, 이제는 컴파일러가 블록 내부의 결과를 하나의 값으로 치환하는 표현식으로 처리할 수 있게 되었습니다.

<b>2. 할당의 동등 사인(=) 뒤에 작성 (직접 할당)</b>

과거에는 조건에 따라 최종 값을 결정하려면 가변 변수(var)를 선언해야 했습니다.</br>
이는 변수의 상태가 블록 외부에서도 예기치 않게 변경될 수 있는 부작용을 내포합니다.

<b>과거 방식 (가변 변수 사용)</b>

```swift
var networkStatus: String // var 선언 강제
if statusCode == 200 {
    networkStatus = "Success"
} else {
    networkStatus = "Failure"
}
```
<code>if</code>나 <code>switch</code>를 할당 연산자(=) 우측에 직접 배치할 수 있습니다.</br>
블록 내부의 마지막 줄이 해당 블록의 결과값으로 평가되어 좌측의 상수(let)에 즉각 할당됩니다.

<b>현대적 방식 (할당 사인 뒤에 if 작성)</b>

```swift
let networkStatus = if statusCode == 200 {
    "Success"
} else {
    "Failure"
}
```
상태 변경(Mutation)을 원천 차단하여 코드의 예측 가능성을 높입니다. 복잡한 삼항 연산자(<code>조건 ? 참 : 거짓</code>)를 사용하지 않고도 가독성과 안정성을 동시에 확보하는 엔지니어링적 최적화입니다.

<b>3. return 뒤에 작성 (직접 반환)</b>

함수나 연산 프로퍼티 내에서 조건에 따른 결과를 반환할 때, 각 분기마다 return 키워드를 기계적으로 중복 작성할 필요가 없어졌습니다.

<b>과거 방식 (반복적 return 사용)</b>

```swift
func getDirection(from degree: Int) -> String {
    switch degree {
    case 0...90: 
        return "1사분면" // 분기마다 return 명시
    case 91...180: 
        return "2사분면"
    default: 
        return "기타"
    }
}
```

<b>현대적 방식 (return 뒤에 switch 작성)</b>

```swift
func getDirection(from degree: Int) -> String {
    return switch degree { // return 키워드 뒤에 switch를 직접 작성
    case 0...90: "1사분면"
    case 91...180: "2사분면"
    default: "기타"
    }
}
```
switch 블록 전체가 거대한 하나의 값으로 평가되므로, 가장 바깥쪽에서 단 한 번의 <code>return</code>으로 전체 구조를 반환할 수 있습니다. 코드의 목적이 "특정 값을 반환하는 것"임이 명확해집니다.

- #### 🌟"값이 없을 수도 있는 상황을 처리하기 위해 if와 let을 함께 사용할 수 있습니다. 이러한 값은 옵셔널(optional)로 표현됩니다. 옵셔널 값은 실제 값을 포함하고 있거나, 값이 없음을 나타내는 nil을 포함합니다. 값의 타입 뒤에 물음표(?)를 작성하여 해당 값을 옵셔널로 지정합니다."(if let 구문)

```swift
var optionalString: String? = "Hello"
print(optionalString == nil)
// Prints "false".


var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
}
```

Swift의 if let 구문은 옵셔널(Optional) 변수의 값이 nil인지 아닌지 검사하여, nil이 아닐 때만 안전하게 내부 값을 언래핑(Unwrapping)해 사용하는 옵셔널 바인딩(Optional Binding) 방식입니다.</br>
if 문 내에서만 유효한 지역변수로 값을 꺼내며, nil일 경우 else 블록을 수행합니다.</br>
옵셔널 값을 처리하는 다른 방법은 ?? 연산자를 사용하여 기본값을 제공하는 것입니다. 옵셔널 값이 없다면 기본값이 대신 사용됩니다.

```swift
let nickname: String? = nil
let fullName: String = "John Appleseed"
let informalGreeting = "Hi \(nickname ?? fullName)"
```

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

*default가 생략 가능한 유일한 상황</br>
평가 대상이 가질 수 있는 경우의 수가 유한하고, 이를 case로 100% 명시했다면 default 블록은 작성할 필요가 없으며, 오히려 작성해서는 안 됩니다. 가장 대표적인 사례가 열거형(Enum)입니다.*

```swift
enum Direction { case north, south, east, west }
let dir = Direction.north

// 모든 케이스를 처리했으므로 default가 없어도 컴파일 성공
switch dir {
case .north: print("북")
case .south: print("남")
case .east: print("동")
case .west: print("서")
}
```

- #### for-in을 사용하여 각 키-값 쌍에 사용할 이름의 쌍을 제공하여 딕셔너리의 항목을 조회합니다. 딕셔너리는 순서가 없는 컬렉션(collection)이므로 키와 값은 임의의 순서로 조회됩니다.

```swift
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]
var largest = 0
for (_, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
    }
}
print(largest)
// Prints "25".
```

딕셔너리를 순회할 때마다 하나의 요소를 가져와, 컴파일러가 알아서 첫 번째 요소는 userName 상수에, 두 번째 요소는 score 상수에 안전하게 할당(Binding)합니다.

```swift
let userScores = ["Alice": 85, "Bob": 92, "Charlie": 78]

// (key, value) 형태의 튜플 패턴 매칭을 통한 데이터 해체 및 바인딩
for (userName, score) in userScores {
    print("\(userName): \(score)")
}
```

🌟코드를 작성할 때 딕셔너리에 데이터를 A, B, C 순서로 삽입했다고 해서, 화면 출력이나 데이터 처리도 A, B, C 순서로 진행된다는 보장은 없습니다.

순서가 필요하다면 딕셔너리의 키나 값을 추출하여 명시적으로 정렬을 수행한 뒤, 순서가 보장되는 배열(Array) 형태로 변환하여 순회해야 합니다.

```swift
// 순서를 보장하기 위한 명시적 정렬 처리
for userName in userScores.keys.sorted() {
    print("\(userName): \(userScores[userName]!)") // 항상 딕셔너리 키의 알파벳 순서대로 출력됨
}
```

- #### 조건이 바뀔 때까지 코드의 블록을 반복하려면 while을 사용해야 합니다. 대신 루프의 조건이 끝에 있을 수 있으므로 적어도 한번은 루프가 실행되도록 합니다.

*번역이 헷갈리게 된거 같다.</br>
아마 이내용을 얘기하고 싶었던거 같다.</br>*
while은 조건을 평가한 후 루프 본문을 실행합니다.</br>
반면 repeat-while 루프는 루프 본문을 실행한 후 조건을 평가합니다.

```swift
var n = 2
while n < 100 {
    n *= 2
}
print(n)
// Prints "128".


var m = 2
repeat {
    m *= 2
} while m < 100
print(m)
// Prints "128".
```

- #### 인덱스의 범위를 만들기 위해선 ..<을 사용하여 루프에 인덱스를 만들 수 있습니다.

가장 상위 값을 생략하는 범위를 만들기 위해 ..<을 사용하고 포함하려면 ...을 사용합니다.

```swift
var total = 0
for i in 0..<4 {
    total += i
}
print(total)
// Prints "6".
```

</br>

## 함수와 클로저 (Functions and Closures)

- #### 함수를 선언하려면 func을 사용합니다. 소괄호 안에 인자의 리스트와 함수의 이름으로 호출합니다. 함수의 반환 타입(return type)에서 매개변수(parameter) 이름과 타입을 구분하기 위해 -> 을 사용합니다.

*번역이 헷갈리게 된거 같다.</br>
아마 이내용을 얘기하고 싶었던거 같다.</br>*
함수를 선언할 때는 func 키워드를 사용합니다. 함수를 호출할 때는 함수 이름 바로 뒤에 소괄호를 붙이고, 그 안에 인자(Argument) 목록을 작성합니다. 함수의 매개변수(입력) 영역과 반환 타입(출력) 영역을 명확히 분리하기 위해 -> 기호를 사용합니다.

```swift
func greet(person: String, day: String) -> String {
  return "Hello \(person), today is \(day)."
}
greet(person: "Bob", day: "Tuesday")
```


## 객체와 클래스 (Objects and Classes)

## 열거형과 구조체 (Enumerations and Structures)  

## 동시성 (Concurrency)

## 프로토콜과 확장 (Protocols and Extensions)

## 오류 처리 (Error Handling)

## 제네릭 (Generics)
