# ✅ LongPress Animation 만들기

#### #UILongPressGestureRecognizer #Gesture #UIKit 

## 🤔 문제 정의

DayBlock 프로젝트에서 작업을 완료할 때 사용하는 버튼으로 블럭을 길게 누르면(Long Press) `왼쪽에서 오른쪽으로 배경색이 칠해지는 애니메이션`을 구현해볼 것이다. 재미있는 요소를 추가하고 싶은 마음 + 새로운 도전의 느낌으로 만들어보려고 하는데 역시나 쉽지 않다,, 검색해봐도 SwiftUI 레퍼런스는 꽤 나오지만 UIkit을 사용해서 하는 예제를 찾기 힘들어 직접 구현해보려 한다.

<img src="https://github.com/thinkySide/DayBlock/assets/113565086/5bdbacd3-48ac-4fcc-9de4-9ed2163a7804">

<br>

## 🪓 삽질 기록

### 1. 테스트용 프로젝트 파기

우선 DayBlock 프로젝트에 바로 적용하기 보다, 애니메이션을 뜯어볼 수 있도록 새 프로젝트를 팠다.

~~~swift
// 테스트용 블럭
let block: UIView = {
    let view = UIView()
    view.backgroundColor = UIColor(rgb: 0xF4F5F7)
    return view
}()

// 테스트용 아이콘
let icon: UIImageView = {
    let image = UIImageView()
    image.contentMode = .scaleAspectFill
    image.tintColor = UIColor(rgb: 0x323232)
    image.image = UIImage(systemName: "bag.fill")
    return image
}()
~~~

대충 요런 화면이다.

<img width="300" src="https://github.com/thinkySide/DayBlock/assets/113565086/ecef4d12-92b3-489e-b7a0-e2000090f70b">

<br>

### 2. LongTapGestrue 붙이기

다음은 block에 `LongTapGesture`를 붙여주었다. 평소 `TapGesture`를 자주 붙여봐서 익숙한 패턴이다.

~~~swift
override func viewDidLoad() {
    super.viewDidLoad()
    setupEvent()
}

