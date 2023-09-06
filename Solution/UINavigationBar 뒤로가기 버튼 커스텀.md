# ✅ UINavigationBar 뒤로가기 버튼 커스텀

#### #UINavigationController #Publishing #UIKit 

## **🔍** 작성 목적

UINavtionController를 사용하며 push 하면 기본적으로 backButton이 생성된다.   
오늘은 요 버튼을 입맛대로 커스텀 해보려 한다!

<br>

## 📌 적용 방법

### 1. 첫번째 ViewController에서 설정하자!

backButton을 설정할 때 가장 중요한 핵심은,   
**눈에 보이는 것과 다르게 Push된 2번째 뷰컨이 아닌, 1번째 뷰컨에서 backButton을 설정해줘야한다는 것이다!!! ⭐️⭐️⭐️**   
(이것 땜에 왜 안되지,,? 삽질을 좀 했다)

<br>

### 2. UIBarButtonItem 만들어주기

우선 내가 커스텀할 BarButtonItem을 하나 만들어준다.

~~~swift
// 텍스트 없이 뒤로가기 아이콘만 넣어줄거라 title에 공백문자열을 줬다.
let backBarButtonItem = UIBarButtonItem(title: "", style: .plain, target: self, action: nil)

// tintColor라 아이콘, 타이틀 색상만 바뀐다.
backBarButtonItem.tintColor = .black
~~~

<br>

### 3. 만든 버튼 backButton으로 설정하기

~~~swift
navigationItem.backBarButtonItem = backBarButtonItem
~~~


## 🪁 Obsidian Link
- [[Pushing the same view controller instance more than once is not supported 오류]]
- [[NavigationBar 하단 라인 삭제하기]]
- [[UIBarButtonItem의 title font 커스텀하기]]
- [[UINavigation LargeTitle과 일반 Title 자연스럽게 전환하기]]