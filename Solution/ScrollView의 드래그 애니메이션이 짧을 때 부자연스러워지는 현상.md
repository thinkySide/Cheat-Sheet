# ✅ ScrollView의 드래그 애니메이션이 짧을 때 부자연스러워지는 현상

#### #UIScrollView #Publishing #UIKit 

## 🤔 문제 정의

DayBlock 프로젝트의 메인화면은 캐러셀 레이아웃으로 만들어졌다. 기능상으로는 문제가 없지만, 애니메이션에서 문제가 있는데 바로 휙~휙~ 길게 드래그를 할때는 스크롤 애니메이션이 부드럽게 작동하다가도, 짧게 틱틱 끊으면서 드래그를 할때는 부자연스럽게 뚝뚝 끊기는 현상이 있다. 프로젝트를 진행하면서 꽤나 오랫동안 방치해뒀던 문제지만 언젠가는 해결해야하는 문제이기에 이렇게 정리해본다.

<img width="300" src="(https://github.com/thinkySide/DayBlock/assets/113565086/7ef9a4bb-4eb5-4b69-9704-4bcfef720c1d">

~~~swift
// 캐러셀 애니메이션 핵심 구현 코드 (HomeViewController.swift)

extension HomeViewController: UIScrollViewDelegate {
    
    func scrollViewWillEndDragging(_ scrollView: UIScrollView, withVelocity velocity: CGPoint, targetContentOffset: UnsafeMutablePointer<CGPoint>) {
        
        // 스크롤된 크기 = 스크롤이 멈춘 x좌표 + 스크롤뷰 inset
        let scrollSize = targetContentOffset.pointee.x + scrollView.contentInset.left
        
        // 블럭 크기 = 블럭 가로 사이즈 + 블럭 여백 (보이는 영역 보다 크게 사이즈를 잡아야 캐러셀 구현 가능)
        let blockWidth = Size.blockSize.width + Size.blockSpacing
        
        // 블럭 인덱스 = 스크롤된 크기 / 블럭 크기
        let currentBlockIndex = round(scrollSize / blockWidth)
        
        // 최종 스크롤 위치 지정
        targetContentOffset.pointee = CGPoint(x: currentBlockIndex * blockWidth - scrollView.contentInset.left,
                                              y: scrollView.contentInset.top)
    }
}
~~~

<br>

## 🪓 삽질 기록

## 1. ScrollView 뜯어보기

ScrollView에 대한 이해가 부족해 생긴 문제라고 생각해 관련 공식문서와 블로그를 찾아봤다. 건드려볼만한 프로퍼티인 velocity(속도), bounces(스크롤 가장자리에서 튀어오르는 애니메이션) 등을 살펴보고 코드에 적용해봤지만 값을 확인해보니 요 문제는 아니었다,,

<br>

## 2. 접근 방식 바꿔보기

현재 스크롤 애니메이션 문제의 근본적인 원인을 찾기 어려워졌고, 아예 접근방식을 다르게 해서 스크롤 애니메이션을 구성해보면 어떨까? 라는 생각을 했다. 우선 애니메이션이 부자연스러워지는 조건은 제스처를 짧게 틱틱 끊었을 때였기 때문에, 조금이라도 왼쪽 or 오른쪽으로 스크롤값이 바뀌면 아예 블럭을 전환시켜버리는 식으로 만들어보았다.

<br>

## 😈 문제 해결

로직을 구현하는데 많은 시행착오가 있었지만, 최종 코드를 완성했다. 와중에 이것도 정확한 이유를 알아내진 못했지만, 가끔씩 스크롤의 시작 지점(contentOffset.x)이 동일한 위치에서 시작했음에도 불구하고 조금씩 오차가 생기는 문제가 발생해 보정 또한 해주었다. 최종 완성된 UI 인터랙션은 처음보다 훨씬 좋아져 보람차다 :)

~~~swift
func scrollViewWillBeginDragging(_ scrollView: UIScrollView) {
    
    // 1. 스크롤이 시작될 때의 contentOffset.x를 저장
    startScrollX = scrollView.contentOffset.x
    
    // 멀티 터치 비활성화
    scrollView.isUserInteractionEnabled = false
}

func scrollViewWillEndDragging(_ scrollView: UIScrollView, withVelocity velocity: CGPoint, targetContentOffset:
UnsafeMutablePointer<CGPoint>) {
    
    // 블럭 크기 = 블럭 가로 사이즈 + 블럭 여백
    let blockWidth = Size.blockSize.width + Size.blockSpacing
    
    // 스크롤된 크기 = 스크롤이 멈춘 x좌표 + 스크롤뷰 inset
    let scrollSize = targetContentOffset.pointee.x + scrollView.contentInset.left
    
    // 이전 블럭 인덱스 저장
    let rememberBlockIndex = blockIndex
    
    // 블럭 한칸 이상을 갔느냐 못갔느냐 체크
    let scrollSizeCheck = abs(startScrollX - targetContentOffset.pointee.x)
    
    // 2. 한칸만 이동하는 제스처
    if scrollSizeCheck <= blockWidth {
        
        // 왼쪽으로 한칸 이동하는 경우 (제스처 보정용 -30)
        if startScrollX-30 > targetContentOffset.pointee.x && blockIndex > 0 {
            blockIndex -= 1
        }
        
        // 오른쪽으로 한칸 이동하는 경우 (제스처 보정용 +30)
        if startScrollX+30 < targetContentOffset.pointee.x && blockIndex < blockManager.getCurrentBlockList().count {
            blockIndex += 1
        }
    }
    
    // 3. 한칸 이상 이동하는 제스처
    else { blockIndex = Int(round(scrollSize / blockWidth)) }
    
    // 최종 스크롤 위치 지정
    targetContentOffset.pointee = CGPoint(x: CGFloat(blockIndex) * blockWidth - scrollView.contentInset.left,
                                          y: scrollView.contentInset.top)
    
    // 사용자 터치 재활성화
    scrollView.isUserInteractionEnabled = true
    
    // 블럭 인덱스 업데이트
    if blockIndex != rememberBlockIndex { viewManager.blockCollectionView.reloadData() }
    blockManager.updateCurrentBlockIndex(blockIndex)
}
~~~

<img width="300" src="https://github.com/thinkySide/DayBlock/assets/113565086/7d038aca-0687-4f3f-87b3-5f05422c0a8d">

<br>

## 💌 Ref.

- [지혜님의 개발블로그](https://www.zehye.kr/ios/2020/03/09/11iOS_scroll_view/)