# ✅ 현재 선택된 UITableView의 indexPath 구하기

#### #UITableView #UIKit 

## **🔍** 작성 목적

평소에 UITableView의 선택된 indexPath를 구하기 위해서 `didSelectRowAt` 메서드에서만 활용하면 됐었다.   
하지만 구현 중에 다른 곳에서도 사용해야 할 곳이 있어 고민한 흔적을 남기려 한다.

<br>

## 📌 적용 방법

### 1. 원래는 전역변수를 만들어 구현하려고 했었다.

전역 변수를 하나 만들어 `didSelectRowAt` 메서드가 실행될 때마다 업데이트 해주는 방식을 사용하려 했으나,,,   
뭔가 애플이라면 무언가 만들어 놓지 않았을까 싶어서 찾아봤다!!

<br>

### 2. indexPathForSelectedRow 활용하기

역시나 있었고 사용 방법 또한 매우 간단했다 ㅎㅎ   
`indexPathForSelectedRow` 는 cell의 indexPath를 반환해주는 인스턴스 프로피티이다!

~~~swift
if let indexPath = viewManager.tableView.indexPathForSelectedRow {
    // 요기서 indexPath를 지지고 볶자
}
~~~

<br>

## 💌 Ref.

- [정주님의 tistory](https://jeong9216.tistory.com/170)


## 🪁 Obsidian Link
- [[UITableView를 코드로 만들 때 컴포넌트가 출력되지 않는 문제]]