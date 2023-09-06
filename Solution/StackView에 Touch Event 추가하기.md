# ✅ StackView에 Touch Event 추가하기

#### #Gesture #UIKit 

## **🔍** 작성 목적

버튼 이외에 StackView로 묶어서 통채로 Gesture를 추가해야하는 일이 빈번합니다.    
어떻게 추가할 수 있는지 이번 기회에 정리해보려합니다.

<br>

## 📌 적용 방법

### 1. Gesture 객체 생성

`UITapGestureRecognizer` 객체를 생성 후, StackView에 추가한다.

~~~swift
lazy var myStackView: UIStackView = {
    let stack = UIStackView()
    let gesture = UITapGestureRecognizer(target: self, action: #selector()) // 객체생성
    stack.addGestureRecognizer(gesture)
    return stack
}()
~~~

<br>

### 2. TouchEvent 메서드 정의

`objc` 키워드를 잊지 말자.

~~~swift
// 1. 메서드 정의
@objc func myStackViewTapped(_ sender: UITapGestureRecognizer) {
    print("스택뷰가 클릭되었습니다.")
}

// 2. 메서드 추가
lazy var myStackView: UIStackView = {
    let stack = UIStackView()
    let gesture = UITapGestureRecognizer(target: self, action: #selector(myStackViewTapped))
    stack.addGestureRecognizer(gesture)
    return stack
}()
~~~


## 🪁 Obsidian Link
- [[LongPress Animation 만들기]]