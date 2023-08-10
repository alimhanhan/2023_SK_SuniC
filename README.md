# 🚌 2023 SK SuniC_UniBus Smart Shelter for Visually Impaired 🚌



<br><br><h2>🎯Step_1) IoT 관련 네트워크 & 취약점 스터디</h2>
: **센서값 및 함수 조작 스터디 진행 및 보고서 작성 & 시나리오 기반 익스플로잇 방안 연구**

<details>
<summary>📑참고 자료 및 논문</summary><br>
<div markdown="1">
# 임시파일 데이터 조작을 통한 아두이노 보드 공격 기법에 대한 연구<br>
--> https://www.notion.so/0105-89580f8a02d34a65aa194665a335a3a0?pvs=4#b3b07534d4374f56935bcb47e6a27c31
<br><br>
# KISA IoT 공통보안 가이드<br>
--> https://www.kisa.or.kr/2060205/form?postSeq=2&lang_type=KO&page
<br><br>
# Arduino를 이용한 데이터 통신 암호 모듈<br>
--> https://koreascience.kr/article/CFKO201331751949663.pdf
<br><br>
# 아두이노를 이용한 다중 레벨 인증 시스템<br>
--> https://koreascience.kr/article/CFKO201523070247810.pdf

</div>
</details>

<br><br><br><h2>🎯Step_2) 아두이노 RC카 조립 & 아두이노 주행 환경 구축</h2>
<h3>🎓 RC카 조립 및 기본 IDE 설정</h3>

![스크린샷 2023-04-15 231930](https://user-images.githubusercontent.com/102565567/232230164-5935c275-a2e2-4a3a-b64d-2169b0923fff.png)

<br><p><h3>🎓 Used Tech Stacks</h3>
: **Arduino IDE** & **Kit** <br>
➡️ `Smart car base`, `DC motor`, `Car Wheels`, `Supports`, `Speed encoder`, `1.5V AA battery holder`, `Pin header`, `T-head`, `Ultrasonic sensor`, `USB cable for Uno`, `Toggle switch`, `Uno R3 compatible board`, `Mini breadboard`, `Motor driver shield`<br><br>
: **`C`** & **`C++`**

<br><br><br><h2>🎯Step_3) 정상 소스 코드 분석 및 취약점 탐색</h2>
<h3>🎓 정상 주행 코드</h3>
<details>
<summary>📑코드 분석</summary><br>
<div markdown="1">

자율주행 코드는 울트라소닉 센서를 사용하여 장애물을 감지하고 회피하는 아두이노 기반의 스마트 카를 구현한 것으로, 스마트 카는 전진하며, 장애물이 감지될 때까지 이동한다. 장애물이 감지되면 스마트 카는 임의로 왼쪽 또는 오른쪽으로 회피하고, 물체와의 거리가 150 이하일 때는 장애물을 피하기 위해 800ms간 후진한다.

 본 코드는 AFMotor 라이브러리를 사용하여 스마트 카의 모터를 제어하며, SoftwareSerial 라이브러리를 사용하여 컴퓨터와의 시리얼 통신을 설정하였다. 초음파 센서에 사용되는 핀은 코드의 맨 앞부분에서 A0과 A1로 정의된다. 장애물과의 거리는 pulseIn 함수를 사용하여 측정하며, 왼쪽 및 오른쪽 모터 속도에 대한 값은 각각 Lspeed 및 Rspeed로 정의하였다.

 코드의 메인 루프는 **`Forward()`** 및 **`Obstacle_Check()`** 함수로 구성된다. **`Forward()`** 함수는 왼쪽 및 오른쪽 모터를 지정된 속도로 전진하도록 설정하였다. **`Obstacle_Check()`** 함수는 **`Distance_Measurement()`** 함수를 사용하여 장애물과의 거리를 확인하고, 거리가 300 이하인 경우 while 루프로 들어간다. 만일 물체와의 거리가 150 이하이면 **`Backward()`** 함수를 사용하여 스마트 카가 800ms간 후진하도록 설정하였다. 반면 거리가 150에서 300 사이인 경우, 스마트 카는 **`Left()`** 및 **`Right()`** 함수를 사용하여 임의로 왼쪽 또는 오른쪽으로 회전한다. 이때 동작 간 딜레이 시간은 400ms로 설정되며, 스마트 카는 다시 **`Distance_Measurement()`** 함수를 사용하여 거리를 다시 확인한다.

 코드에는 또한 스마트 카를 후진, 오른쪽 또는 왼쪽으로 회전하도록 하는 함수 및 모터를 정지하는 함수가 포함되어 있는데, 이러한 함수들는 **`Obstacle_Check()`** 함수에서 필요할 때 호출된다.

</div>
</details>
<details>
<summary>📑코드 취약점</summary><br>
<div markdown="1">

이 코드는 Eduino Smart Car를 제어하기 위한 아두이노 코드로, 초음파 센서를 사용하여 차량의 전방에 장애물이 있을 경우 장애물을 회피하도록 구현되었다.

 하지만 이 코드에는 사용자 인증이나 암호화 기능이 없기 때문에, 누구나 이 코드를 업로드하여 차량을 제어할 수 있다. 따라서 악의적인 사용자가 이 코드를 이용하여 차량을 제어하거나 센서 값을 조작할 가능성이 있다.

