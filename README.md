# 유방암의 임파선 전이 예측 AI 

---

## 🔬 프로젝트 소개
* ### 프로젝트 주제
  - 유방암 병리 슬라이드 영상과 임상항목을 통한 유방암의 임파선 전이 여부 예측

* ### 프로젝트 목적 : 
  - 암 진단 시 림프절 전이 여부에 따라 치료와 예후가 결정됩니다. 따라서 유방암이 림프절로 전이될지 여부를 예측하여 유방암 치료에 기여하는 것을 목표로 합니다. 

## 🔬 개발 환경
  - AWS, VSCode,Phycharm,Anaconda
  
---
## 🔬 프로젝트 설명
* ### 개발 과정
<img width="600" alt="스크린샷 2022-12-06 오후 2 49 54" src="https://user-images.githubusercontent.com/105691874/205828484-18e6fb12-1c06-4da2-90dc-08deccede781.png">

 
---
## 🔬 프로젝트 수행 절차 및 
* ### 데이터 분석
    * ####  데이터: Train dataset 1000, Test dataset 250
      <img width="300" alt="스크린샷 2022-12-06 오후 2 55 27" src="https://user-images.githubusercontent.com/105691874/205830106-a9d6eb9b-e5bd-4810-af7f-8f813c04dbb3.png">
    
    * #### Image Data 분석
      <img width="300" alt="스크린샷 2022-12-06 오후 3 00 22" src="https://user-images.githubusercontent.com/105691874/205831855-e1ea26f8-aff9-4fe8-8b3f-7b543f1c8602.png">
        
        * 병리 슬라이드 이미지 파일 (png)
        * 1500 ~ 7300px 크기의 고해상도 이미지
        * 각 데이터마다 종횡비가 상이
        * 노이즈 데이터 존재

    * #### Tabular Data 분석
        <img width="300" alt="스크린샷 2022-12-06 오후 3 02 30" src="https://user-images.githubusercontent.com/105691874/205832630-6847b290-9f71-47bf-b984-fbfcb153aaa4.png">

        * 환자 정보 테이블 파일(csv)
        * 나이, 진단명, 암의 개수 등 학습에 필요한 23가지의 항목 존재
        * 많은 양의 결측치 발견


    
