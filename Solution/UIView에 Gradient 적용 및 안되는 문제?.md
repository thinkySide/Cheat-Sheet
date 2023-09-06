# ✅ UIView에 Gradient 적용 및 안되는 문제?

#### #CAGradientLayer #Publishing #UIKit 

## 🔍 작성 목적
`UIView`에 커스텀 Gradient를 한번도 적용 해본적이 없어 정리해보고자 한다.

<br/>

## 📌 적용 방법
~~~swift
// 1. CAGradientLayer 인스턴스 생성
let gradient = CAGradientLayer()

// 2. gradient의 크기 지정 (컴포넌트와 같게)
gradient.frame = myView.bounds

// 3. 그라디언트 컬러 팔레트 지정
grdient.colors = [
   UIColor.blue.cgColor,
   UIColor.green.cgColor, 
]

// 4. 레이어 추가하기
myView.layer.addSublayer(gradient)
~~~

<br/>

## 🪓 삽질 기록

### 1. 왜 안됨?
분명 예제 그대로 했는데 적용이 안된다... 혹시나 놓친 설정이 있나 싶어 여러가지 레퍼런스를 찾아봤지만 기본 설정은 모두 해준 상태이다!
~~~swift
lazy var reportTitleView: UIView = {
    let view = UIView()
    
    // 그라디언트 적용하기
    let gradient = CAGradientLayer()
    gradient.frame = view.bounds
    gradient.colors = [
        UIColor.blue.cgColor,
        UIColor.green.cgColor,
    ]
    view.layer.addSublayer(gradient)

    [titleLabel, myLine]
        .forEach { view.addSubview($0) }
    return view
}()
~~~

<img width="500" src="https://user-images.githubusercontent.com/113565086/225531158-fb95d9be-1a83-4484-aa26-72f42718a411.png">

<br/>

### 2. Gradient 적용 시점 변경
그라디언트가 적용되지 않는 문제는 View LifeCycle과 관련이 있었다.   
View와 ViewController를 분리해서 사용하고 있었는데, gradient의 `frame` 사이즈를 적용시켜주려고   
reportTitleView의 `bounds` 값을 할당했더니 문제는 이 코드가 동작하는 시점에서는   
reportTitleView가 아직 그려지지 않았기 때문에 할당이 원하는 대로 동작하지 않았던 것!   
그래서 모든 크기가 결정된 후 호출되는 `layoutSubviews()` 메서드에 그라디언트 적용 코드를 넣어줬더니 완성!!! 인줄 알았는데,,, 글자가 없어졌다?
~~~swift
override func layoutSubviews() {
    super.layoutSubviews()
    let gradient = CAGradientLayer()
    gradient.frame = reportTitleView.bounds // 위에서는 이게 동작 X
    gradient.colors = [
        UIColor.blue.cgColor,
        UIColor.green.cgColor,
    ]
    reportTitleView.layer.addSublayer(gradient)
}
~~~

<img width="500" src="https://user-images.githubusercontent.com/113565086/225533142-84391588-ee2b-441d-a5ce-b153753900f6.png">

<br/>

## 😈 문제 해결
결국 `layoutSubviews()` 메서드가 호출되면서 가장 상단에 배치가 되어서 그랬다.   
`insertSublayer(_:at:)` 메서드로 바꿔 가장 아래 즉, 0을 넣어주면 해결 완료!   
그라디언트 적용은 금방할 줄 알았는데,,, 역시 쉬운게 하나 없다 ㅎㅎ
~~~swift
override func layoutSubviews() {
    super.layoutSubviews()
    let gradient = CAGradientLayer()
    gradient.frame = reportTitleView.bounds
    gradient.colors = [
        UIColor.blue.cgColor,
        UIColor.green.cgColor,
    ]
    reportTitleView.layer.insertSublayer(gradient, at: 0) // 가장 하단에 그라디언트 그리기
}
~~~

<img width="500" src="https://user-images.githubusercontent.com/113565086/225535084-4783be4b-4cdc-4508-8d9a-f410b49993aa.png">
