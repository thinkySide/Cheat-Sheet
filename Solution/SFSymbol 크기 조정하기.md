# ✅ SFSymbol 크기 조정하기

#### #SFSymbol #Swift 

## **🔍** 작성 목적

버튼에 `SFSymbol`을 넣어서 `widthAnchor`, `heightAnchor`를 잡아줬는데 크기가 바뀌지 않는다!   
자주 사용하는 Symbol의 사이즈를 직접 조정하는 코드를 정리해본다.

<br>

## 📌 적용 방법

### 1. SymbolConfiguration 생성하기

SymbolConfiguration : "심볼 이미지에 적용 할 특정 글꼴, 크기, 스타일 및 무게 속성이 포함 된 객체."

~~~swift
let configuration = UIImage.SymbolConfiguration(pointSize: 24, weight: .regular)
~~~

<br>

### 2. UIImage(systemName:)에 적용하기

요러면 깔끔하게 적용 끝!

~~~swift
button.setImage(UIImage(systemName: "plus", withConfiguration: configuration), for: .normal)
~~~

<br>

## 💌 Ref.

- [stackOverFlow](https://stackoverflow.com/questions/60641048/change-a-sf-symbol-size-inside-a-uibutton)