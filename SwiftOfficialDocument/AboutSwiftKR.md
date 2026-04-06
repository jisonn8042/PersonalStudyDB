# Swift에 대해

<details>
<summary><h2 style="display: inline">변수는 항상 사용 전에 초기화 되어야 합니다.</h2></summary>
</br>
C/C++ 같은 언어에서는 변수를 선언만 하고 초기화하지 않을 경우, 해당 메모리 주소에 남아있던 무의미한 값이 그대로 할당되어 사용될 위험이 있습니다.</br>
Swift 컴파일러는 변수가 명확한 값으로 초기화되기 전에 메모리를 읽으려는 시도를 허용하지 않으며, 이를 시도할 경우 컴파일 에러를 발생시켜 예측 불가능한 동작을 런타임 전에 방지합니다.
</details>

</br>

<details>
<summary><h2 style="display: inline">배열(Array) 인덱스는 범위 초과(out-of-bounds) 오류에 대해 검사되어야 합니다.</h2></summary>
</br>
할당된 배열의 크기를 벗어난 인덱스에 접근할 때, 시스템이 이를 무시하고 엉뚱한 메모리 영역을 건드리게 되면 심각한 데이터 오염이나 보안 취약점(Buffer Overflow)으로 이어집니다.</br>
Swift는 인덱스 접근 시 항상 유효 범위를 검사하며, 범위를 초과하면 런타임 에러(Fatal error)를 발생시켜 프로세스를 즉시 강제 종료합니다.</br>
잘못된 메모리를 참조한 상태로 오작동을 이어가는 것보다 즉각적인 실행 중단이 시스템 무결성 유지에 안전하기 때문입니다.
</details>

</br>

<details>
<summary><h2 style="display: inline">정수(Integer)는 오버플로우(overflow)에 대해 검사되어야 합니다.</h2></summary>
</br>
정수 타입(예: <code>Int8</code>)이 표현할 수 있는 최대치나 최소치를 넘어서는 연산을 수행할 때, 기존 언어들은 값이 순환하여 예상치 못한 음수나 양수로 바뀌는 현상(Wrap-around)을 암묵적으로 허용했습니다. Swift는 이러한 오버플로우 발생 시 즉시 에러를 발생시킵니다.</br>
데이터 손실이나 치명적인 계산 오류를 막기 위함이며, 만약 의도적인 순환 연산이 필요하다면 <code>&+</code>, <code>&-</code>와 같은 오버플로우 허용 연산자를 명시적으로 사용하도록 강제합니다.
</details>

</br>

<details>
<summary><h2 style="display: inline">옵셔널(Optional)은 nil 값이 명시적으로 처리되도록 합니다.</h2></summary>
</br>
소프트웨어 크래시의 가장 흔한 원인은 '값이 없음(Null)'을 가리키는 메모리에 접근하는 것입니다.</br>
Swift는 값이 존재하지 않을 수 있는 상태를 Optional이라는 별도의 타입으로 완전히 분리했습니다.</br>
옵셔널에 담긴 값을 사용하려면 컴파일러의 통제에 따라 반드시 <code>nil</code> 여부를 검사하고 정상적인 값으로 추출(Unwrapping)하는 로직을 거쳐야만 합니다.</br>
이를 통해 Null Pointer Exception의 발생 가능성을 컴파일 단계에서 완벽히 통제합니다.
</details>

</br>

<details>
<summary><h2 style="display: inline">메모리는 자동으로 관리됩니다.</h2></summary>
</br>
<b>C/C++과 같은 저수준 언어</b>에서는 개발자가 직접 메모리를 할당(malloc)하고 해제(free)해야 했습니다.</br>
이 과정을 잊거나 실수하면 메모리 누수(Memory Leak)나 해제된 메모리 접근(Use After Free) 같은 심각한 버그가 발생했습니다.</br>
<b>Swift는</b> ARC(Automatic Reference Counting) 시스템을 사용하여 힙(Heap) 영역의 메모리를 관리합니다.</br>
객체(인스턴스)가 생성될 때 참조 횟수를 추적하고, 객체를 참조하는 변수가 없어져 카운트가 0이 되는 순간 해당 메모리를 자동으로 즉시 해제합니다.</br>
개발자가 명시적으로 메모리를 할당/해제할 때 흔히 발생하는 메모리 누수(Memory Leak)나 해제된 메모리를 다시 참조하는 현상을 시스템 차원에서 해결합니다.
</details>

