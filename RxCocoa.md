## RxCocoa
- Cocoa Framework의 애플리케이션 개발 도구들에 Rx를 적용시킨 라이브러리

### Binder
- Publisher에서 Subscriber로 향하는 단방향 Binding
- bind
  - subscribe와 동일한 기능을 수행
  - 항상 메인스레드에서 수행되기 때문에, UI 작업을 처리할 때 특별히 스레드 관리를 해주지 않아도 된다는 장점이 있음
```swift
// 간단한 bind 예제

@IBOutlet weak var label: UILabel!
@IBOutlet weak var button: UIButton!

let disposeBag = DisposeBag()

override func viewDidLoad() {
  super.viewDidLoad()
  
  // button을 Tap할 경우, label의 text가 async하게 "Hello, Swift!"로 변경됨
  button.rx.tap
    .map { "Hello, Swift!" }
    .bind(label.rx.text)
    .disposed(by: disposeBag)
}
```
```swift
// CollectionView 응용 예제

// ** UICollectionViewDelegate, UICollectionViewDataSource 생략 **
class ViewController: UIViewController { 
  @IBOutlet weak var collectionView: UICollectionView!
  
  let disposeBag = DisposeBag()
  let viewModel = ViewModel()
  
  override func viewDidLoad() {
    super.viewDidLoad()
    bindCollectionView()
  }
  
  private func bindCollectionView() {
    // 구현해둔 viewModel의 Observable<UIImage?> 타입인 image 프로퍼티에 접근
    // collectionView.rx.items(cellIdentifier: String, cellType: Cell.Type)를 활용한 collectionView 바인딩
    viewModel.image.bind(to: collectionView.rx.items(cellIdentifier: "customCell", cellType: CustomCollectionViewCell.self)) { index, element, cell in
      cell.imageView.image = element
    }
    .disposed(by: disposeBag)
  }
}

```

