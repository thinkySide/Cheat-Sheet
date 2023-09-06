# ✅ Closure에서 self 생략하기

#### #Closure #Swift 

## **🔍** 작성 목적

Swift에서 Closure로 코드를 작성하다보면 무수히 많은 self 명시로 어지러울 때가 있다. 클로저가 실행될 때 자신의 인스턴스라는 사실을 알려줘야하는 것 때문에 붙이는 거라고 알고는 있지만 너무 불편하다! 그래서 애플에서 Swift 5.8부터 새로 만들어준 기능이 self를 생략하는 기능인데 가끔 헷갈릴 때가 있어 정리해보고자 한다.

<br>

## 📌 적용 방법

### 1. Unwrapping 해서 self 사용하기

방법은 간단하다! 클로저 실행 내부에서 self를 옵셔널 바인딩하면 된다.

~~~swift
closure = { [weak self] in

    // if-let 바인딩도 물론 가능하다!
    guard let self else { return }

    // self를 명시해주지 않아도 자유롭게 사용이 가능하다 후후
    navigationController?.pushViewController(viewController, animated: true)
}
~~~

<br>

### 2. Task에서도 동일하게 사용하자

Task도 클로저이기 때문에 위와 동일하게 사용해주면 된다! 첨에 완전 다른 건줄 알아서 헤메다가 찾게 되었다 ㅎㅎ

~~~swift
Task { [weak self] in
    guard let self else { return }
}
~~~