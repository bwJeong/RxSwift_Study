# RxSwift_Study

## RxSwift

### Observable
- 이벤트(next, error, completed)들을 비동기적으로 생성하는 기능
- 이벤트의 종류 및 생명주기
  - next: 최신/다음 데이터를 전달하는 이벤트
  - error: 에러 발생 이벤트, 더 이상의 이벤트를 발생시키지 않으며 Observable 종료(**completed 이벤트를 발생시키지 않는다!**)
  - completed: 성공적으로 종료되었음을 나타내는 이벤트. 더 이상의 이벤트를 발생시키지 않으며 Observable 종료
- Observable로 생성된 객체를 구독(Subscribe)함으로써 비동기적으로 발생하는 이벤트를 감지할 수 있다.

#### Observable 생성
- just: 하나의 element를 Observable로 만들고 반환
```swift
Observable<Int>.just(7)
// 7
```
- of: 가변적인 element를 Observable로 만들고 반환
```swift
Observable<Int>.of(1, 2, 3, 4, 5)
// 1
// 2
// 3
// 4
// 5

Observable<Int>.of([1, 2, 3, 4, 5])
// [1, 2, 3, 4, 5]
```
- from: 배열의 각 element를 Observable로 만들고 반환
```swift
Observable<Int>.from([1, 2, 3, 4, 5])
// 1
// 2
// 3
// 4
// 5
```
- create: 커스텀 Observable을 생성
```swift
Observable<Int>.create { observer in
  observer.onNext(1)
  observer.onNext(2)
  observer.onNext(3)
  
  //observer.onError(~)
  
  observer.onCompleted()
  
  return Disposables.create()
}
// 1
// 2
// 3
```

#### Observable 구독
- subscribe를 이용해 Observable을 구독함으로써 비동기적으로 이벤트를 감지
- disposed를 이용해 구독을 취소(**메모리릭을 방지하기 위함!**)
- DisposeBag을 사용함으로써 효율적으로 한 번에 Disposable들을 관리할 수 있다.
```swift
let disposeBag = DisposeBag()
let observable = Observable<Int>.just(7)
observable
  .subscribe(onNext: { num in
    print(num)
  }, onError: { err in
    print(err)
  }, onCompleted: {
    print("Completed!")
  })
  .disposed(by: disposeBag)
  // 7
  // Completed!
```
