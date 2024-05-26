# 스위치 방향과 화살표

- 스위치 방향을 보는 함수와 그에 따라 화살표 방향, 화살표 색을 설정하는 문서입니다.

## 1. 스위치 방향 보기 - `GetMode()`

<image src="..\Res\countersw.png" width="50%">

- 전원 스위치를 켜는 방향에 따라 주행 방향을 다르게 만들 수 있습니다.

- 스위치 방향을 보는 함수는 `GetMode()` 함수를 사용할 수 있습니다.

  |스위치 방향|주행 방향|값|
  |--|--|--|
  |왼쪽 방향|왼쪽|`1`|
  |오른쪽 방향|오른쪽|`0`|

- 계수기 설치 규격에 따라 계수기 내부 IDC 케이블이 연결되는 것을 기준으로 계수기 위치를 정할 수 있습니다.
  - 왼쪽 방향인 경우 : Master가 출발점, Slave가 도착점
  - 오른쪽 방향인 경우 : Master가 도착점, Slave가 출발점

### 1.1 (예제) 스위치 방향에 따라 0 또는 1 출력하기

- 스위치가 왼쪽으로 켜지면 1, 오른쪽으로 켜지면 0을 출력하는 코드

```
$import(..\..\counter_HW\studio.shc);
$import(..\..\counter_HW\matrix.shc);
$target(counter.sbc);

main(){
    ///LED Matrix 객체를 가져온다.
    matrix = matrix_t(MATR_3);
    ///LED Matrix에 노란색으로 스위치가 켜진 방향을 출력한다.
    matrix.Print("%d" % GetMode()) with (YELLOW);
}
```

## 2. 화살표 작동하기 - `(matrix_t).SetColor(색상)`, `(matrix_t).Arrow(방향)`

- 화살표는 주행 방향을 나타내는 표시로, 실제 대회 참가자가 주행 방향을 잘못 보는 일이 없게 합니다.
- 화살표를 출력하기 전 화살표의 색을 정해야 하며, `(matrix_t).SetColor(색상)`의 형태로 사용합니다.
- 화살표 함수는 `(matrix_t).Arrow(방향)`의 형태로 사용할 수 있습니다.

- 색상(2024년 5월 25일부터는 색상 코드 뒤에 1 붙이지 않음.)

  |색상|상수|값|색상|상수|값|
  |--|--|--|--|--|--|
  |빨강|RED|0xFF0000|주황|ORANGE|0xFF3300|
  |노랑|YELLOW|0xFFAA00|연두|GREENYELLOW|0xAAFF00|
  |초록|GREEN|0x00FF00|청록|CYAN|0x00FFFF|
  |파랑|BLUE|0x0000FF|보라|VIOLET|0x6600FF|
  |자홍|MAGENTA|0xFF0066|흰색|WHITE|0xFFFFFF|

- 화살표 방향

  |방향|상수|값|
  |--|--|--|
  |오른쪽|ARROW_RIGHT|0|
  |왼쪽|ARROW_LEFT|2|
  |위|ARROW_UP|1|
  |아래|ARROW_DOWN|3|

### 2.1. 화살표 색상의 변경

- 화살표가 켜진 상태에서 `(matrix_t).SetColor(색상)`을 사용하면 화살표의 색이 부드럽게 바뀝니다.
- 색이 바뀌는 과정에서 이 함수를 다시 사용하면 오작동할 수 있습니다.

### 2.2 (예제) 스위치 켜진 방향에 따라 화살표 켜기

```
$import(..\..\counter_HW\studio.shc);
$import(..\..\counter_HW\matrix.shc);
$target(counter.sbc);

main(){
    ///LED Matrix 객체를 가져온다.
    matrix = matrix_t(MATR_4);

    ///스위치 켜진 방향 저장하기
    mode = GetMode();

    ///LED Matrix 색상을 초록색으로 설정
    matrix.SetColor(GREEN);

    if(mode){///왼쪽 방향으로 켜진 경우
        matrix.Arrow(ARROW_LEFT);
    }
    else{
        matrix.Arrow(ARROW_RIGHT);
    }

    ///스위치 눌러 프로그램 종료
    while(!SWITCH_Read()){}
}
```