* ### 데이터 전처리
    - 이미지 사이즈 선정:
        - 컴퓨팅 자원이 감당 가능한 최대 사이즈 1024x1024로 이미지 사이즈 고정
    
    - 결측치/ 노이즈 제거:
        - Image data 노이즈 제거:
        ![화면 캡처 2023-02-27 174206](https://user-images.githubusercontent.com/112038669/221515178-4dbe218d-bf59-435c-bbe0-ceb526765ae8.png)
        
        - Tabular data 결측치 제거:
          - 수치형: 해당 열의 중앙값으로 결측치 보완
          - 범주형: 해당 열의 최빈값으로 결측치 보완
          
    - Augumentation:
        - 사용 기법:
            - Rotation
            - Flip
            - Zoom In
            - CLAHE
            - Equalization
        - 선정이유:
            - 이미지 데이터내의 병변 부위를 손상시키지 않는 augumentation 기법을 선정


* ### 모델 선정
    - 두가지 방식의 모델 사용
        - Multi modal 사용:
            - Image data의 feature과 Tabular data의 feature를 concat킨킨 후, classification 실행
        - Tabulr model과 Image model를  각각 사용:
            - Image data와 Tabular data를 각각 classification model에 input시킨 후 나온 결과값들로 ensemble 실행
            
    - 사용 모델:
        - multi modal:
            - Image Feature Extractor(backbone을 통해 image data의 feature를 추출)
                - EfficientNet
                - ResNext
                - DenseNet
                - AlexNet
                - ResNet
            - Tabular Feature Extractor(sequential layer와 TabNet의 encoder를 통해 Tabular data의 feature map을 추출)
                - MLP
                - TabNet 
                
        - single model:
            - Image Classification Model
                - mmClassification
                    - Res2Net
                    - MobileNet_v2
                    - ResNeXt50_32x4d
            - Tabular Classification model
                - Gradient Boosting Classifier
                - Cat Boosting Classifier
                - Light Gradient Boosting Machine
            - Ensemble
                - Hard Voting
        
---
## 🔬 프로젝트 수행 결과

* ### 모델 결과
    - multi modal:      
      |Image Feature Extractor | Tabular Feature Extractor | F1 SCore | 
      |------------------------|---------------------------|----------|
      |      EfficientNet      |           MLP             |  0.7024  |
      |         ResNext        |           MLP             |  0.7994  |
      |         DenseNet       |           MLP             |  0.7320  |
      |         AlexNet        |           MLP             |  0.7442  |
      |         ResNet         |           MLP             |  0.7540  |
      |      EfficientNet      |          TabNet           |  0.7195  |
      |         ResNext        |          TabNet           |  0.7074  |
      |         DenseNet       |          TabNet           |  0.7301  |
      |         AlexNet        |          TabNet           |  0.7121  |
      |         ResNet         |          TabNet           |  0.7164  |         
            
     - single modal:
          - Image Classification Model:
            | Image Classification Model | F1 SCore | 
            |----------------------------|----------|
            |           Res2Net50        |  0.6575  |
            |         MobileNet_v2       |  0.6615  |
            |        ResNeXt50_32x4d     |  0.7067  |
            
          - Tabular Classification Model:
            |  Tabular Classification Model   | F1 SCore | 
            |---------------------------------|----------|
            |  Gradient Boosting Classifier   |  0.7826  |
            |     Cat Boosting Classifier     |  0.8167  |
            | Light Gradient Boosting Machine |  0.8406  |
            
          - Ensemble:
            | Image Classification Model |   Tabular Classification Model  | F1 SCore | 
            |----------------------------|---------------------------------|----------|
            |         Res2Net50          |   Gradient Boosting Classifier  |  0.6781  |
            |        MobileNet_v2        |     Cat Boosting Classifier     |  0.6912  |
            |       ResNeXt50_32x4d      | Light Gradient Boosting Machine |  0.7364  |
            |         Res2Net50          |   Gradient Boosting Classifier  |  0.6944  |
            |        MobileNet_v2        |     Cat Boosting Classifier     |  0.7112  |
            |       ResNeXt50_32x4d      | Light Gradient Boosting Machine |  0.6399  | 
            |         Res2Net50          |   Gradient Boosting Classifier  |  0.7113  |
            |        MobileNet_v2        |     Cat Boosting Classifier     |  0.7372  |
            |       ResNeXt50_32x4d      | Light Gradient Boosting Machine |  0.7444  |
            
     - 최종 결과:
        |         ResNext        |           MLP             |  0.7994  |
            
* ### 성능 한계
  - 용량이 큰 Image data:
    - 고해상도 이미지를 단순 resize 시킴으로써 발생한 많은 pixel loss가 성능 개선을 방해하는 것으로 생각됨
  - 제한된 컴퓨팅 자원:
    - 자원 한계로 인해 제한된 epoch수와 이미지의 크기로 인해 성능 개선에서 어려움을 겪음

* ### 추후 개선 방향
  1. 고해상도 이미지 그대로를 모델의 input data로 활용
    - Multiple Instance Learning(MIL) 사용:
      - 고해상도 이미지를 resize시키지 않고 원본 그대로를 input data로 사용하는 학습 모델
      ![화면 캡처 2023-02-28 205113](https://user-images.githubusercontent.com/112038669/221879765-890552d7-a33c-4cbf-977c-cb9e4bfe1e89.png)
      - 고해상도 이미지를 patch라는 작은 사이즈 이미지로 자른 후, 그렇게 자른 이미지들을 bag라 불리는 묶음으로 묶어서 input data로 사용한다
      - 장점: 고해상도 이미지를 원본 데이터를 보존한 상태에서 모델내에 입력할 수 있기 때문에 resize로 이미지의 사이즈를 줄이는 것보다 성능개선에 도움이 될것이라 판단
  2. Tabular data에 기존 변수를 사용해 새로운 변수를 추가
    - 새로운 변수를 추가해 Tabular data의 복잡도를 높임으로써 성능개선을 도모
    ![화면 캡처 2023-03-02 161847](https://user-images.githubusercontent.com/112038669/222358258-45f3c622-9d36-4892-b87a-3875e02db70c.png)



      
      
