# ✅ UITableView를 코드로 만들 때 컴포넌트가 출력되지 않는 문제

#### #UITableView #UIKit 

## 🤔 문제 정의
분명 delegate, dataSource등을 위임 받고 Cell의 컴포넌트까지 넣었는데,,,   
셀 배경화면만 둥둥 떠다니고 출력이 되지 않는다.   
코드로 구현할 때 가끔 부딪히는 경험을 정리해봤다.

내 테이블 뷰를 돌려내!!!

<img width="300" src="https://user-images.githubusercontent.com/113565086/229740767-dde1ba1a-59ea-4c14-9023-53042d1c433b.png">

<br>

## 🪓 삽질 기록

### 1. 놓친 부분이 있나 확인해보기
이럴 때 제일 많이 놓치는 부분을 다시 한번 더 확인했다.   
- delegate 위임을 받았는가? ✔️
- dataSource 위임을 받았는가? ✔️
- cellIdentifier이 일치하는가? ✔️
- Auto Layout 설정이 제대로 되었는가? (translate어쩌구 = false, addsubView(), Constraints 등) ✔️

두번이나 검토해봤지만 문제가 될만한 부분은 없었다.

<br>

## 😈 문제 해결

tableView 코드 구현 방법을 검색해 보던 중, 내 코드와 다른 부분을 찾았다!   
바로 cell을 생성하는 메서드 부분이었다.   
나는 `cocoapod` 에서 지원하는 템플릿을 그대로 가져와 써서 `storyboard` 기준 생성 메서드인   
`awakeFromNib()` 에다가 생성 코드를 쓰고 있었던 것... (메서드 이름부터가 Nib임 ㅡㅡ)   
코드 기반으로 생성할 때는 `init(style:reuseIdentifier:)` 메서드를 사용해야 한다.   
하여간 코드로 작성할때 이런 문제가 자주 발생하는 것 같다.

~~~swift
override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
    super.init(style: style, reuseIdentifier: reuseIdentifier)
    // 여기서부터 지지고 볶고 하자
}
~~~

<img width="300" src="https://user-images.githubusercontent.com/113565086/229743287-d9cbf049-eeed-46e9-869a-ed2559c99315.png">

