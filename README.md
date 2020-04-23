## ReactiveX
###  observabal streams으로 Async 프로그래밍을 하기 위한 API

---

### Sync/Async

- Sync(동기) : 작업을 하면 다른 작업 못하게 막음
- Async(비동기) : 작업을 해도 다른 작업은 계속 실행됨

> **Sync를 Async로 하려면 DispatchQueue하면 됨**
>
> `DispatchQueue.global().async`은 concurrence(동시 발생)을 async방식으로 하는 것
> `DispatchQueue.main.async`은 main thread로 돌리는 것

### PromiseKit

- RxSwift와 비슷함
- DispatchQueue하기 귀찮을 때 사용하는 비동기 라이브러리
- return할 때, Promise를 create하고 seal in에 넣어서 사용함
- `.done`, `.catch`를 사용해 나온 이후에 어떻게 처리할 것인지 정할 수 있음

### RxSwift

- Async 코드를 간결하게 쓰기위해 사용함
- RxSwift install하려면 pod file에 `pod 'RxSwift`만 명시해주면 됨
- Promise와 비슷하지만, return 타입이 observable
- return할 때, Observabal을 create하고 seal in에 넣어 `.onNext`로 보냄

### Disposable

- Observable에서 `.subscribe`하면 Disposable을 통해 dispose하면 작업을 취소할 수 있음
- error가 나거나 특정상황일 때 모든 작업을 취소시킬 때 사용하기 유용함
- RxSwift나 PromiseKit을 사용하지 않고 dipose하려면 OperationQueue와 귀찮은 작업들을 해야함

### DisposeBag : dispose를 담는 가방

- DisposeBag에 insert 되어있는 작업을 한번에 dispose해줌

### Operators

#### 데이터를 직접 받아서 create해주는 methods

1. just

- Observabal에서 create해서 seal in에 넣어서 내려오는 것과 같은 기능
- Observabal.just의 문자열이 `.subscribe`할 때 첫번째 인자로 그대로 내려옴
- 문자열이 아니라 Array여도 그대로 내려옴

2. From

- Array에 있는 요소들을 하나씩 떼어서 내려줌(Array 요소 타입은 Any)

3. Map : 가장 많이 쓰이는 method

- `.just` 문자열이 `.map`을 먼저 거치면서 문자열 뒤에 "RxSwift"를 추가시키고, 바뀐(mapping된) 문자열이 내려감
- Array인 경우, Array를 from을 거쳐 내려보내면서 mapping되면 `.count`를 시켜 Int타입으로 바뀌고 값이 내려감 = stream
- 예시) mapping을 연속해서 사용할 경우, 링크를 URL객체로 변환시키고 data를 가져와서 UIImage로 가져올 수도 있음

4. Filter

- Array의 요소를 하나씩 내려보냈을 때, `.filter`를 거치면서 값이 false인 경우에는 최종값으로 내려가지 않음
