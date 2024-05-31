# 센서 값 받아오기

- 계수기 내 IR 센서의 값을 받아오는 방법을 정리한 문서입니다.

## 1. 센서 번호 및 위치

- 위치[^3번과7번]

  |위치|Master|Slave|
  |--|--|--|
  |위|0|4|
  |중간|1|5|
  |아래|2|6|

- 센서 값 레지스터

  |레지스터|설명|읽기/쓰기|
  |--|--|--|
  |(0) `raw`|ADC에서 입력받은 센서 값|읽기|
  |(1) `BlackMin`|Calibration의 BlackMin값, 유효한 센서의 최솟값|읽기/쓰기|
  |(2) `WhiteMin`|Calibration의 WhiteMin값, 유효한 센서의 최댓값|읽기/쓰기|
  |(3) `value`|Normalized된 센서 값|읽기|

  - (3)번 레지스터는 (1)번과 (2)번의 데이터를 바탕으로 값을 0~255로 매핑해서 읽은 값을 저장합니다. 당연히 Calibraion값 범위를 넘어서면 끝값이 저장됩니다.
  - (3)번 레지스터를 사용하기 전에 Calibraition 과정을 거쳐야 합니다.
  - 따라서 이번 시간에는 Sensor값 중에서 Raw값만 다루고, 나머지 값은 계수기 코딩 파트에서 자세히 다루도록 하겠습니다.
 
## 2. 센서 값 함수

- 값을 읽어오는 함수

  |함수명|매개변수|설명|출력|
  |--|--|--|--|
  |`sensor[n].GetRawValue()`|`n` 센서 번호(0~7)|센서의 ADC 값을 받아옵니다.|센서의 ADC 값(0~4095)|
  |`sensor[n].GetBlackMin()`|`n` 센서 번호(0~7)|칼리브레이션의 BlackMin 레지스터 값을 받습니다.|BlackMin값(0~4095)|
  |`sensor[n].GetWhiteMin()`|`n` 센서 번호(0~7)|칼리브레이션의 WhiteMin 레지스터 값을 받습니다.|WhiteMin값(0~4095)|
  |`sensor[n].GetValue()`|`n` 센서 번호(0~7)|노멀라이즈된 센서 값을 받습니다.|유효 센서값(0~255)|

- 값을 쓰는 함수
  
  |함수명|매개변수|설명|출력|
  |--|--|--|--|
  |`sensor[n].SetBlackMin(value)`|`n` 센서 번호(0~7)<br>`value` BlackMin값|BlackMin값을 레지스터에 저장합니다.|void|
  |`sensor[n].SetWhiteMin(value)`|`n` 센서 번호(0~7)<br>`value` WhiteMin값|WhiteMin값을 레지스터에 저장합니다.|void|

- 센서 초기화 및 상태 함수

  |함수명|매개변수|설명|출력|
  |--|--|--|--|
  |`SENSOR_Init()`|void|센서 8개를 모두 초기화합니다.|void|
  |`SENSOR_Begin()`|void|센서 ADC를 활성화합니다.|void|
  |`SENSOR_End()`|void|센서 ADC를 비활성화합니다.|void|
  |`SENSOR_IR(state)`|`state` IR LED 상태|IR LED를 켜거나 끕니다.|void|
  |`SENSOR_Threshold(high, low)`|`high` L->H로 State가 변할 때의 스레시홀드<br>`low` H->L로 변할 때의 스레시홀드|State값의 기준치를 저장합니다.|void|
  |`SENSOR_GetState()`|void|센서의 상태 값을 비트 단위로 받아옵니다(물체 감지 시 1, 미감지 시 0).|상태 값(LSB부터 0번~MSB가 7번 센서)|
  

## 3. (예제) 센서 읽기

- 버튼을 눌러 센서를 선택합니다(0~7).
- 센서의 값을 0~9로 출력합니다.
- 센서 값은 0~4095로 출력되므로 450으로 나눈 값을 출력합시다.
  - 예: 2번 센서 값이 5일때: 25 출력

- 소스 코드

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $import(..\..\counter_HW\sensor.shc);///센서 라이브러리 추가해야 함.
  $target(counter.sbc);
  
  main(){
      matrix = matrix_t(MATR_3);
      
      ///센서를 초기화합니다.
      SENSOR_Init();
      SENSOR_Begin();
      SENSOR_IR(1);
      
      ///값을 받아올 센서 값
      sensorid = 0;
      
      while(1){
          ///스위치 누를 때마다 센서 다르게 선택.
          sw = SWITCH_Read();
          if(sw == SWITCH_1){
              sensorid += 1;
              sensorid %%= 8;
          }
          elif(sw == SWITCH_2){
              sensorid -= 1;
              sensorid %%= 8;
          }
          elif(sw == SWITCH_BOTH){
              break;
          }
          data = sensor[sensorid].GetRawValue() / 450;
          matrix.Print("%d" % sensorid + "%d" % data) with (RED);
      }
  }
  ```

- 실행 결과

  - 센서를 작동시키면 아래 그림처럼 IR센서부분이 켜진 것을 확인할 수 있습니다.
 
    <image src="..\Res\Examples1\007_Sensor0.jpg" width="25%">
    <image src="..\Res\Examples1\007_Sensor1.jpg" width="25%">

  - 버튼을 누르면 앞자리 숫자가 바뀌고, 그 숫자의 센서 값이 뒷자리 값으로 출력됩니다.
 
    <image src="..\Res\Examples1\007_Sensor2.jpg" width="25%">

[^3번과7번]: 사용하지 않는 센서
