# ✅ UICollectionView 선택된 item으로 스크롤이 안되는 문제

#### #UICollectionView #LifeCycle #UIKit 

## 🤔 문제 정의

선택한 컬러가 무엇인지 바로 확인이 가능하도록 `UICollectionView`의 선택된 item에 스크롤이 되게 만들려했는데   
잘못된 부분이 없는 것 같은데 이상하게 작동을 하지 않고 있다. 분명 선택은 하고 있는데,,, 어떤 문제가 있는걸까?

~~~swift
func setupSelectedCell() {
    let indexPath = IndexPath(item: colorManager.getIndex(), section: 0)
    let collectionView = viewManager.colorCollectionView
    
    // 현재 선택은 되지만 스크롤 기능 작동하지 않음 ⭐️
    collectionView.selectItem(at: indexPath, animated: true, scrollPosition: .centeredVertically)
}
~~~

<img width="300" src="https://user-images.githubusercontent.com/113565086/233839746-13897bd6-a355-4391-9e73-afa0c79fad32.gif">

<br>

## 🪓 삽질 기록

### 1. scrollToItem 메서드 이용해보기

`selectItem` 메서드에는 스크롤 기능도 함께 내장되어 있지만 작동을 안하는 것 같아   
스크롤을 위한 메서드 `scrollToItem`를 한번 더 호출해봤다. 결과는 똑.같.다!!!🤬

~~~swift
collectionView.scrollToItem(at: indexPath, at: .centeredVertically, animated: true)
~~~

<br>

### 2. viewDidLayoutSubviews 메서드에 넣기

다른 문제들을 잡고 있다 이제는 해결해야할 단계가 온 것 같아 다시 들여다봤다. Stackoverflow를 둘러보던중, `viewDidLayoutSubviews` 메서드를 재정의하면서 셀 선택 코드를 넣으면 해결이 된다는 글을 보고 바로 적용해 봤다.

~~~swift
override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()
    setupSelectedCell() // 요기다가 넣어줌
}

private func setupSelectedCell() {
    let indexPath = IndexPath(item: self.colorManager.getCurrentIndex(), section: 0)
    let collectionView = self.viewManager.colorCollectionView
    collectionView.selectItem(at: indexPath, animated: false, scrollPosition: .centeredVertically)
}
~~~

결과는 아주 잘 작동 하긴하는데,,문제는 `viewDidLayoutSubviews` 메서드가 계속 호출이 되면서 스크롤이 하고 싶지 않을 때도 초깃값으로 스크롤이 되어버리는,, 문제가 발생. 그래도 거의 다왔다 ㅎㅎ

<br>

## 😈 문제 해결

그래서 처음 스크롤이 되었는지 확인할 수 있는 전역변수를 만든 후, 값을 바꿔주는 것으로 간단하게 해결했다. 전체 문제 해결은 삽집을 꽤나 오래하다가 중단했다 다시 시작하게 되었는데, 역시 다른거 보다가 잠깐 봤을 때 해결되는 문제가 많은 것 같다. ++ View의 Lifecycle에 대한 심도있는 이해가 있었다면 이런 상황도 금방 해결할 수 있지 않았을까 ㅜ 반성하자,,

~~~swift

/// 초기 스크롤 상태 확인을 위한 전역변수
private var isScrolled: Bool = false

override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()
    setupSelectedCell()
}

private func setupSelectedCell() {

    // 스크롤값 확인해주기
    if !isScrolled {
    isScrolled = true
    let indexPath = IndexPath(item: self.colorManager.getCurrentIndex(), section: 0)
    let collectionView = self.viewManager.colorCollectionView
    collectionView.selectItem(at: indexPath, animated: false, scrollPosition: .centeredVertically)
}
}
~~~

<br>

## 💌 Ref.

- [Stackoverflow - UICollectionView scrollToItem Not Working in iOS 14](https://stackoverflow.com/questions/50237180/uicollectionview-scrolltoitem-not-working-in-ios-14#comment87492844_50237180)