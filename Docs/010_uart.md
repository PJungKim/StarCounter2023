# UART로 PC와 통신하기

- UART로 PC와 통신하는 과정을 정리한 문서
- 사실상 Arduino 다루듯이 쓸 수 있게 설계해서 계수기 하드웨어 쪽에서는 UART 다루기가 매우 편합니다.
- 계수기 S/W쪽에서 시리얼 통신 받아오기가 다소 까다로워서 이에 대한 대책을 마련할 필요가 있습니다.

## 1. UART 함수

|함수|매개변수(인자)|설명|출력|
|--|--|--|--|
|`FTDI_Begin(baud)`|`baud` 보드레이트(초당 전송 비트)<br>9600, 38400, 115200중에 S/W와 협의하여 선정|FTDI를 사용할 수 있게 초기화합니다.|void|
|`FTDI_End()`|void|UART 통신을 끕니다.|void|
|`FTDI_Print(st)`|`st` 문자열(ref형)|FTDI를 통해 문자열을 출력합니다.|void|
|`FTDI_cout << st`|`st` 문자열(ref, str형)|FTDI를 통해 문자열을 출력합니다.|void|
|`FTDI_Write(byte)`|`byte` 1바이트 문자|FTDI에 8비트 데이터를 전송합니다.|void|
|`FTDI_Available()`|void|FTDI에 들어온 데이터 비트 수를 가져옵니다.|계수기에 들어온 비트 수(0~255)|
|`FTDI_Read()`|void|FTDI에 들어온 데이터를 1바이트씩 읽습니다.|읽어 온 데이터 값(0~255)|
|`FTDI_Scan(timeout, txt)`|`timeout` FTDI 수신 타임아웃(ms단위)<br>`st` 읽어올 문자열(ref형)|FTDI에 전송된 문자열을 읽습니다.|실제로 읽은 바이트 수(0~255)|

## 2. UART를 이용해 PC로 송신하기

- 버튼을 누르면 PC로 버튼 번호를 전송하겠습니다.

  |누른 버튼|전송 데이터|
  |--|--|
  |`BUTTON_1` 빨간 버튼|`1`|
  |`BUTTON_2` 파란 버튼|`2`|

- 소스 코드

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  
  main(){
      matrix = matrix_t(MATR_3);
      
      ///UART 초기화
      FTDI_Begin(9600);
      
      ///버튼 불 켜기
      PWM_Begin();
      PWM_Write(BUTTON_LED_RED, 64);
      PWM_Write(BUTTON_LED_BLUE, 255);
      
      ///9600Baud로 UART 초기화
      FTDI_Begin(9600);
      
      while(1){
          sw = SWITCH_Read();
          if(sw == SWITCH_1){
              FTDI_cout << "1\n";///줄바꿈 포함해서 출력.
          }
          elif(sw == SWITCH_2){
              FTDI_cout << "2\n";///줄바꿈 포함해서 출력.
          }
          elif(sw == SWITCH_BOTH){
              break;
          }
      }
      
  }
  ```

- 실행 결과

## 3. UART 수신 받기

- PC에서 전송한 숫자를 LED Matrix에 출력하기

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  
  main(){
      matrix = matrix_t(MATR_3);
      
      ///UART 초기화
      FTDI_Begin(9600);
      
      while(!SWITCH_Read()){
          #txt():str;
          ///FTDI가 수신되었을 때
          if(FTDI_Available()){
              FTDI_Scan(10, _txt);///문자열을 스캔
          }
          matrix.Print(_txt)with(RED);///빨간색으로 입력된 문자열 출력.
      }
      
  }
  ```

## 4. 합 표시하는 장치

- PC에서 숫자를 입력받은 뒤 LED Matrix에 그 값을 누적해서 출력합니다.
  - 예를 들어 1, 4, 6 순으로 입력하면 1->5->11 순으로 출력됩니다.
  - 출력 색상은 청록색으로 하겠습니다.
 
- 소스 코드

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  
  main(){
      matrix = matrix_t(MATR_3);
      
      ///UART 초기화
      FTDI_Begin(9600);
      
      ///숫자 입력 값
      sum = 0;
      
      while(!SWITCH_Read()){
          #txt():str;
          ///FTDI가 수신되었을 때
          if(FTDI_Available()){
              FTDI_Scan(10, _txt);///문자열을 스캔
              sum += Atoi(_txt);///숫자로 변환 후 누적.
          }
          matrix.Print("%2d" % sum)with(CYAN);///청록색으로 입력된 문자열 출력.
      }
      
  }
  ```

