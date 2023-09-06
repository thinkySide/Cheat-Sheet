# ✅ 상단에 고정되는 StickyHeader 구현하기

#### #기능구현 #UIScrollView #StickyHeader #UIKit 

## **🔍** 작성 목적

최상단에 고정할 View를 구성하려 한다. (CSS의 fixed-position과 같은 구현!)

<img width="500" src="https://user-images.githubusercontent.com/113565086/229415352-11267269-60cf-4184-b23c-686e7c75b77b.png">


<br>

## 📌 적용 방법

### 1. 그냥 ScrollView 밖에다가 만들자

뭔가 거창한 구현을 생각해서 이것저것 찾아봤는데 나중에 알고보니 ScrollView 밖에다가 두면 됐었다...😅   
단순 Top, Bottom 고정을 원할 때는 ScrollView 밖에다가 만들자!   
(이런 구현과 별개로 중간에 착! 붙는 View를 만들어야 할 때 다시 찾아보도록 하자.)