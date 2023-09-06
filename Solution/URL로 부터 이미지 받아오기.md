# ✅ URL로 부터 이미지 받아오기

#### #Networking #URL #Swift 

## 🤔 문제 정의
image를 포함하고 있는 url에서 데이터를 받아와 UIImage로 변환해야 한다.   
예전에 해본적이 있어 쉽게 될 줄 알았는데 잘 되지 않는다... 어떤 이유 때문일까?

~~~swift
func networkTest() {

    // URL 생성
    guard let url = URL(string: "http://localhost:8000/중략") else {
        print("URL 생성 실패")
        return
    }

    // URLSession
    URLSession.shared.dataTask(with: url) { data, response, error in

        // error 체크
        if error != nil {
            print(error!.localizedDescription)
            return
        }
        // data 체크
        if let safeData = data {
            print("데이터 추출 성공: \(safeData)")
            if let image = UIImage(data: safeData) {
                print("이미지 생성 성공: \(image)")
            } else {
                print("이미지 생성 실패")
                return
            }
        } else {
            print("데이터 생성 실패")
        }

    }.resume()
}

// 데이터 생성 성공: 21 bytes
// 이미지 생성 실패
~~~

<br>

## 🪓 삽질 기록

### 1. Data(contentsOf:) 메서드 사용
URL 타입으로 데이터를 생성할 수 있는 메서드가 있어서 사용해봤다.   
하지만 이 방법은 데이터 생성조차도 실패하게 된다.

~~~swift
func networkTest() {

    // URL 생성
    guard let url = URL(string: "http://localhost:8000/중략") else {
        print("URL 생성 실패")
        return
    }

        // Data(contentsOf:) 메서드 사용
    if let data = try? Data(contentsOf: url) {
        print("데이터 생성 성공: \(data)")
        if let image = UIImage(data: data) {
            print("이미지 생성 성공: \(image)")
        } else {
            print("이미지 생성 실패")
        }
    } else {
        print("데이터 생성 실패")
    }
}

// 데이터 생성 실패
~~~

<br>

## 😈 문제 해결

URL 문자열 내 공백이 제대로 변환되지 않은 문제였다,,,(변환과는 별개로 젼혀 다른 문제였다 ㅡㅡ)   
공백을 `%20` 으로 처리해주어야 했었는데, `%` 로만 처리해줘서 유효하지 않은 URL이었던 것!   
Data to UIImage 방법은 위의 방법이 맞았었다.