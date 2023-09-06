# ✅ NavigationBar 하단 라인 삭제하기

#### #UINavigationController #UIKit 

## **🔍** 작성 목적

UI 디자인을 하다보면 `NavigationBar`도 커스텀해야 하는 경우가 많다.   
그 중에서도 하단에 기본값으로 붙어 따라오는 하단 라인을 삭제해보자!

<img width="500" src="https://user-images.githubusercontent.com/113565086/229415352-11267269-60cf-4184-b23c-686e7c75b77b.png">

<br>

## 📌 적용 방법

### 1. 그건 선이 아니라 그림자라구요!

지금까지 내가 선이라고 생각하던건 선이 아니었다...   
바로 그림자였다! 그래서 그림자 속성을 건드려주면 간단히 없앨 수 있다.

~~~swift
// 비어있는 UIImage값을 줌으로써 없애주기
navigationController?.navigationBar.shadowImage = UIImage()

// 반대로 선을 살리고 싶다면? 초깃값으로 돌리자
navigationController?.navigationBar.shadowImage = nil

// 내 경우 UINavigationBarAppearance를 사용해서인지 위의 코드가 먹지 않아 이렇게 해결했다! ⭐️
navigationBarAppearance.shadowColor = .clear
~~~

깔끔하게 선이 없어졌다 ㅎㅎ

<img width="500" src="https://user-images.githubusercontent.com/113565086/229664924-f1d67ab0-3ecc-470b-ac23-0ea4d1d86b3e.png">