![Untitled](https://user-images.githubusercontent.com/87659486/146333603-416733ea-2936-4eea-a1f8-bcafdf3ad5f1.png)

## 0️⃣ Final Project 수행 목표

- 문제 정의를 통해 AI 를 실제 서비스를 출시해보는 연습을 해본다.
  - 기존 AI 서비스 조사를 통해 트렌드와 서비스 출시의 기저를 파악해본다.
  - 서비스 기획, 데이터셋 조사 및 준비, 모델링, 웹 프로토타이핑, 모델 서빙 등 전체 과정을 직접 구현해본다.
- AI 대회 참여와 실제 서비스 구현 사이의 차이를 몸소 깨달아본다.
  - AI 대회: 추최 측의 데이터 이용가능. 평가 Metric 제공. 참가자들 사이의 공유 문화
  - 실제 서비스: 서비스 요구사항만 있음. 데이터 없음. 평가 Metric 없음.

## 1️⃣ 문제 정의

### 1-1. 시장 조사

- ["23만원짜리 420만원이라도 산다"…2030 꽂히면 아낌없이 지른다](https://www.mk.co.kr/news/business/view/2021/08/792098/) (2021-08-16)
  - 올해 국내 신발 시장 규모는 6조2426억원으로 지난해 대비 2.3% 늘어날 전망이다.
- [올해 패션시장 '가방·스포츠의류·신발'이 성장 주도 - 한국섬유신문](http://www.ktnews.com/news/articleView.html?idxno=120187) (2021-07-16)
  - 올해는 가방, 스포츠의류, 신발 등이 회복세를 주도할 것으로 전망
- [하이힐 줄고, 로퍼 늘고…“팬데믹 이후에도 편한 신발 유행 지속”](http://m.apparelnews.co.kr/news/news_view/?idx=193593) (2021-11-06)
  - 연간 3,900억 달러 규모의 신발 시장은 2020년 17%의 매출 손실을 겪은 후 아직 팬데믹 이전 수준에 도달하지 못하고 있으나 2021년부터 2025년 사이 매년 11% 이상의 성장이 예상된다.
  - 올해 신발 구색에서 스니커즈 점유율은 6% 증가.
  - 아이템 수 증가 및 인기 증가로 수집가들의 선호도도 상승, 비싼 가격에 재판매되고 있다.

### 1-2. 서비스 기획 주제

* OCR 을 이용해 신발 tag 에 있는 품번을 인식하여 웹 상의 정보를 사용자에게 제공

### 1-3. 서비스 기획 세분화
 
* 사용자의 사진 입력
  * 사용자는 서비스에 본인의 사진을 업로드한다.
  * 업로드한 사진에서 품번이 있는 부분을 잘라낸다.
* 정보 제공
  * 품번 인식이 제대로 된 경우, 가격 등의 상품 정보를 제공받는다.
* 데이터 생성
  * 오프라인 유저들의 패턴을 파악을 위한 로그 데이터를 쌓는다.

## 2️⃣ 서비스 흐름도

![순서도](https://user-images.githubusercontent.com/87659486/147045137-8ff307f3-4418-4c8f-b7c4-5612c431571b.JPG)

## :three: 서비스 요구사항

* 정성적 요구사항: 상품번호 타이핑 시간보다 빠르게 OCR 인식
* 정량적 요구사항: 이미지를 잘라낸 시점부터 정보를 제공받기까지 5초 이내

## :four: 서비스 시스템 구조

![시스템 구조도 변경](https://user-images.githubusercontent.com/87659486/147352581-41ba8be2-47f9-4d0f-be37-d1f031b9aac8.jpg)

1. 사용자가 사진을 업로드하고 품번이 있는 부분을 잘라낸다. 
2. 잘린 사진을 OCR 인식기 모델이 있는 백엔드로 전송한다. 
3. 전송 받은 사진에 대해 모델이 예측을 수행하여 품번 label 과 confidence score 를 다시 프론트로 전송한다. 
4. 예측된 품번 label 이 잘못된 경우 label 수정을 하도록 유도한다. 
5. 품번 label 을 Naver API 서버에 전송하여 상품명, 최저가 등의 정보를 다시 프론트로 전송하여 띄워준다. 
6. 품번 label 과 사용자가 잘라낸 이미지를 Google Cloud Storage 에 저장한다. 

## :five: 데이터셋

### 5.1. 수집한 데이터셋

* 참여자 1명당 20~30장을 직접 찍어 구성한 데이터셋 총 153장

  * 촬영장소는 주로 각자의 집 혹은 외부의 신발 매장.

  ![데이터_원본_학습](https://user-images.githubusercontent.com/87659486/147046511-880bd81e-4e47-42ff-8087-dbcec6239738.jpg)

  > 직접 찍은 사진의 원본 (좌)  
  >
  > 원본에서 품번만 잘라낸 이미지 (우)  

### 5.2. 생성한 데이터셋

* 아래의 레포지토리를 참고하여 생성한 데이터셋으로 현 시점의 테스크를 위한 인식기를 학습시키기에 적합하다고 판단.

  * https://github.com/Belval/TextRecognitionDataGenerator
  * 다양한 폰트와 배경, blur 효과, 회전각 적용 등 다양한 데이터를 생성

  ![데이터_생성](https://user-images.githubusercontent.com/87659486/147046954-cabc3bb2-2d53-41ff-8778-e93e0cfa2d6d.jpg)

### 5.3. 학습 데이터셋 버전 관리

* "모델 학습, 모델 추론, 데이터셋 버전 업데이트" 를 하나의 cycle 로 생각하여 cycle 을 돌리면서 데이터셋 버전 업데이트에 따른 성능 변화 관찰
  * v1.0
    * 기본값으로 생성한 이미지 500장으로 모델을 학습
    * 모델 추론을 통해 기울어진 텍스트, 특이한 폰트 등에서 모델이 약한 부분을 관찰
    * 텍스트 각도, 폰트를 고려한 이미지 추가를 데이터셋 버전 업데이트 방향으로 설정
  * v2.0
    * v1.0 에서 0~30도 기울어지고 다양한 폰트를 갖는 이미지를 800장 추가
    * 모델 추론을 통해 90도정도 기울어진 텍스트, 긴 문자열 등에서 모델이 약한 부분을 관찰
    * 더 많이 기울어지고 텍스트의 길이를 늘린 이미지 추가를 데이터셋 버전 업데이트 방향으로 설정
  * v2.1
    * v2.0 에서 더 많이 기울어지고 텍스트 길이가 더 긴 이미지를 500장 추가
    * 모델 추론 결과, 기존 v2.0 에서의 문제 해결이 불가능하고 오히려 정확도를 낮춤
    * 위 이유들로 v2.1 은 기각

## :six: 모델링

### 6.1. 모델 선정

#### ViTSTR

* https://github.com/roatienza/deep-text-recognition-benchmark

* 성능 비교 테이블

  |    Model name    | Pretrained model | Model after Fine-tuning |
  | :--------------: | :--------------: | :---------------------: |
  |   ViTSTR-Tiny    |      24.35%      |         42.31%          |
  | ViTSTR-Tiny+Aug  |      21.80%      |       **63.46%**        |
  |   ViTSTR-Small   |    **28.85%**    |       **63.46%**        |
  | ViTSTR-Small+Aug |    **28.85%**    |         58.33%          |
  |   ViTSTR-Base    |      33.33%      |         58.97%          |
  | ViTSTR-Base+Aug  |      37.82%      |         60.90%          |

  > Accuracy 측정

* 위와 같은 정확도 결과를 얻어으나 아래의 더 좋은 결과를 얻어 ViTSTR 모델은 사용하지 않기로 결정



#### clova-ai deep-text-recognition-benchmark

* https://github.com/clovaai/deep-text-recognition-benchmark

* 성능 비교 테이블

  |               Model name               | Pretrained model | Model after Fine-tuning |
  | :------------------------------------: | :--------------: | :---------------------: |
  |          None-ResNet-None-CTC          |      38.14%      |         63.91%          |
  |           None-VGG-None-CTC            |       8.2%       |         77.32%          |
  |         TPS-ResNet-BiLSTM-Attn         |      43.29%      |         82.47%          |
  |         TPS-ResNet-BiLSTM-CTC          |    **62.88%**    |       **85.56%**        |
  | TPS-ResNet-BiLSTM-Attn-case-sensitive* |      49.35%      |         73.07%          |

  > *: 이 모델은 특수 문자를 인식함.  
  >
  > Accuracy 측정

* 특수 문자를 인식해야하고 영어 대문자를 인식해야하는 task 임을 고려하여 정확도는 조금 낮지만 case-sensitive 모델을 선정

### 6.2. 데이터셋 버전에 따른 성능 변화

* 최종 모델 TPS-ResNet-BiLSTM-Attn-case-sensitive (a.k.a. TRBA-sensitive) 를 학습시켜 정확도를 관찰

* 성능 비교 테이블

  | 데이터셋 버전 |  v1.0  |  v2.0  |  v2.1  |
  | :-----------: | :----: | :----: | :----: |
  |    정확도     | 66.67% | 75.65% | 67.95% |

  > v2.1 의 경우, 정확도도 떨어지고 v2.0 에서의 문제 해결에 도움이 되지 않아 최종 데이터셋 버전은 v2.0 으로 결정

## :seven: 모델 평가

### 7.1. Metric 선정

- 성능 평가 : 정확도(Accuracy)
- 속도 평가 : CPU/GPU 추론 속도

### 7.2. 정량 평가

* 최종 정확도: 78.20%
* 최종 속도: 1초(V100 GPU), 3초(CPU)

### 7.3. 정성 평가

* 일반적인 경우

  ![general cases](https://user-images.githubusercontent.com/87659486/147409826-a2ea9f44-c2c7-49e9-bea8-297f2e475f23.png)


* Edge Case

  ![edgecase](https://user-images.githubusercontent.com/87659486/147354772-91c44eeb-68c8-42a0-a09e-c579edc1b2ac.jpg)
  

## :eight: 프로토타입

* 프론트엔드
  * streamlit 을 이용하여 구현
* 백엔드
  * Fast API 를 이용하여 구현
* 스토리지
  * 구글 클라우드 스토리지를 이용
  * 사진 이미지와 예측 label 을 전송하여 저장
* 구현 데모 페이지
  * with GPU: http://49.50.172.150:6012/ (21.12.24. 이후로는 사용 불가)
  * with CPU: http://34.64.74.230:8501/ (GCP 무료 크레딧이 종료되는 22.03. 이후 사용 불가)
  ![프로토타입1](https://user-images.githubusercontent.com/87659486/147622892-c024b517-b5cc-40f3-a1a8-f364b6ed104c.jpg)
  ![프로토타입2](https://user-images.githubusercontent.com/87659486/147622895-47664a6a-d671-4a10-be03-5d6f3203e22e.jpg)


## :nine: Future Works

1. 너무 느린 추론 속도 (V100 GPU: 1s/img, CPU: 3s/img)
   * Feature Extractor 인 ResNet 를 포함하여 성능의 큰 하락 없이 모델의 경량화 시도를 하여 추론 시간을 단축시키고 한 GPU 에 여러 모델을 올려 분산처리를 시도하면 좋을 것 같음.
2. 모델 정확도
   * 78.20% 의 정확도로 만족스러운 서비스가 될 것인지 의문이 들어 data centric, model centric 관점에서 더 접근하여 정확도를 높이는 시도를 하면 좋을 것 같음.
3. Edge Case 처리
   * 최종 모델의 논문 상으로는 90도 회전된 이미지에 대한 처리가 불가하여 이를 잘 처리하는 모델을 찾아보고 실험해보면 좋을 것 같음.
   * 이 외에도 이미지에 품번이 아닌 다른 텍스트가 들어간 경우, 모델의 추론 난이도가 올라가는 문제도 있어 이 부분을 해결하는 새로운 시도가 있다면 좋을 것 같음.
4. 배치 서빙
   * 모델의 지속적 성능 향상을 위해 구글 클라우드 스토리지에 쌓이는 데이터 중 기존 모델의 예측 레이블을 사용자가 수정한 데이터에 대해 모델을 정기적으로 학습하고 학습 후 성능을 모니터링하는 파이프라인을 구축하는 시도가 있으면 좋을 것 같음.


## :keycap_ten: 시연 영상

![시연 영상](demo.gif)

## 참여자

|  이름  | 캠퍼 ID | Github                                        | 역할                                                         | 비고 |
| :----: | :-----: | --------------------------------------------- | ------------------------------------------------------------ | :--: |
| 김서기 |  T2035  | [*Link*](https://github.com/seogi98)          | **백엔드 (GCP, 네이버 검색API 연결)**, 모델 실험, <br />프론트엔드(Streamlit), 데이터 수집 및 라벨링 |      |
| 김승훈 |  T2042  | [*Link*](https://github.com/lead-me-read-me)  | **데이터**, **모델 실험 (TRBA)**, 베이스라인 구축, <br />백엔드(FastAPI, GCP), 데이터 수집 및 라벨링 |      |
| 배민한 |  T2260  | [*Link*](https://github.com/Minhan-Bae)       | **PM**, 모델 실험, 프론트엔드 (Streamlit), 데이터 수집 및 라벨링 |      |
| 손지아 |  T2113  | [*Link*](https://github.com/oikosohn)         | **EDA**, **모델 실험**, 베이스라인 구축, 프론트엔드 (Streamlit), <br />데이터 관리, 데이터 생성, , 데이터 수집 및 라벨링 |      |
| 이상은 |  T2157  | [*Link*](https://github.com/lisy0123)         | **논문 리서치**, 목업 디자인, 데이터 수집 및 라벨링          | 퇴소 |
| 조익수 |  T2213  | [*Link*](https://github.com/projectcybersyn2) | **모델 분석 및 실험 (TRBA, ViTSTR)**, <br />프론트엔드(Streamlit), 데이터 수집 및 라벨링 |      |
