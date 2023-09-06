# ✅ Xcode Commit Push 에러 - pathspec did not match any file(s) known to git

#### #Github #Xcode 

## 🤔 문제 정의

Xcode에서 새로운 커밋을 올리고 푸쉬하려는데 요상한 에러가 발생했다.   
대충 파일 이름이 달라져서 생긴 문제인 것 같은데,,, 어떻게 해결할 수 있을까?

<img width="300" src="https://github.com/thinkySide/BppleForImageData/assets/113565086/cd3ec036-ee4d-4cfa-863f-9f11d62b7a93">

<br>

## 🪓 삽질 기록

### 1. fetch 해보기

브랜치 업데이트가 안되면 그럴 수 있다 그래서 fetch를 해보았다.   
터미널에서도 해보고 xcode에 내장되어있는 기능으로도 해봤는데,,, 결과는 동일하다.

<img width="300" src="https://github.com/thinkySide/BppleForImageData/assets/113565086/4e292adf-7faf-4c21-928f-e78cabc755e0">

<br>

## 😈 문제 해결
Xcode가 워낙 버그가 많기 때문에 재실행 해봤더니 된다. 역시 fetch가 문제였던 것 같다! ~~~Xcode 수정좀 해줘 ㅡㅡ~~~