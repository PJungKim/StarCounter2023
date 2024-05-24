# Hello, World! 전광판에 출력하기

계수기 LED Matrix에 Hello, World!를 간단히 출력해 보겠습니다. Starlit 계수기는 Starlit Virtual Machine에 의해 실행된 후 자동으로 다운로드 모드로 진입합니다. 따라서 계수기 전광판 중에서 가운데 쪽에 있는 `MATR_3` Matrix를 사용하겠습니다.

## 1. 소스 코드
```
///먼저, 가장 중요한 studio.shc(계수기용 스튜디오)와
///LED Matrix 라이브러리를 임포트합니다.

$import(..\..\counter_HW\studio.shc);
$import(..\..\counter_HW\matrix.shc);
$target(counter.sbc);

///계수기를 사용하기 전, LED Matrix 객체를 선언합니다.
#matrix():matrix_t;




///메인 함수
main(){
    ///계수기의 LED Matrix 초기화(3번 위치)
    matrix = matrix_t(MATR_3);
    matrix.Print("/wHello, World!");
}
```

## 2. 코드 해석

- `matrix_t` : LED Matrix는 matrix_t 자료형의 객체를 사용합니다.
- `matrix_t(매트릭스 번호)` : LED Matrix 객체를 초기화합니다.
  - matrix_t 객체를 전역변수로 사용하는 이유
  - 여러 함수에서 matrix 객체를 동시에 사용하는 특성상 전역변수로 쓰는 것이 편하기 때문이다.

- `(Matrix).Print(문자열)` : LED Matrix에 스크롤하면서 문자열을 출력합니다.
  - `matrix.Print("/wHello, World!");`를 통해 Hello, World!를 흰색 글자로 전광판에 출력할 수 있습니다.

## 3. 업로드하기

- 계수기의 USB를 PC와 연결하세요. UART 통신하는 그 USB를 사용하면 됩니다.

- 오른쪽 LED Matrix에 자홍색 아랫방향 화살표가 표시되는지 확인하세요.
  - 표시되지 않으면 전원을 완전히 끄고(USB까지 뽑은 상태) 버튼 2개를 모두 누른 상태에서 전원을 다시 켜면 됩니다.

- PC의 시리얼 포트가 어디와 연결되었는지 확인한 다음 설정에 들어가서 COM 포트 번호를 바꿉니다.

- 2번을 눌러 컴파일한 후 업로드하면 됩니다.
