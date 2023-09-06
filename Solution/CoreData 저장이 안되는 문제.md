# ✅ CoreData 저장이 안되는 문제

#### #CoreData #Swift 

## 🤔 문제 정의

오랜만에 다시 시작한 DayBlock 프로젝트, 가장 중요한 CoreData를 적용하기 위해 전체적으로 갈아 엎는중,, 분명 잘 저장하고 있는 거 같은데 앱 종료 후 다시 들어가보면 초기화되어있는 상황이 계속되어 어떤 문제인지 알아보기로 했다.

<img width="300" src="https://github.com/thinkySide/DayBlock/assets/113565086/9eb6861e-2a2d-40e7-adf9-cc35847788fa">

~~~swift
// blockManager.swift

/// 비즈니스 로직용 배열
private var groupList = [Group(name: "그룹 없음", color: 0x323232, list: [])]

/// 코어데이터용 배열
var groupEntity: [GroupEntity] = []
    
/// 컨텍스트
let context = (UIApplication.shared.delegate as! AppDelegate).persistentContainer.viewContext

/// Entity Fetch
func getAllItems() {
        do {
            // Entity 불러오기
            groupEntity = try context.fetch(GroupEntity.fetchRequest())
                
            // 비즈니스용 배열에 Entity 값 불러와서 추가하기
            for item in groupEntity {
                groupList.append(Group(name: item.name, color: item.color, list: []))
            }
        } catch {
            // error
        }
    }

// Create Item
func createItem(group: Group) {
        
    // 새로운 아이템 생성
    let newItem = GroupEntity(context: context)
    newItem.name = group.name
    newItem.color = group.color
        
    // 컨텍스트에 저장
    do {
        try context.save()
    } catch {
        // error
    }
}

// 그룹 생성
func createNewGroup() {
    let newGroup = remoteGroup
    createItem(group: newGroup)
    getAllItems()
    resetRemoteGroup()
}
~~~

<br>

~~~swift
// HomeViewController.swift
override func viewDidLoad() {
    super.viewDidLoad()
    blockManager.getAllItems()
}
~~~

<br>

~~~swift
// CreateGroupViewController.swift
func createGroup() {
    /// 리모트 그룹 업데이트
    guard let groupName = viewManager.groupLabelTextField.textField.text else { return }
    blockManager.updateRemoteGroup(name: groupName)
        
    /// 그룹 생성
    blockManager.createNewGroup()
        
    /// selectView의 그룹 리스트 업데이트
    delegate?.updateGroupList()
    dismiss(animated: true)
}
~~~

<br>

## 🪓 삽질 기록

### 1. 테스트 프로젝트 만들어보기
DayBlock 프로젝트에서 구현하려고 하는 게 나름(?) 어려워 다른 로직때문에 그런가? 라고 생각을 해 테스트 프로젝트를 따로 파 작성해봤다. 결과는 똑같다,, 뭔가 코어데이터를 불러오는 거 같기는 한데 저장이 안되는,,? 무슨 현상일까

<img width="300" src="https://github.com/thinkySide/DayBlock/assets/113565086/698a0869-801c-4066-a44a-ea667f169409">

<br>

### 2. Entity 바꿔보기
GroupList Entity에서는 [Block] 커스텀 타입을 담기위해 transformable 타입을 사용하고 있었다. 요게 문제인가 싶어서 기본으로 제공하는 타입으로만 새로 Entity를 만든 후 테스트 해봤는데! 이제는 저장이 된다. 문제는 transformable에서 나오고 있었던 것! 어떤 문제 때문인지 파악해야할 차례이다.

<br>

### 3. 커스텀 타입 말고 관계형 데이터베이스로 설정해보기
[Block]이라는 커스텀 타입에 담을려고 하다보니 레퍼런스가 많지 않고, 문제가 많이 생기는 것 같아 다른 방법이 없을까 찾아봤다. 관계형으로 설정해 Group안에 Block을 가지는 형태로 만들어봤더니 데이터를 잘 불러오는 것을 확인할 수 있었다! (transformable 타입 연구할 시간에 CoreData 자체를 더 깊게 들여다봤으면 진작 찾을 수 있었을 것 같았던 부분이었다,, 반성)

<img src="https://github.com/thinkySide/DayBlock/assets/113565086/4b1fa33f-a3e3-427e-8dcd-f0e950ee7470">

<br>

## 😈 문제 해결

거의 2주일 정도의 삽질 끝에 해결한 CoreData,, 느낀점이 굉장히 많다. 이전에도 다짐했던 어떤 프레임워크든 라이브러리든 사용하기 전 기본적인 내용은 필히 숙지하고 사용할 것, 그리고 앱의 Model부분은 프로젝트 초반에 모두 구성해놓고 UI작업으로 들어갈 것(이거 때문에 코드를 다시 수정하는 시간도 굉장히 길어졌다,,) 어찌되었든 잘 해결되었고, 나머지 부분들도 잘 구성해볼 예정이다!

<img width="300" src="https://github.com/thinkySide/Cheat-Sheet/assets/113565086/9c0c136e-264a-4a95-8a08-8dcaaf72bda5">

<br>

## 💌 Ref.

-[jake-kim님의 tistory](https://ios-development.tistory.com/93)


## 🪁 Obsidian Link
- 