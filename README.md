

#### 목차

1. 프로젝트 소개
2. 기존 앱들의 문제점
3. 해결방법
4. System Architecture
5. 구현 기능 Demo
6. 개발 과정 중 겪었던 문제 & 해결방법



### 1. 프로젝트 소개

현재 존재하는 식단 관리 앱들의 핵심적인 기능과 목적은 사용자가 평소에 먹는 음식들을 기록하여 권장량만큼의 영양소를 섭취할 수 있도록 하는 것이다. 본 프로젝트는 이런 앱들이 공통적으로 갖고 있는 문제점을 해결하고, 차별화된 기능을 추가하는 것이다.

- 회원가입 시 사용자의 목표, 성별, 체중, 체지방량 등을 고려하여 하루동안 섭취해야 하는 영양소 양 추천
- 사용자가 먹은 제품의 영양성분표를 촬영하면 OCR로 인식하여 섭취한 영양소 양 기록
- 카카오톡 OAuth로 회원가입 및 로그인



### 2. 기존 앱들의 문제점

- 기존에 존재하는 앱들은 제품을 직접 검색하거나, 바코드를 찍어서 제품을 인식한 후 DB에 있는 제품 정보를 바탕으로 영양소 섭취량을 기록
- 인기가 없거나 새로 출시된 제품의 경우 DB에 존재하지 않는 경우가 있음
- 사용자가 직접 정보를 입력하면 DB에 제품이 추가되는 앱들이 존재하는데, 잘못된 정보가 기록되는 경우가 많음



### 3. 해결방법

영양성분표에 표기된 정보를 OCR로 인식하여 식단을 기록한다면, DB에 존재하지 않거나 잘못된 데이터가 저장되어 있는 문제점을 해결할 수 있을 것이라 판단하였다.



### 4. System Architecture

<img width="231" alt="그림1" src="https://github.com/jonghyun813/nutrient-intake-tracking-application/assets/66056087/6a8bbb9c-7d5f-46fa-a6ee-2b2744906711">


**Frontend Framework – Flutter**

한 학기 내에 Android, iOS에서 모두 사용할 수 있는 앱 제작이 목표이다 보니 cross-platform framework를 사용하기로 하였다. 가장 많이 사용하는 Flutter와 React-Native 중에서 최종적으로 Flutter를 사용하게 된 이유는, 사용자들에게 식단 관련 정보를 보여주는 데 최적화된 우리만의 디자인을 사용하기 위함이다. 우리가 사용하고자 하는 독특한 디자인 구현을 위해서는, 화면에 나타나는 거의 모든 UI를 native os가 아닌 Dart가 렌더링하는 Flutter를 사용하는 것이 React-Native 보다 유리하다고 생각했다.



**Backend Web Framework – Django**

OCR을 활용하여 영양 성분 정보를 정확히 추출해내기 위해서는 사진에 대한 전처리가 필요하다. 사진 전처리에는 파이썬 라이브러리를 활용할 계획인데, Node js와 같이 다른 언어로 된 framework를 사용하면 외부 script를 실행하는 데 overhead가 발생할 것으로 예상되었다. 따라서 파이썬 기반의 framework인 Django를 채택하였다.



**Database – PostgreSQL**

Django에서 공식적으로 지원하는 데이터베이스는 모두 RDBMS 계열이다. MongoDB와 같은 NoSQL도 별도의 연동 과정을 통해 사용할 수는 있지만, 공식적으로 지원하고 있지는 않아서 안정성이 떨어진다고 알려져 있다. 따라서 꼭 NoSQL 과 같이 다른 구조를 가진 데이터베이스를 사용해야 하는 것이 아니라면, 공식적으로 지원하고 있는 데이터베이스를 사용하는 것이 권장된다. 추가적으로 이번 프로젝트에서 저장해야 하는 데이터는 뒤에서 더 자세히 다루겠지만, 테이블 구조를 사용하는 것이 적합하다고 판단했기에 PostgreSQL을 사용하였다.



**Image Preprocessing – Python**

앞서 언급했듯이 OCR을 활용하여 영양 성분 정보를 정확히 추출해내기 위해서는 사진에 대한 전처리가 필요하다. 파이썬에는 이미지 처리와 관련하여 많은 라이브러리들이 존재하고, OpenCV와 같은 라이브러리는 C/C++ 기반으로 동작하기 때문에 매우 빠르다는 장점이 있다. 따라서 이러한 장점을 이용하기 위해 이미지 전처리 과정에서는 파이썬을 사용하기로 하였다.



**OCR – Google Cloud Vision API**

이미지 전처리를 제대로 진행해도, OCR이 정확하게 되지 않으면 소용이 없다. 따라서 OCR API를 선택하는 데 있어서 가장 중요하게 여겼던 것은 OCR 정확도였다. 한글에 대해 안정적인 정확도를 내는 API에는 Google Cloud Vision, Naver Clova 크게 두 가지가 있다. 이 둘의 정확도 차이는 크지 않지만, Google Cloud Vision API가 저렴하면서도 응답 속도가 빠르다고 알려져 있기에 Google Cloud Vision을 사용하게 되었다.



### 5. 구현 기능 Demo

**Kakao 계정으로 회원가입 & 로그인**

- Kakao 계정으로 회원가입 및 로그인을 진행할 수 있다.
- 회원가입 시 사용자의 정보(나이, 성별, 키, 몸무게, 식단 목표, 평소 활동량, 근육량, 체지방률)을 바탕으로 사용자에게 알맞은 영양 섭취량을 계산한다.

<p align="center">
  <img src="https://github.com/jonghyun813/nutrient-intake-tracking-application/assets/66056087/e2d855d3-5a22-4028-9109-ba5ec35f362e" style="zoom: 50%;" alt="demo_ocr">
</p>



**OCR를 활용한 식단 기록**

- 사용자가 먹은 제품의 영양성분표를 촬영하면 OCR로 제품의 영양 정보를 인식한다.
- 사용자로부터 섭취한 제품의 수량을 입력받는다.
- 위의 정보로부터 사용자가 섭취한 영양소를 계산하여 기록한다.
- 만약 잘못된 영양소 정보가 존재한다면, 사용자가 수정할 수 있다.

<p align="center">
  <img src="https://github.com/jonghyun813/nutrient-intake-tracking-application/assets/66056087/71a623be-b307-4da7-9f21-f7e8347c0466" style="zoom: 50%;" alt="demo_ocr">
</p>





**기록된 식단을 바탕으로 사용자가 얼마나 더, 또는 덜 먹어야 하는 지 표시**

- 사용자가 회원가입 시 입력했던 정보를 바탕으로 계산한 목표 식단과 오늘 하루동안 섭취한 식단을 비교하여 보여준다.
- 권장 섭취량을 초과하여 섭취한 영양소가 있다면 빨간색으로 표시된다.
- 권장 섭취량만큼 먹지 못한 영양소가 있다면 얼마나 더 먹어야 하는 지 표시한다.

<p align="center">
  <img src="https://github.com/jonghyun813/nutrient-intake-tracking-application/assets/66056087/ecac6c86-30a3-49b7-8fd0-85292e640b02" style="zoom: 50%;" alt="demo_ocr">
</p>



### 6. 개발 과정 중 겪었던 문제 & 해결방법
