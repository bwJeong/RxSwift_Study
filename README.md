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
'''{.swift}
Observable<Int>.just(7)
'''
- of: 가변적인 element를 Observable로 만들고 반환
- from: 배열의 각 element를 Observable로 만들고 반환
- create
