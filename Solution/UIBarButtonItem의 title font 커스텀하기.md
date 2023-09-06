# ✅ UIBarButtonItem의 title font 커스텀하기

#### #UIBarButtonItem #UINavigationController #UIKit 

## **🔍** 작성 목적

디자인 하다보면 여기저기 수정하고 싶은 곳이 많아진다.   
그 중에서도 애플이 기본으로 설정해 놓은 디자인을 바꾸려 할 때 많이 찾게 되는데,   
`UINavigationBar`에 들어가는 `UIBarButtonItem`의 title 폰트를 바꾸는 방법을 정리해본다.

~~~swift
let item = UIBarButtonItem(title: "확인", // 요 font를 바꿔보자!
                           style: .plain,
                           target: self,
                           action: #selector(itemTapped))
~~~

<br>

## 📌 적용 방법

### 1. NSAttributedString 이용하기

`NSAttributedString`은 string을 적용된 속성과 함께 표시할 수 있게 도와준다.   
일반적으로 Key와 같이 사용된다고 한다!

~~~swift
lazy var createBarButtonItem: UIBarButtonItem = {
    let item = UIBarButtonItem(title: "확인", style: .plain, target: self, action: #selector(itemTapped))

    // 1. 커스텀 할 UIFont 만들어주기
    let font = UIFont(name: Pretendard.semiBold, size: 18)

    // 2. NSAttributedString 만들어주기
    let attributes = [NSAttributedString.Key.font: font]

    // 3. setTitleTextAttributes 메서드를 활용해 넣어주기
    item.setTitleTextAttributes(attributes as [NSAttributedString.Key : Any], for: .normal)
    return item
}()
~~~


## 🪁 Obsidian Link
- [[Pushing the same view controller instance more than once is not supported 오류]]
- [[NavigationBar 하단 라인 삭제하기]]
- [[UINavigationBar 뒤로가기 버튼 커스텀]]
- [[UINavigation LargeTitle과 일반 Title 자연스럽게 전환하기]]