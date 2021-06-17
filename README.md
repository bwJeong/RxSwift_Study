# RxSwift_Study
- [RxRelay](https://github.com/bwJeong/RxSwift_Study/blob/main/RxRelay.md)
- [RxCocoa](https://github.com/bwJeong/RxSwift_Study/blob/main/RxCocoa.md)

## RxSwift
1. [Observable](#observable)
2. [Subject](#subject)
3. [Combining Operator](#combining-operator)
4. [Scheduler](#scheduler)

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

// 구독하지 않았으므로 값은 방출되지 않음!
// 7
```
- of: 가변적인 element를 Observable로 만들고 반환
```swift
Observable<Int>.of(1, 2, 3, 4, 5)

// 구독하지 않았으므로 값은 방출되지 않음!
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

// 구독하지 않았으므로 값은 방출되지 않음!
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

// 구독하지 않았으므로 값은 방출되지 않음!
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
### Subject
- Observable과 Observer 두 가지 역할을 모두 수행(**이벤트의 emit과 subscribe 역할을 모두 수행!**)
- next, error, completed 생명주기

#### Subject의 종류
- PublishSubject
  - 초기값 지정 X
  - 구독한 이후의 순간부터 방출된 이벤트를 전달
```swift
let disposeBag = DisposeBag()
let subject = PublishSubject<Int>()
subject.onNext(1)

subject
  .subscribe(onNext: { num in
  print(num)
  })
  .disposed(by: disposeBag)
  
subject.onNext(2)
subject.onNext(3)

// 2
// 3
```
- BehaviorSubject
  - 초기값 지정 O
  - 구독한 순간, 이전 next 이벤트를 방출하고 이후부터 방출된 이벤트를 전달
```swift
let disposeBag = DisposeBag()
let subject = BehaviorSubject<Int>(value: 0)
subject.onNext(1)

subject
  .subscribe(onNext: { num in
  print(num)
  })
  .disposed(by: disposeBag)
  
subject.onNext(2)
subject.onNext(3)

// 1
// 2
// 3
```

### Combining Operator
- CombineLatest
  - 각 Observable에 대해서, 서로 이벤트를 방출할 때에 가장 최신의 이벤트를 결합하여 전달
  - 모든 Observable이 이벤트가 있을 경우에만 이벤트를 결합하여 전달
 ```swift
let disposeBag = DisposeBag()
let subjectA = PublishSubject<String>()
let subjectB = PublishSubject<String>()

Observable.combineLatest(subjectA, subjectB) { $0 + $1 }
  .subscribe(onNext: { fullName in
    print(fullName)
  })
  .disposed(by: disposeBag)
  
subjectA.onNext("손")
subjectB.onNext("흥민")
subjectA.onNext("이")
subjectB.onNext("강인")

// 손흥민
// 이흥민
// 이강인
```
- zip
  - 각 Observable에 대해서, 서로 하나씩의 이벤트를 모두 방출했을 때 해당 이벤트들을 결합하여 전달
 ```swift
let disposeBag = DisposeBag()
let subjectA = PublishSubject<String>()
let subjectB = PublishSubject<String>()

Observable.zip(subjectA, subjectB) { $0 + $1 }
  .subscribe(onNext: { fullName in
    print(fullName)
  })
  .disposed(by: disposeBag)
  
subjectA.onNext("손")
subjectB.onNext("흥민")
subjectA.onNext("이")
subjectB.onNext("강인")

// 손흥민
// 이강인
```

### Scheduler
- observe(on:)
  - Operator(map, filter 등...)를 이용한 작업 및 subscribe 작업을 다른 Scheduler에서 하고 싶을 때 사용
- subscribe(on:)
  - Observable을 특정 Scheduler에서 생성하고 싶을 때 사용
