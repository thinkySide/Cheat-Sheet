# ✅ UI Component화 하기

#### #Publishing #Component #UIKit 

## **🔍** 작성 목적

레이아웃을 잡을 때 가장 시간이 많이 걸리는 작업이 반복적인 컴포넌트들을 작성하는 것인 것 같다.   
자주 사용하는 컴포넌트 들을 클래스 등을 만들어 관리할 수는 없을까? Figma에서 컴포넌트로 만들어서 재사용 했던 것 처럼 말이다!

<br>

## 📌 적용 방법

### 1. 컴포넌트로 만들 것들 상속 받아 만들기

자주 사용하는 TopTabBar, ButtonStackView 등등을 어떤 것을 상속 받아 만들지 결정하고 만들어주기만 하면 된다! 어렵게 생각할 필요 없이 UIView로 묶어도 좋고, UIStackView로 묶어도 좋다. 중요한 것은 컴포넌트로서 사용 가능하게 init 할 때부터 커스텀 가능한 메서드를 만들거나, 재사용을 위한 부분들을 신경써주는 것이다. 다음 코드는 액션 버튼을 만들기 위해 만들어둔 컴포넌트이다.

~~~swift

import UIKit

final class ActionButton: UIButton {
    
    // 버튼 모드 열거형
    enum Mode {
        case confirm
        case cancel
    }
    
    
    init(frame: CGRect, mode: Mode) {
        super.init(frame: frame)
        
        // 버튼 모드에 따라 다른 모양으로 생성
        switch mode {
        case .confirm:
            self.setBackgroundColor(GrayScale.mainText, for: .normal)
            self.titleLabel?.font = UIFont(name: Pretendard.semiBold, size: 17)
            self.setTitleColor(.white, for: .normal)
            self.setTitle("확인", for: .normal)
            
        case .cancel:
            self.setBackgroundColor(GrayScale.cancelButton, for: .normal)
            self.setBackgroundColor(GrayScale.entireBlock, for: .highlighted)
            self.titleLabel?.font = UIFont(name: Pretendard.semiBold, size: 17)
            self.setTitleColor(GrayScale.subText, for: .normal)
            self.setTitle("취소", for: .normal)
        }

        /// Corener Raduis
        self.clipsToBounds = true
        self.layer.cornerRadius = 13
        
        /// Size
        self.heightAnchor.constraint(equalToConstant: 56).isActive = true
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
~~~


## 🪁 Obsidian Link
- [[UIView에 Gradient 적용 및 안되는 문제?]]
- [[커스텀 Font 사용하기]]
- [[Dashed Line 만들기]]
- [[특정 모서리에만 cornerRadius 값 주기]]
- [[유동적인 사이즈의 컴포넌트 생성하기]]