</br>

<details>
<summary><h2 style="display: inline">오류 처리를 통해 예기치 않은 오류를 처리할 수 있습니다.</h2></summary>
</br>
네트워크 통신이나 파일 입출력 등 실행 중 실패할 가능성이 존재하는 작업에 대해, Swift는 <code>throws</code>, <code>try</code>, <code>catch</code> 문법을 강제합니다.</br>
오류 발생 가능성이 있는 함수는 반드시 에러를 던질 수 있다고 선언해야 하며, 이를 호출하는 쪽에서는 발생 가능한 예외 상황을 처리하는 코드를 필수적으로 작성해야 합니다.</br>
개발자가 에러 상황을 묵인하거나 간과하는 것을 컴파일러가 허용하지 않으므로 견고한 아키텍처 구성이 강제됩니다.
</details>

</br>

<details>
<summary><h2 style="display: inline">직관적이고 간결한 문법: 타 언어 경험자가 빠르게 적응할 수 있는 경량화된 구문 제공</h2></summary>
</br>
기존 C나 Objective-C에서 요구되던 복잡한 헤더 파일 구조나 불필요한 세미콜론(;) 등을 배제하고, Python이나 JavaScript처럼 직관적이고 보일러플레이트(Boilerplate) 코드를 최소화한 문법 구조를 채택했다는 의미입니다.
</details>

</br>

<details>
<summary><h2 style="display: inline">고급 언어 기능 내장: 타입 추론(Type inference) 및 패턴 매칭(Pattern matching) 지원</h2></summary>
</br>
<ul>
<li><b>타입 추론 (Type inference):</b> 변수나 상수를 선언할 때 할당되는 값을 바탕으로 컴파일러가 스스로 자료형을 결정하는 기능입니다. 개발자가 불필요하게 타입을 반복 명시할 필요가 없어 코드가 압축됩니다.</li>
<li><b>패턴 매칭 (Pattern Matching):</b> 단순히 두 값이 같은지 비교(<code>==</code>)하는 것을 넘어, 데이터의 '형태(Shape)', '타입(Type)', '내부 구조(Structure)' 등 다양한 조건을 템플릿(패턴)과 대조하고, 일치할 경우 내부의 값을 안전하게 추출(Binding)하여 흐름을 제어하는 문법적 메커니즘입니다. 예를 들어 API 응답 결과를 담은 Result 열거형을 처리할 때, 성공/실패 여부뿐만 아니라 내부에 연관 값(Associated Value)으로 담긴 실제 데이터나 에러 코드를 <code>switch</code> 또는 <code>if case let</code> 문을 통해 단 한 번의 조건식으로 분해하고 변수에 할당할 수 있습니다.</li>
</ul>
</details>

</br>

<details>
<summary><h2 style="display: inline">표현의 효율성 극대화: 복잡한 비즈니스 로직이나 아이디어를 명확하고 간결하게 구현</h2></summary>
</br>
단순히 타이핑을 적게 한다는 의미를 넘어, 문법적 제약과 강력한 기능의 조화를 통해 타인이 작성한 코드를 빠르게 이해하고(Read), 논리적 오류를 최소화하며 코드를 구축하고(Write), 향후 요구사항 변경이나 버그 수정 시 파급 효과를 통제하기 쉬운(Maintain) 안정적인 코드베이스를 형성한다는 소프트웨어 공학적 장점을 시사합니다.
</details>
