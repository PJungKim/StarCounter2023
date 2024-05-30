# LED Matrix 다루기

## 1. 숫자 출력하기

- 라인트레이서가 주행을 시작하면 LED Matrix에 실시간으로 시간을 측정해서 보여 줄 수 있습니다. 이를 위해 숫자를 출력하는 프로그램을 만들어 보겠습니다. 2자리 출력은 숫자뿐 아니라 문자도 출력 가능합니다.

### 1.1. 2자리 문구 출력 함수 : `(matrix_t).Print("ZT") with (RED);`

- 반드시 위와 같이 매개변수를 입력해야 합니다. 잘못된 문법 : `(matrix_t).Print("ZT", RED);`
- 매트릭스 객체에 Print만 사용하는 경우 텍스트가 Scroll되며, 뒤에 with를 붙이면 2자리 문구가 출력됩니다.
- 2자리 문구 출력은 개별적으로 색을 정하지 못하고 반드시 with 뒤에 추가적인 매개변수를 붙여야 색을 정할 수 있습니다.

- 예제 : 2자리 문구 67 출력하기
  ```
  matrix1.Print("67") with (RED);
  ```

#### 1.1.1. 색상 표

  |색상|상수|값|색상|상수|값|
  |--|--|--|--|--|--|
  |빨강|RED|0xFF0000|주황|ORANGE|0xFF3300|
  |노랑|YELLOW|0xFFAA00|연두|GREENYELLOW|0xAAFF00|
  |초록|GREEN|0x00FF00|청록|CYAN|0x00FFFF|
  |파랑|BLUE|0x0000FF|보라|VIOLET|0x6600FF|
  |자홍|MAGENTA|0xFF0066|흰색|WHITE|0xFFFFFF|



### 1.2. 2자리 숫자 출력하기

- (matrix_t).Print 함수에 `%02d` 등을 사용하여 숫자를 출력할 수 있습니다.
- 예제 : 현재시간(초) 출력하기
  ```
  matrix1.Print("%02d" % (Tick() / 1000 % 100)) with (BLUE);
  ```

## 2. (예제) 1초마다 1부터 10까지 늘어나는 계수기 디스플레이

- 소스 코드[^주의][^미확인]

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);

  main(){
      ///LED Matrix 초기화
      matrix = matrix_t(MATR_3);

      ///i가 1부터 10까지 반복
      for(i)from(1)to(10){
          ///빨간색으로 숫자를 순차적으로 출력
          matrix.Print("%02d" % i) with (RED);
          ///1초 기다리기
          Delay(1000);
      }
  }
  ```

- 실행 결과

  - 아래 그림과 같이 1부터 10까지 카운트하고 프로그램을 종료합니다.

    <image src = "..\Res\Examples\002_Matrix0.jpg" width="25%">
    <image src = "..\Res\Examples\002_Matrix1.jpg" width="25%">
    <image src = "..\Res\Examples\002_Matrix2.jpg" width="25%">


[^주의]: $import는 현재 디렉터리 기준 상대 위치로, 위치가 맞지 않으면 파일 임포트 오류가 발생합니다.
[^미확인]: 검증이 되지 않은 코드로, 컴파일 오류가 발생할 여지가 있습니다.

  
