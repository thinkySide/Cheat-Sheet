# ✅ Carousel 기능 구현하기

#### #기능구현 #Carousel #UICollectionView #UIKit 

## **🔍** 작성 목적

DayBlock 프로젝트를 진행하며 메인화면에 사용 될 CollectionView를 만들어 볼려 한다.   
`Carousel Effect` 캐러셀 이펙트를 활용해 구현할 예정이다!

<img width="300" src="https://user-images.githubusercontent.com/113565086/229653932-69edfe7f-c38f-4cee-b21a-067091035430.png">

<br>

## 📌 적용 방법

### 1. CollectionView, CollectionViewCell 준비하기

기본적으로 `CollectionView`와 `CollectionViewCell`을 준비한다.

~~~swift
// MyView.Swift

private let collectionView: UICollectionView = {
    /// layout
    let layout = UICollectionViewFlowLayout()
    layout.scrollDirection = .horizontal // 가로 스크롤 방향
    layout.itemSize = CGSize(width: 160, height: 160) // item 사이즈
    layout.minimumLineSpacing = 32 // item Row 간격
    
    /// collectionView
    let collectionView = UICollectionView(frame: .zero, collectionViewLayout: layout)
    collectionView.backgroundColor = .clear
    collectionView.clipsToBounds = true
    collectionView.showsHorizontalScrollIndicator = false // 스크롤 인디케이터 비활성화 
    collectionView.contentInsetAdjustmentBehavior = .never // Safe Area 가려지는 현상 방지
    collectionView.decelerationRate = .fast // 스크롤 감속도 빠르게 (페이징 효과)
    collectionView.isPagingEnabled = false // 페이징 비활성화 ⭐️ (item을 양옆으로 보여주기 위해 직접 구현해야 함)
    return collectionView
}

// Auto Layout 코드 생략...
~~~

~~~swift
// ViewController.swift

final class ViewController: UIViewController {

    private let viewManager = MyView()

    override func viewDidLoad() {
        // dataSource & delegate
        viewManager.collectionView.dataSource = self
        viewManager.collectionView.delegate = self

        // Cell 등록
        viewManager.collectionView.register(MyCell.self, forCellWithReuseIdentifier: "MyCell")
    }
}

extension ViewController: UICollectionViewDataSource, UICollectionViewDelegate {
    
    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return 5
    }
    
    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        let cell = viewManager.collectionView.dequeueReusableCell(withReuseIdentifier: "MyCell", for: indexPath) as! MyCell
        cell.backgroundColor = .systemBlue
        return cell
    }
    
}
~~~

<br>

### 2. CollectionView의 ContentInset 설정하기

CollectionView 상하좌우 여백을 주기 위해 contentInset을 설정한다.   
계산법은 `(전체 화면 가로 사이즈 - 블럭 가로 사이즈) / 2` = 가로 inset 크기를 구한다.

~~~swift
func setupContentInset() {
    /// (전체 화면 가로 사이즈 - 블럭 가로 사이즈) / 2
    let inset: CGFloat = (UIScreen.main.bounds.width - 160) / 2.0
    
    /// 왼쪽과 오른쪽에 inset만큼 떨어트리기
    let contentInset = UIEdgeInsets(top: 0, left: inset, bottom: 0, right: inset)
    
    /// CollectionView의 contentInset 설정
    viewManager.blockCollectionView.contentInset = contentInset
    }
~~~

<img width="600" src="https://user-images.githubusercontent.com/113565086/229658518-fe7b33c8-8617-495d-ab26-7f38ee56e4c1.png">

<br>

### 3. scrollViewWillEndDragging 메서드 활용 구현

이 부분은 아직 정확히 이해하지 못해서 추후 천천히 뜯어보고 업데이트 할 예정!   
핵심은 스크롤된 크기를 구해 몇번째 블럭인지 계산하고, 최종 스크롤 위치를 지정해 페이징 효과를 구현하는 로직이다.

~~~swift
extension ViewController: UIScrollViewDelegate {
    func scrollViewWillEndDragging(_ scrollView: UIScrollView, withVelocity velocity: CGPoint, targetContentOffset: UnsafeMutablePointer<CGPoint>) {
        
    /// 스크롤된 크기 = 스크롤이 멈춘 x좌표 + 스크롤뷰 inset
    let scrollSize = targetContentOffset.pointee.x + scrollView.contentInset.left
    
    /// 블럭 크기 = 블럭 가로 사이즈 + 블럭 여백 (보이는 영역 보다 크게 사이즈를 잡아야 캐러셀 구현 가능)
    let blockWidth = 160 + 32
    
    /// 블럭 인덱스 = 스크롤된 크기 / 블럭 크기
    let blockIndex = round(scrollSize / blockWidth)
    
    /// 최종 스크롤 위치 지정
    targetContentOffset.pointee = CGPoint(x: blockIndex * blockWidth - scrollView.contentInset.left,
                                          y: scrollView.contentInset.top)
    }
}
~~~

캐러셀 이펙트 구현 성공!

<img width="300" src="https://user-images.githubusercontent.com/113565086/229660078-8a81c442-92a8-4b46-9802-3c52cdea7d73.gif">

<br>

## 💌 Ref.
- [jake-kim님의 tistory](https://ios-development.tistory.com/1020)