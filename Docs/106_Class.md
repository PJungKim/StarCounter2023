# 객체와 클래스

- Starlit에서 객체와 클래스에 대해 설명한 문서입니다.

## 1. Class

- 객체의 모양을 나타내는 개념입니다.
- Class에는 속성과 메소드로 구성되어 있습니다.
  - 속성 : 객체의 특성을 나타내는 변수를 의미.
  - 메소드 : 객체의 기능을 의미합니다.
 
## 2. Class 정의 예제

```
$class(building){
    ///속성을 정의하는 부분
    #name:str;
    #floor:int;

    ///메소드를 정의하는 부분
    Init(name:ref){
        strcpy(_(self.name), name);
        self.floor = 0;
    }

    Construct(){
        self.floor += 1;
    }

    Destruct(){
        self.floor -= 1;
    }
}
```
~~건물을 부수다니....~~

### 2.1. 설명

- 속성은 괄호 없이 `#floor:int;`와 같이 정의합니다. 변수 정의와 미묘하게 다릅니다.
- 메소드는 함수 정의하듯이 정의하면 됩니다. 다만, 함수와는 다르게 그 클래스의 객체에 대한 함수로 정의됩니다.
- `self`는 자기 자신의 객체를 받은 것을 의미합니다. 실질적으로 `A.Init("B")`는 `Init(A, "B")`와 같으며, 정의 역시 `Init(self:_building, name:ref)`과 같습니다.
- `self` 자체는 Call-By-Reference로 동작하므로 함수 내에서 자신의 속성을 바꿀 수 있습니다.[^속성변경]
  - 즉, `(건물).Construct()`를 쓰면 1층 증축이 되고, 반대로 `(건물).Destruct()`를 쓰면 1층 파괴됩니다.

## 3. Class 사용 예제 - 자주 사용하는 Class를 반환하는 함수

```
GetBuilding(name:ref, floor:int):building{
    #bd():building;
    bd.Init(name);
    bd.floor = 0;
    return(bd);
}
```
~~아직 지어지 않은 건물의 부지를 반환하는건가?~~

## 4. Class 사용 예제 - 실질적으로 사용하기

```
main(){
    jeonnong = building("Cheonnong_Hall", 2);
    jeonnong.Construct();
    mirae = building("Mirae_Hall", 13);
    mirae.Destruct();
}
```
~~건물 증축, 파괴하기 참 쉽다 그쟈??~~

## 5. Public, Private, Protected

- Starlit에서는 암묵적 룰을 적용해 Privated 변수를 사용하지 않는 것을 원칙으로 합니다.
  - Privated 변수를 사용한다고 해서 컴파일 오류는 안 뜨지만, 권장하지는 않습니다.
  - `name` : Public 속성을 가진 속성 또는 메소드
  - `name_` : Protected 속성을 가진 속성 또는 메소드
  - `name__` : Privated 속성을 가진 속성 또는 메소드~~라고 하지만, 상속도 잘 되기 때문에 별 의미는 없다.~~
 
    



[^속성변경]: Starlit 3.1 업데이트 전까지 Call-By-Reference가 정의되어 있지 않아 속성 변경이 어려웠다.
