# 센서 타이밍 이슈

- 센서 인식 시간과 관련한 문제와 이를 해결하기 위한 방안을 정리한 문서입니다.

## 1. Polling 이슈

- 2023년도 계수기의 State Machine의 구조는 Starlit 언어로 작성한 Polling 방식인 특성상 센서에서 Positive 또는 Negative Edge가 발생할 때, 그 지점에서의 시간과 실제 받아오는 시간에서의 오차가 필연적으로 발생합니다.
  - 여기서 Edge가 발생한 시점을 `Edge Time`이라고 부르고, 실제 상태가 변한 시간을 `State Time`이라고 부르겠습니다.
  - `Edge Time`은 물리적인 Edge가 발생한 시간을 의미합니다.
  - `State Time`은 물리적인 Edge가 발생하고, 계수기 소스 코드에서 `driveTime = Tick();`을 실행하는 시점에서의 시간입니다.
  - `State Time`값은 반드시 `Edge Time`값보다 크거나 같을 것입니다. 이때, `Error` = `State Time` - `Edge Time` >= 0입니다.
 
- Polling Issue 해결 방안 : `Edge time` 사용하기
  - `Error`값은 Starlit 언어만으로 추정하기 매우 어려우며, 이 Error로 인해 경기 결과의 공정성 문제가 발생할 여지가 있습니다.
  - `Edge Time`을 C언어 기반의 센서 인터럽트에서 받아오는 방법을 사용한다면, Edge Time 자체는 ms 단위이고, 센서 인터럽트 역시 1ms 주기로 동작하므로 결과의 신뢰성을 향상시킬 수 있습니다.
  - `Edge Time`은 각각 `Rising Edge Time`과 `Falling Edge Time`으로 분류하며, 경기 규정에 따라 출발에서는 Rising Edge가 발생하는 시점이 `경기 시작 시간`이고, 종료에서는 Falling Edge가 발생하는 시점을 `경기 종료 시간`으로 보기 때문에 `경기 시작 시간`은 `Rising Edge Time` 레지스터에서, `경기 종료 시간`은 `Falling Edge Time` 레지스터에서 가져올 수 있습니다.
 
### 1.1. Edge Time을 받아오기 위한 함수

|함수명|매개변수|설명|출력|
|--|--|--|--|
|`sensor[i].GetRisingTime()`|`i` : 센서 번호(0~7)|`Rising Edge Time` 구하기|`Rising Edge Time` (ms 단위 정수)|
|`sensor[i].GetFallingTime()`|`i` : 센서 번호(0~7)|`Falling Edge Time` 구하기|`Falling Edge Time` (ms 단위 정수)|

### 1.2. (예제) 센서 인식 시간 받아오기

## 2. Bouncing 이슈 및 해결방안

- Bouncing Effect는 센서의 Noise로 인해 Edge가 발생하는 시점에 High-Low 신호가 순간적으로 번갈아 바뀔 수 있는 현상을 의미합니다.
- Threshold를 분리시켜 Bouncing 현상을 완화시켰지만, Threshold의 차이가 크면 인식 감도가 둔해질 수 있습니다.
- Bouncing Effect를 해결하기 위해 인식 시간을 받아올 때, Rising Edge는 가장 먼저 일어났을 때의 시간을, Falling Edge는 가장 나중에 일어난 시점의 시간을 받아오는 것이 가장 바람직합니다.
- Bouncing Effect를 고려하지 않으면 Rising Edge가 발생할 때마다 시간이 늦어지므로 가장 먼저 일어난 시점의 시간을 구하기 어렵습니다. 따라서 Rising Edge가 발생하면 Flag를 걸어서 더 이상 센서 인식 시간을 변경하지 못하게 한 다음 읽고 난 뒤에 센서 인식 시간을 바꿀 수 있게 하여 문제를 해결할 수 있습니다.

### 2.1. Bouncing Effect를 해결하기 위한 Flag 함수

|함수명|매개변수|설명|출력|
|--|--|--|--|
|`sensor[i].RisingTimeFlagEnable()`|`i` : 센서 번호(0~7)|Rising이 처음 발생한 시간을 반환하게 함.|void|
|`sensor[i].FallingTimeFlagEnable()`|`i` : 센서 번호(0~7)|Falling이 처음 발생한 시간을 반환하게 함.|void|
|`sensor[i].RisingTimeFlagDisable()`|`i` : 센서 번호(0~7)|Rising이 나중에 발생한 시간을 반환하게 함.|void|
|`sensor[i].FallingTimeFlagDisable()`|`i` : 센서 번호(0~7)|Falling이 나중에 발생한 시간을 반환하게 함.|void|

### 2.2. (예제) 센서 인식 시간 받아오기 코드 수정