1. **무작위 방향 변경:** 코드는 장애물을 만날 때 로봇의 방향을 무작위로 변경한다. 이로 인해 예측할 수 없는 동작을 일으키고, 로봇이 다른 장애물과 충돌할 가능성이 존재한다. 따라서 장애물의 위치와 거리에 따라 로봇이 이동할 수 있는 최적의 방향을 결정하기 위해 보다 견고한 알고리즘을 사용할 필요가 있다.
2. **장애물 메모리 없음**: 위의 코드는 이전에 만난 장애물의 위치를 기억하지 않는다. 이로 인해 로봇이 같은 방향으로 계속 회전하고, 주변 환경을 효과적으로 탐색하지 못할 수 있다. 따라서 보다 정교한 접근 방식을 사용할 필요가 있다.
3. **보정 없음:** **`Distance_Measurement()`** 함수에서 거리 계산은 고정된 음속을 가정하는데, 이는 모든 조건에서 정확하지 않을 수 있다. 또한 이 함수는 센서 읽기의 보정이나 유효성 검사를 수행하지 않는데, 이로 인해 거리가 부정확하게 측정되거나 장애물 회피 작업이 원활하게 이루어지지 않을 수 있다.
4. **오류 처리 없음:** 본 코드는 오류 처리나 장애 허용 메커니즘을 포함하지 않아 오류가 발생할 경우 IoT기기의 대처가 불가하다. 예를 들어 초음파 센서가 작동하지 않거나 응답을 멈춘 경우, 차량은 장애물을 감지할 수 없으며 이로 인해 충돌할 가능성이 있다.
5. **제한된 센서 범위:** 위의 코드는 제한된 범위(300cm) 내의 장애물만 검사한다. 이 검사 범위는 모든 환경에 대해 충분하지 않을 수 있으며, 더 멀리 위치한 장애물과 충돌할 위험성이 있다.

</div>
</details>
<details>
<summary>📑주행 영상</summary><br>
<div markdown="1">

**1️⃣ 리모컨 컨트롤러 주행 영상**

https://user-images.githubusercontent.com/102565567/232230903-c32e84b7-da76-492e-88e4-565e7a29460f.mp4

<br><br>**2️⃣ 원격 자율주행 영상**

https://user-images.githubusercontent.com/102565567/232230962-3c4a6050-3151-493a-b011-f4925d790489.mp4
 
</div>
</details>
<br><br><br><h2>🎯Step_4) 악성코드 제작 및 실행</h2>
<h3>🎓 공격용 조작 코드</h3>
<details>
<summary>Code 1️⃣_거리가 0일때 후진하도록 조작한 소스 코드</summary><br>
<div markdown="1">
 
  ⇒ 장애물 인지 기능을 무력화하여 장애물과 충돌하도록 조작
<br><br><br>**☝️ Code 1 삽입 후 주행 영상**

https://user-images.githubusercontent.com/102565567/232231719-f00d703e-c303-4eaf-b914-ada00c8c7dc8.mp4

</div>
</details>
<details>
<summary>Code 2️⃣_`Distance_Measurement()`함수(=장애물 거리 측정 함수)의 반환값을 항상 400이 되도록 조작한 소스 코드</summary><br>
<div markdown="1">

 ⇒ 장애물과의 실제 거리를 반영하지 못하게 함으로써 장애물과 충돌하도록 조작
<br><br><br>**✌️ Code 2 삽입 후 주행 영상**

https://user-images.githubusercontent.com/102565567/232231980-074545f0-b612-4abb-89cc-4ce8848319bb.mp4

</div>
</details>
<br><br><br><h2>🎯Step_5) 취약점 보완 조치 진행</h2>
<h3>🎓 취약점 보완 코드</h3>
<details> 
<summary>Code 1️⃣_장애물을 만날 때 로봇의 방향 번경 전 앞에 장애물이 있는지를 검사하는 과정 추가</summary><br>
<div markdown="1">
 
  —> 장애물이 있는 경우 장애물을 피할 방향을 결정하고, 장애물이 없는 방향으로 변경하여 이동한다. 이를 위해 `Left()`와 `Right()`함수를 이용하여 로봇을 각 방향으로 회전시키고, `Distance_Measurement()`함수를 이용하여 회전한 방향으로부터의 거리를 측정한다. 이후 거리가 더 긴 방향으로 회전하여 이동한다.
<br><br><br>**☝️ Code 1 조치 후 주행 영상**

https://user-images.githubusercontent.com/102565567/232232504-0664049c-0351-40de-87fe-3d0c40242caf.mp4

</div>
</details>
<details>
<summary>Code 2️⃣_보안 코드 1에 이전에 만난 장애물의 위치를 기억하게 하는 코드 추가</summary><br>
<div markdown="1">

 —> `prev_distance`라는 전역 변수를 추가하고, `Obstacle_Check()`함수에서 현재 거리(`distance`)와 이전 거리(`prev_distance`)를 비교하여, 이전에 만난 장애물의 위치를 기억하게 하는 코드를 추가
<br><br><br>**✌️ Code 2 조치 후 주행 영상**

—> 50번의 운행 시도 결과, 장애물 위치를 인지했던 첫 번째 운행을 제외한 나머지 49번 모두 장애물이 없는 좌측으로 방향을 전환함을 확인

https://user-images.githubusercontent.com/102565567/232232597-1aad853c-9fac-413d-a71c-e899fafda603.mp4

</div>
</details>
