# iOS구조 전략

## 데이터 관리 전략

일시적인 화면 값
→ delegate / closure / in-memory store

앱 전체에서 공유되는 현재 상태
→ Store / Repository

앱 종료 후에도 유지되어야 하는 값
→ UserDefaults / DB / CoreData

서버 기준으로 관리되어야 하는 값
→ API + Repository + local cache

### 실제 적용

ViewController: 화면 표시, 버튼 이벤트 처리

Store: 앱 안에서 현재 상태를 들고 있는 객체

- 현재 상태를 메모리에 보관
- 상태를 변경하는 메서드 제공
- 상태가 바뀌면 화면들에게 알림

```swift
import Foundation

extension Notification.Name {
    static let userProfileDidChange = Notification.Name("userProfileDidChange")
}

final class UserProfileStore {

    static let shared = UserProfileStore()

    private init() {}

    private(set) var name: String = "未設定"

    func updateName(_ name: String) {
        self.name = name

        NotificationCenter.default.post(
            name: .userProfileDidChange,
            object: nil,
            userInfo: ["name": name]
        )
    }
}
```

UserProfileStore가 현재 사용자 이름을 가지고 있다.

이름을 바꾸려면 updateName()을 통해서만 바꾼다.

이름이 바뀌면 NotificationCenter로 알린다.


Repository: 데이터 출처를 숨기고, 저장/조회 규칙을 관리하는 객체

- 데이터를 저장한다
- 데이터를 불러온다
- 데이터 출처를 감춘다
- API / DB / UserDefaults 접근을 한 곳에 모은다
- ViewController가 저장 방식에 의존하지 않게 한다

```swift
import Foundation

final class UserProfileRepository {

    static let shared = UserProfileRepository()

    private init() {}

    private let nameKey = "user_name"

    func saveName(_ name: String) {
        UserDefaults.standard.set(name, forKey: nameKey)
    }

    func loadName() -> String {
        return UserDefaults.standard.string(forKey: nameKey) ?? "未設定"
    }
}
```


DB / UserDefaults / API: 실제 데이터가 저장되거나 불러와지는 장소

Observer / Notification: 데이터 변경 사실을 여러 화면에 알리는 통로

ViewController</br>
    ↓</br>
Store / Repository</br>
    ↓</br>
UserDefaults / SQLite / CoreData / API


Store와 Repository를 같이 사용한 구조

EditNameViewController</br>
        ↓</br>
UserProfileStore.updateName()</br>
        ↓</br>
UserProfileRepository.saveName()</br>
        ↓</br>
UserDefaults / DB / API</br>
        ↓</br>
NotificationCenter로 변경 알림</br>
        ↓</br>
여러 ViewController 갱신

사용자 입력
    ↓
ViewController
    ↓
Store
    ↓
Repository
    ↓
DB / UserDefaults / API
    ↓
Store 상태 변경
    ↓
Observer로 화면 갱신

Repository
→ 저장/조회 담당

Store
→ 현재 상태 보관 + 변경 알림 담당

이경우의 ViewController

```swift
//EditNameViewController

final class EditNameViewController: UIViewController {

    @IBOutlet weak var nameTextField: UITextField!

    @IBAction func saveButtonTapped(_ sender: UIButton) {
        let name = nameTextField.text ?? ""

        UserProfileStore.shared.updateName(name)

        navigationController?.popViewController(animated: true)
    }
}
```

```swift
//MainViewController

final class MainViewController: UIViewController {

    @IBOutlet weak var nameLabel: UILabel!

    override func viewDidLoad() {
        super.viewDidLoad()

        render()

        NotificationCenter.default.addObserver(
            self,
            selector: #selector(userProfileDidChange(_:)),
            name: .userProfileDidChange,
            object: nil
        )
    }

    private func render() {
        nameLabel.text = UserProfileStore.shared.name
    }

    @objc private func userProfileDidChange(_ notification: Notification) {
        render()
    }

    deinit {
        NotificationCenter.default.removeObserver(self)
    }
}
```

## 화면 전환 전략

Stack 방식: UINavigationController로 화면을 쌓음

Root 교체 방식: 현재 앱의 시작 화면 자체를 바꿈

Tab 방식: 여러 흐름을 병렬로 나눔

Modal 방식: 임시 작업 화면을 띄움

Container 방식: 하나의 부모 화면 안에서 자식 화면만 교체

State 기반 방식: “현재 상태가 무엇인가”에 따라 보여줄 화면을 결정

Coordinator 방식: 화면 전환 책임을 ViewController 밖으로 분리