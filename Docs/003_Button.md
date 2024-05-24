# 버튼 사용하기

- 계수기에는 2개의 버튼이 존재하며, 각각 빨강, 파랑 LED가 내장되어 있다.

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

  |LED|상수|
  |----|----|
  |빨강|`BUTTON_LED_RED`|
  |파랑|`BUTTON_LED_BLUE`|

## 2. 버튼을 눌러 숫자 조절하기

- 빨간 버튼을 눌러 숫자가 1이 커지게 하고, 파란 버튼을 눌러 숫자가 1이 작아지게 하기
- 0보다 작아지면 프로그램 종료
- 9보다 커져도 프로그램 종료

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);

  main(){
      /// 버튼 불 켜기
      /// 누가 설계했는지는 모르지만 빨간색 LED 밝기가 너무 밝네요 ㅠㅠ
      PWM_Write(BUTTON_LED_RED, 64)
      PWM_Write(BUTTON_LED_BLUE, 255)

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
          }
          elif(SWITCH2 == sw){
              value -= 1;
              if(0 > value){
                  break;
              }
          }
      }
  }
  ```
