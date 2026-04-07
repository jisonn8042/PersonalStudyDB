# Swift 둘러보기

Swift에서 이 코드의 라인은 완벽한 프로그램입니다. 문자 출력 또는 문자열 처리와 같은 기능을 위한 별도의 라이브러리를 가져올 필요는 없습니다.</br>
전역 범위로 작성한 코드는 프로그램의 전체에서 사용되기 때문에 main() 함수가 필요하지 않습니다.</br>
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