func setupEvent() {
    let gesture = UILongPressGestureRecognizer(target: self, action: #selector(blockLongPressed)) // LongPressGesture 추가
    gesture.minimumPressDuration = 0.1 // 최소 눌러야 하는 시간
    block.addGestureRecognizer(gesture) // 어디에? 블럭에!
}

@objc func blockLongPressed(_ gestrue: UILongPressGestureRecognizer) {
    switch gestrue.state {
        
    case .began:
        print("제스처 시작")
        
    case .ended:
        print("제스처 종료")
        
    default:
        // 나머지 상태는 안쓸 것 같아서 일단 default로 묶어놨다.
        break
    }
}
~~~

<br>

### 3. CABasicAnimation 활용

본격적으로 애니메이션을 구현하기 위해 첫번째로 찾은 방법은 `CABasicAnimation` 클래스 활용이었다. `keypath`를 활용해 다양한 요소에 애니메이션 효과를 줄 수 있어서 공식문서를 조금 뜯어본 후 코드를 작성해봤다.

~~~swift
// subLayer로 붙이기 위한 CALayer
let animationLayer: CALayer = {
    let layer = CALayer()
    layer.backgroundColor = UIColor(rgb: 0xDAEBFE).cgColor
    return layer
}()

// CALayer에 붙일 실제 애니메이션
let basicAnimation: CABasicAnimation = {
    let animation = CABasicAnimation(keyPath: "bounds.size.width") // 내가 조정할 값은 bounds.size.width임!

    // bounds.size.width = 0 에서 시작해 
    // bounds.size.width = block.bounds.width * 2
    // 으로 향하는 애니메이션을 준다는 뜻
    animation.fromValue = 0 // 애니메이션 시작값
    animation.toValue = block.bounds.width * 2 // 애니메이션 종료값
    
    animation.duration = 0.7 // 애니메이션 지속 시간
    animation.fillMode = .backwards // 애니메이션 종료 후 값(초기 상태로 돌아갈 것)
    animation.isRemovedOnCompletion = false // 애니메이션 종료 후 애니메이션 제거 여부
    animation.timingFunction = CAMediaTimingFunction(name: .easeIn) // 애니메이션 효과

    return animation
}()

// 채우기용 애니메이션
func fillAnimation() {

    // 레이어 프레임 설정(처음 width는 0에 height는 block의 높이와 같게 설정)
    animationLayer.frame = CGRect(x: 0, y: 0, width: 0, height: block.bounds.height)

    // 블럭의 subLayer로 추가
    block.layer.addSublayer(animationLayer)

    // 애니메이션 레이어 추가
    animationLayer.add(basicAnimation, forKey: "widthAnimation")
}

@objc func blockLongPressed(_ gestrue: UILongPressGestureRecognizer) {
    switch gestrue.state {
        
    case .began:
        print("제스처 시작")
        fillAnimation() // 여기서 채우기 애니메이션 시작해야됨.
        
    case .ended:
        print("제스처 종료")
        
    default:
        break
    }
}
~~~

<img width="300" src="https://github.com/thinkySide/DayBlock/assets/113565086/253d1544-dc9b-496f-980f-841c5404ddcd">

<br>

### 4. CABasicAnimation 문제 발견

우선 왼쪽에서 오른쪽으로 색이 채워지는 애니메이션과 정반대의 `fromValue`와 `toValue`를 주면 다시 색이 지워지는 애니메이션도 구현이 가능했다. 하지만 문제는 LongPressGesture가 종료되었을 때 색이 얼마나 칠해졌는지에 대한 값을 알아올수가 없다는 것이었다.(방법이 있을 수도 있으나 나는 못찾았다,,) 이게 왜 문제냐면 잠깐 Press했다가 제스처를 종료해도 항상 끝에서부터 지워지는 애니메이션이 실행되다보니 부자연스럽게 뚝뚝 끊기는 것,, 그래서 다른 방법으로 시도해보고자 한다.

<br>

### 5. 처음부터 색을 칠해놓고 그 위에 레이어를 얹어서 이동시키기

구현이 가능할지 모르겠지만 우선 로직을 생각해봤다.

1. 먼저 블럭의 배경색 자체를 처음부터 색칠된 색상으로 적용시켜 놓는다.
2. 그 위에 원래 블럭의 배경색을 가진 View를 얹어 놓는다.
3. LongPressGesture가 눌리는 시간에 따라 해당 View의 xPosition을 조정해 차오르는 것처럼 보이게 만든다! ⭐️
4. 만약 LongPressGesture가 종료된다면 즉시 xPostion의 이동을 멈추고 해당 위치를 저장해 놓는다.(요걸로 다시 돌아가는 애니메이션 구현)

<img src="https://github.com/thinkySide/DayBlock/assets/113565086/0ddb0106-9492-4455-b7f3-9fb7a855872f">

~~~swift
let block: UIView = {
    let view = UIView()
    view.backgroundColor = UIColor(rgb: 0xDAEBFE)
    return view
}()

/// 얹을 View 추가
let fillLayer: UIView = {
    let view = UIView()
    view.backgroundColor = UIColor(rgb: 0xF4F5F7)
    return view
}()

@objc func blockLongPressed(_ gestrue: UILongPressGestureRecognizer) {
    switch gestrue.state {
        
    case .began:
        print("제스처 시작")

        // animate 메서드를 이용해 x값의 변화를 주었다. (값을 적절히 적용해 애니메이션 커스텀)
        UIView.animate(withDuration: 1.3, delay: 0, usingSpringWithDamping: 1, initialSpringVelocity: 0.5) {
            self.fillLayer.frame.origin.x = self.block.frame.width
        }
        
    case .ended:
        print("제스처 종료")

        // 제스처 종료 시점에 origin 값을 기준으로 0으로 되돌린다.
        UIView.animate(withDuration: 0.7, delay: 0, usingSpringWithDamping: 1, initialSpringVelocity: 0.5) {
            self.viewManager.fillLayer.frame.origin.x = 0
        }
        
    default:
        break
    }
}
~~~

https://github.com/thinkySide/Cheat-Sheet/assets/113565086/855f693d-5bf7-4d1b-94b6-67b68235cb74

결과는 대성공! 오히려 처음 방법보다 더 쉬웠던 것 같다,,(이래서 기본적인 거 응용이 중요한듯) 이제 더 커스텀도 진행해봐야겠다!

<br>

### 6. 애니메이션 끝나는 시점 정의

UIView의 `animate` 메서드는 `completion`을 통해 애니메이션 종료 시점을 특정할 수 있다. 이를 이용해 단순히 completion 내부에 실행 코드를 작성해봤지만,, 내가 원하는 것과는 동일하게 작동하지 않았다. 원인은 completion은 `@escaping` 키워드로 이루어져있어 메서드 종료 이후에도 살아남아 꾸역꾸역 실행하기 때문,,!(이것 때문에 채우는 애니메이션을 중간에 끊고, 다시 지워나가는 도중 모두 채워졌다는 completion 블럭 안의 코드가 실행되었다.) 이를 해결하기 위해 completion을 컨트롤할 수 있는 전역변수를 만들고 실행이 원할 때는 그대로의 코드를, 아닐 때는 nil을 할당해 원하는 결과물을 만들었다.

~~~swift
 /// 애니메이션 Completion 관리용 클로저
var storeTrackingBlockClosure: (() -> Void)?

/// 트래킹 블럭 저장 클로저 할당 및 델리게이트 실행
func setupStoreTrackingBlock() {
    storeTrackingBlockClosure = {
        self.delegate?.storeTrackingBlock()
    }
}

@objc func blockLongPressed(_ gestrue: UILongPressGestureRecognizer) {
    switch gestrue.state {
        
    case .began:
        print("제스처 시작")

        UIView.animate(withDuration: 0.9, delay: 0.15, usingSpringWithDamping: 1, initialSpringVelocity: 0.1) {
            self.fillLayerBlock.transform = CGAffineTransform(translationX: self.frame.width, y: 0)
        } completion: { _ in

            // storeTrackingBlockClosure이 nil이 아닐 때만 아래 구문 실행
            if let storeClosure = self.storeTrackingBlockClosure {
                storeClosure()
                return
            }
        }
        
    case .ended:
        print("제스처 종료")

        // 중간에 제스처가 해제될 때는 CompletionHandler가 실행되면 안되기 때문에 nil 할당
        storeTrackingBlockClosure = nil

        UIView.animate(withDuration: 0.9, delay: 0, usingSpringWithDamping: 1, initialSpringVelocity: 0.1) {
            self.fillLayerBlock.transform = CGAffineTransform(translationX: 0, y: 0)
        } completion: { _ in
            // 애니메이션 지우는 시점도 호출 가능
        }
        
    default:
        break
    }
}
~~~

최종 DayBlock 내 적용 모습이다. 이것저것 뜯어보며 시간이 오래걸리긴했지만 만족스러운 결과물이다! 영상에서는 나오지 않지만 다 채워지는 시점에 블럭 저장 메서드도 정상적으로 작동된다.😀

https://github.com/thinkySide/Cheat-Sheet/assets/113565086/6075c07d-7f11-4309-aa69-daf3e4b72b9f


## 🪁 Obsidian Link
- [[StackView에 Touch Event 추가하기]]