## RxRelay

### Relay
- Subject의 Wrapper 클래스
- next가 아닌 accept를 이용해 이벤트를 발생
- error, completed 이벤트가 발생하지 않기 때문에 이벤트로 인해 종료되지 않는다.
- 위와 같은 특징으로 인해 UI 작업을 요하는 부분에서 많이 사용
- .value를 통해서 현재값을 get하는 것이 가능

#### Relay의 종류
- PublishRelay
  - PublishSubject와 동일하게 동작
```swift
let disposeBag = DisposeBag()
let relay = PublishRelay<Int>()
relay.accept(1)

relay
  .subscribe(onNext: { num in
  print(num)
  })
  .disposed(by: disposeBag)
  
relay.accept(2)
relay.accept(3)

// 2
// 3
```
- BehaviorRelay
  - BehaviorSubject와 동일하게 동작
```swift
let disposeBag = DisposeBag()
let relay = BehaviorRelay<Int>(value: 0)
relay.accept(1)

relay
  .subscribe(onNext: { num in
  print(num)
  })
  .disposed(by: disposeBag)
  
relay.accept(2)
relay.accept(3)

// 1
// 2
// 3
```
