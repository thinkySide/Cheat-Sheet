# ✅ ScrollView 사용하기

#### #UIScrollView #UIKit 

## **🔍** 작성 목적

`TableView`, `CollectionView`를 사용할 때는 cell의 개수에 따라   
자동으로 스크롤이 되었기 때문에 문제가 없었지만 일반 `ScrollView`는 처음 사용해본다.   
현재 진행중인 프로젝트에 직접 적용하며 배워보자!

<br/>

## 📌 적용 방법

### 1. ScrollView 객체 생성
`UIScrollView` 객체를 만들어준다.
~~~swift
var scrollView: UIScrollView = {
    let scroll = UIScrollView()
    return scroll
}()
~~~

<br/>

### 1-1. ScrollView 기본 설정
기본적인 오토레이아웃 설정을 해준다.
~~~swift
// addSubView 추가
addSubview(scrollView)

// translatesAutoresizingMaskIntoConstraints 설정
scrollView.translatesAutoresizingMaskIntoConstraints = false
~~~

<br/>

### 2. ScrollView 제약 걸어주기
화면을 꽉채울 수 있게 설정해봤다.
~~~swift
NSLayoutConstraint.activate([
    scrollView.topAnchor.constraint(equalTo: topAnchor),
    scrollView.bottomAnchor.constraint(equalTo: bottomAnchor),
    scrollView.leadingAnchor.constraint(equalTo: leadingAnchor),
    scrollView.trailingAnchor.constraint(equalTo: trailingAnchor),
])
~~~

<br/>

### 3. 콘텐츠 객체 생성하기
이게 바로 **실제 콘텐츠의 크기**를 결정할 객체이다! ⭐️
~~~swift
var contentView: UIView = {
    let view = UIView()
    return view
}()
~~~

<br/>

### 3-1. contentView 기본 설정
또다시 기본적인 오토레이아웃 설정을 해준다.   
`scrollView` 안에다가 만들어줘야한다!
~~~swift
// addSubView 추가
scrollView.addSubview(contentView)

// translatesAutoresizingMaskIntoConstraints 설정
contentView.translatesAutoresizingMaskIntoConstraints = false
~~~

<br/>

### 4. contentView 제약 걸어주기
`scrollView` 에 꽉찰 수 있도록 설정해줬다.
~~~swift
NSLayoutConstraint.activate([
    contentView.topAnchor.constraint(equalTo: scrollView.topAnchor),
    contentView.bottomAnchor.constraint(equalTo: scrollView.bottomAnchor), 
])
~~~

<br/>

### 5. UIScrollView 방향 정하기 ⭐️
보통 세로로 스크롤 구현을 많이 하는데, 세로로 스크롤을 구현하려면 `contentView` 의 `heightAnchor` 를 잘 잡아줘야 한다!   
`widthAnchor` 도 잡아줘야 크기가 깨지지 않고 잘 맞는다.
~~~swift
NSLayoutConstraint.activate([
    // myView 높이만큼 contentView의 크기를 잡아주겠다는 뜻!
    contentView.heightAnchor.constraint(equalTo: myView.heightAnchor),
    contentView.widthAnchor.constraint(equalTo: scrollView.widthAnchor)
])
~~~

<br/>

## 🎨 그림 그려보기
이해를 돕기 위해 간단히 그려봤다!   
빨간색 영역인 `scrollView` 는 고정된 사이즈로 화면을 움직이고,   
파란색 영역인 `contentView` 는 실제 콘텐츠의 높이를 알려준다.

<img width="600" src="https://user-images.githubusercontent.com/113565086/226287216-ee3086f2-f8e4-46bb-b28b-32758b12fbcf.png">

<br/>

## 💌 Ref.

- [inwoodev님의 velog](https://velog.io/@inwoodev/Swift-ScrollView-%EC%BD%94%EB%93%9C%EB%A1%9C%ED%99%94%EB%A9%B4%EC%97%90-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)

