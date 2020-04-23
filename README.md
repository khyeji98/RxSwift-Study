# RxSwift-Study : RxSwift 스터디 정리

### Sync/Async

- Sync(동기) : 작업을 하면 다른 작업 못하게 막음
- Async(비동기) : 작업을 해도 다른 작업은 계속 실행됨

#### Sync를 Async로 하려면 DispatchQueue하면 됨

`DispatchQueue.global().async`은 concurrence(동시 발생)을 async방식으로 하는 것
`DispatchQueue.main.async`은 main thread로 돌리는 것

### PromiseKit

- RxSwift와 비슷함
- DispatchQueue하기 귀찮을 때 사용하는 비동기 라이브러리
- return할 때 closure를 사용함
- `.done`, `.catch`를 사용해 나온 이후에 어떻게 처리할 것인지 정할 수 있음

### RxSwift

- Async 코드를 간결하게 쓰기위해 사용함
- RxSwift install하려면 pod file에 `pod 'RxSwift`만 명시해주면 됨
- Promise와 비슷하지만, return 타입이 observable
- return할 때, seal에 넣어 `.onNext`로 보냄

### Observable

- Observable에서 `.subscribe`하면 
