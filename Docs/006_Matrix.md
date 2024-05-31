# LED Matrix 추가기능

- 계수기와 연동되는 LED Matrix의 추가 기능을 서술한 문서입니다.

# 1. 요약

- 아래 함수는 LED Matrix 객체에 대한 Method입니다. 일부 기능은 앞에서 설명했습니다.

  |함수명|매개변수|기능|출력|
  |--|--|--|--|
  |`Arrow(direction)`|`direction` 방향(int)|LED Matrix에 화살표 표시|void|
  |`SetColor(color)`|`color` 색상(int)|LED Matrix의 화살표 색상 설정|void|
  |`Print(text)`|`text` 문자열(ref/str)|LED Matrix에 스크롤 형태로 출력|void|
  |`SetFPS(fps)`|`fps` 초당 프레임 수(int)|LED Matrix에 스크롤할 때의 속도 설정[^속도]|void|
  |`Clear()`||LED Matrix 초기화|void|
  |`Print(text)with(color)`|`text` 2글자 이하 문자열(ref/str)<br>`color` 출력 색상(int)|정적인 상태로 출력(최대 2자)[^작은]|void|
  |`AddrWindow(x, y, w, h)`|`x` `y` 시작점(int)<br>`w` `h` 크기(int)|LED Matrix 출력 영역 설정|void|
  |`SetDot(color)`|`color` 출력 색상|LED Matrix에 출력 영역에 누적해서 픽셀 설정|void|
  |`FillColor(color)`|`color` 출력 색상|LED Matrix에 선택한 전영역에 같은 색으로 설정|void|
  |`Print()`||LED Matrix에 AddrWindow~SetDot으로 픽셀 설정한 것을 최종적으로 출력|void|

# 2. (예제) 특정 영역 채우기

- `(matrix).FillColor(색상)` 함수를 사용하면 `(matrix).AddrWindow` 함수로 설정한 영역 전체를 특정 색으로 채울 수 있습니다. 이를 이용해 전 영역에 빨간색으로 출력하는 예제는 아래와 같습니다.

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  $import(stuimg);///이미지 객체를 빌립니다.
  
  main(){
      matrix = matrix_t(MATR_3);
      matrix.Clear();
      
      
      ///영역 설정. 이번에는 전영역으로 설정.
      matrix.AddrWindow(0, 0, 8, 8);
      
      ///전 영역을 빨간색으로 채웁니다.
      matrix.FillColor(RED);
      
      ///LED Matrix에 출력합니다.
      matrix.Print();
      
  }
  ```

# 3. 글자 천천히 출력하기

- 아래 예제는 글자를 출력하는 속도를 조절하는 예제입니다. 버튼을 눌러 속도를 조절할 수 있으며, 속도는 10~30으로 설정 가능합니다.

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  $import(stuimg);///이미지 객체를 빌립니다.
  
  main(){
      matrix = matrix_t(MATR_3);
      
      ///기본 fps값은 15입니다.
      fps = 15;
      matrix.Print("/wHello, World!");
      
      while(1){
          ///스위치를 입력받습니다.
          sw = SWITCH_Read();
          if(sw == SWITCH_1){
              if(fps < 30){
                  fps += 1;
                  matrix.SetFPS(fps);
                  matrix.Print("/wHello, World!");
              }
          }
          elif(sw == SWITCH_2){
              if(fps > 10){
                  fps -= 1;
                  matrix.SetFPS(fps);
                  matrix.Print("/wHello, World!");
              }
          }
          elif(sw == SWITCH_BOTH){
              ///프로그램 종료
              break;
          }
      }
  }
  ```

- 실행 결과
  - `BUTTON_1`을 눌렀을 때 LED Matrix 속도가 빨라집니다.
  - `BUTTON_2`를 눌렀을 때 LED Matrix 속도가 느려집니다.

# 4. 화살표 색상 바꾸기

- 화살표가 켜지기 전에 `SetColor(color)` 함수를 사용하면 색상이 결정되고, 화살표가 출력되는 순간 그 색이 출력됩니다.
- 화살표가 켜진 상태에서 `SetColor(color)` 함수를 사용하면 부드럽게 색상이 바뀝니다.
- 색상이 바뀌는 중에 화살표 색을 또 바꾸면 현재 상태에서 부드럽게 색이 바뀌면 좋겠지만, 바뀐 색에서 출발해서 부드럽게 바뀝니다. ~~ATmega의 한계가 적나라하게 보이네요...~~

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  $import(stuimg);///이미지 객체를 빌립니다.
  
  main(){
      matrix = matrix_t(MATR_3);
      
      
      col = 1;
      #cols(int(RED, YELLOW, GREEN, CYAN, BLUE, MAGENTA, WHITE)):int[7];
      matrix.SetColor(cols[col]);
      matrix.Arrow(ARROW_LEFT);
      
      while(1){
          ///스위치를 입력받습니다.
          sw = SWITCH_Read();
          if(sw == SWITCH_1){
              col += 1;
              col %%= 7;
              matrix.SetColor(cols[col]);
          }
          elif(sw == SWITCH_2){
              col -= 1;
              col %%= 7;
              matrix.SetColor(cols[col]);
          }
          elif(sw == SWITCH_BOTH){
              ///프로그램 종료
              break;
          }
      }
  }
  ```
  
  - ~~C언어에는 없는 %%= 연산자가 눈에 띈다.~~

# 5. Starlit Fog

- 이스터 에그로 LED Matrix에 Starlit Fog 패턴이 숨겨져 있습니다.
- Starlit과는 다르게 단색으로, 매우 느린(...) 속도로만 지원 가능합니다. ~~ATmega가 느린걸 어떡합니까??~~
- `(matrix).Arrow(0x08)`을 입력하면 됩니다.



[^속도]: 속도를 설정한 후에 Print를 하면 반영되며, LED Matrix를 실행하는 도중에 속도를 변경하면 반영되지 않으니 주의 바랍니다.
[^작은]: 작은 글자로 최대 2글자까지 출력하며, 2글자가 넘어가면 깨지며, Starlit과는 다르게 글자색을 따로 설정합니다. 또한, 영어의 경우 대문자만 출력합니다.



