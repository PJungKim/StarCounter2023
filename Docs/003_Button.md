# 버튼 사용하기

- 계수기에는 2개의 버튼이 존재하며, 각각 빨강, 파랑 LED가 내장되어 있습니다.

  <image src = "..\Res\countersw.png" width="50%">

## 1. 버튼 연결 표

- 스위치

  |버튼|상수|
  |----|----|
  |빨간 버튼|`SWITCH_1`|
  |파란 버튼|`SWITCH_2`|
  |동시에 누름|`SWITCH_BOTH`|
  |빨강 길게|`SWITCH_1_LONG`|
  |파랑 길게|`SWITCH_2_LONG`|
  |동시 길게|`SWITCH_BOTH_LONG`|



- LED

  |LED|상수|값|
  |----|----|----|
  |빨강|`BUTTON_LED_RED`|1|
  |파랑|`BUTTON_LED_BLUE`|2|

- 빨간색 LED가 파란색 LED에 비해 밝기가 너무 밝기 때문에 이를 감안해서 밝기 제어를 하는 것을 권장합니다.
  - 파란색 LED를 255로 할 때, 빨간색 밝기는 64 정도로 쓰면 밸런스가 맞습니다.

## 2. 버튼 관련 각종 함수

### 2.1. 버튼의 불을 켜는 함수 - `PWM_Write(핀 번호, 값(0~255))`

- 버튼에 달린 LED에 불을 켜는 함수입니다.
- 핀 번호 : `BUTTON_LED_RED` 또는 `BUTTON_LED_BLUE` 중에 선택해서 작동시킬 수 있습니다.
- 값 : LED 밝기에 따라 값을 넣습니다.
  - 빨간색이 파란색에 비해 매우 밝기 때문에 빨간색은 0\~64, 파란색은 0\~255의 범위에서 조절하면 좋습니다.
  - 빨간색을 강조하고 싶다면 빨간색 값을 64보다 크게 해도 무방합니다.
 
### 2.2. 버튼을 인식하는 함수 - `SWITCH_Read()`

- 현재 버튼의 입력 상황을 출력하는 함수입니다.
- 라인트레이서의 `Custom_Switch_Read()` 함수와 비슷하게 작동합니다.
- 출력 값은 1번의 스위치 표와 같습니다. 스위치 값을 위의 상수와 비교해서 스위치가 동작했을 때 프로그램을 만들 수 있습니다.

## 3. 버튼을 눌러 숫자 조절하기

- 빨간 버튼을 눌러 숫자가 1이 커지게 하고, 파란 버튼을 눌러 숫자가 1이 작아지게 하기
- 0보다 작아지면 프로그램 종료
- 9보다 커져도 프로그램 종료
- 소스 코드[^주의]

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  
  
  
  main(){
      /// LED Matrix 초기화
      matrix = matrix_t(MATR_3);
  
      /// 버튼 불 켜기
      /// 누가 설계했는지는 모르지만 빨간색 LED 밝기가 너무 밝네요 ㅠㅠ
      PWM_Begin();
      PWM_Write(BUTTON_LED_RED, 64);
      PWM_Write(BUTTON_LED_BLUE, 255);
  
      /// 값 초기화
      value = 0;
      /// 버튼을 눌렀을 때 숫자 올리거나 내리기
      while(1){
          sw = SWITCH_Read();
          if(SWITCH_1 == sw){
              value += 1;
              if(9 < value){
                  break;
              }
              matrix.Print("%02d" % value) with (VIOLET);
          }
          elif(SWITCH_2 == sw){
              value -= 1;
              if(0 > value){
                  break;
              }
              matrix.Print("%02d" % value) with (VIOLET);
          }
      }
      PWM_End();
  }
  ```

- 실행 결과
  - `PWM_Write(BUTTON_LED_RED, 64);`, `PWM_Write(BUTTON_LED_BLUE, 255);` : 아래 그림처럼 버튼 LED가 켜집니다.

    <image src="..\Res\Examples\003_Button0.jpg" width="25%">

  - 버튼을 누르면 숫자가 올라갔다 내려가며, 범위를 초과하면 프로그램은 꺼집니다.
 
    <image src="..\Res\Examples\003_Button1.jpg" width="25%">
    <image src="..\Res\Examples\003_Button2.jpg" width="25%">
    <image src="..\Res\Examples\003_Button3.jpg" width="25%">


[^주의]: $import는 현재 디렉터리 기준 상대 위치로, 위치가 맞지 않으면 파일 임포트 오류가 발생합니다.
