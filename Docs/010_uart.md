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
|`FTDI_Scan(n, st)`|`n` 읽어올 데이터 최대 바이트 수<br>`st` 읽어올 문자열(ref형)|FTDI에 전송된 문자열을 읽습니다.|실제로 읽은 바이트 수(0~255)|

