# LED Matrix에 원하는 위치에 원하는 점 출력하기

- LED Matrix에 원하는 위치에 원하는 점을 출력하는 방법과 예제를 정리하였습니다.

## 1. 픽셀 단위 제어 함수

- 계수기의 LED Matrix 작동방식은 SSD1331 OLED의 영역 제어와 비슷하게 동작한다고 생각하면 됩니다.
  - `(matrix).Clear()` : LED Matrix의 전영역을 초기화합니다.
  - `(matrix).AddrWindow(시작점x, 시작점y, 너비, 높이)` : LED Matrix의 점을 출력할 영역을 저장합니다.
  - `(matrix).SetDot(색상)` : AddrWindow 영역에서 순차적으로 픽셀의 색상을 저장합니다.
  - `(matrix).Print()` : AddrWindow 영역에서 저장된 데이터를 출력합니다.

### 1.1. (예제) 특정 위치에 점 출력하기

- 소스 코드
  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  
  main(){
      ///LED Matrix 객체를 가져온다.
      matrix = matrix_t(MATR_3);
      
      ///LED Matrix 초기화
      matrix.Clear();
      
      ///LED Matrix의 (3, 3) 위치만 지정한다.
      matrix.AddrWindow(3, 3, 1, 1);
      
      ///LED Matrix의 색상을 저장.
      matrix.SetDot(WHITE);
      
      ///LED Matrix 출력
      matrix.Print();
      
  }
  ```

### 1.2. (예제) 임의의 위치에 임의의 색 출력하기

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  
  main(){
      ///LED Matrix 객체를 가져온다.
      matrix = matrix_t(MATR_3);
      
      ///LED Matrix 초기화
      matrix.Clear();
      
      while(!SWITCH_Read()){
          x = Random() % 8;
          y = Random() % 8;
          r = Random() % 256;
          g = Random() % 256;
          b = Random() % 256;
          matrix.AddrWindow(x, y, 1, 1);
          matrix.SetDot((r << 16) | (g << 8) | b);
          Delay(10);
          matrix.Print();
      }
  }
  ```

## 2. 픽셀 제어 순서

- AddrWindow 함수를 실행한 뒤 픽셀이 채워지는 순서를 알아볼 시간입니다.
- 픽샐이 채워지는 순서를 파악하기 위해 아래와 같이 소스 코드를 구성합니다.

  ```
  $import(..\..\counter_HW\studio.shc);
  $import(..\..\counter_HW\matrix.shc);
  $target(counter.sbc);
  
  main(){
      matrix = matrix_t(MATR_3);
      matrix.Clear();
      
      ///영역 설정. 이번에는 전영역으로 설정.
      matrix.AddrWindow(0, 0, 8, 8);
      
      for(i)from(1)to(64){
          matrix.SetDot(MAGENTA);
          matrix.Print();
          Delay(500);
      }
      
  }
  ```

- 실행 결과 왼쪽 아래 지점에서 실행해서 오른쪽으로 채우고, 위쪽으로 채워 나가는 것을 알 수 있습니다.

## 3. (예제) LED Matrix에 반복 연산을 사용해 출력하기

```
$import(..\..\counter_HW\studio.shc);
$import(..\..\counter_HW\matrix.shc);
$target(counter.sbc);
$import(stuimg);///이미지 객체를 빌립니다.

main(){
    matrix = matrix_t(MATR_3);
    matrix.Clear();
    
    #im():img_t;
    im.data[0:2, 0:7, 0:7] = 0;
    im.data[1, y:0:7, x:0:7] = x * 32;
    im.data[2, y:0:7, x:0:7] = y * 32;
    
    ///영역 설정. 이번에는 전영역으로 설정.
    matrix.AddrWindow(0, 0, 8, 8);
    
    for(y)from(0)to(7){
        for(x)from(0)to(7){
            matrix.SetDot((int(im.data[2, y, x]) << 16) | (int(im.data[1, y, x]) << 8) | im.data[0, y, x]);
        }
    }
    
    matrix.Print();
    
}
```

### 3.1. 계수기 추가 예제

- [이곳](https://github.com/PJungKim/StarCounter2023/blob/main/Docs/103_Array.md)에서 제시된 코드를 적용하여 LED Matrix를 여러 형태로 출력해 볼 수 있습니다.

- 원모양
  ```
  img.data[0:2, y:0:7, x:0:7] = int(127/((x - 3.5) * (x - 3.5) + (y - 3.5) * (y - 3.5)));
  ```

- 체크무늬
  ```
  img.data[0, y:0:7, x:0:7] = 255 * ((x + y) % 2);
  img.data[2, y:0:7, x:0:7] = 255 * ((x + y + 1) % 2);
  ```

