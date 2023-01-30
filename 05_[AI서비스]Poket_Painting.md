# 👩‍💻 팀 프로젝트(채색모델 담당)

ㅤ

ㅤ

# 1. 서비스 소개

직접 그린 포켓몬 손그림 이미지를 입력하면 → 어떤 포켓몬인지 분류해주고 → 자동으로 채색해주는 서비스

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-14-38-50-image.png)

ㅤ

ㅤ

# 2. 데이터 및 모델 구성

### 시행착오 - 학습데이터/입력데이터 불일치로 인한 오류

- 포켓몬 컬러이미지로 학습하였더니 손그림을 제대로 분류하지 못함
  학습이미지를 외곽선만 있는 선화로 변경
  ![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-14-44-10-image.png)
  
  ㅤ

- 포켓몬 흑백이미지로 학습하였더니 손그림을 제대로 채색하지 못함
  (손그림은 흑백이미지와 다르게 음영이 없고 외곽선만 있기 때문)
  손그림에 흑백 음영을 추가하기 위해 GAN 모델 추가
  ![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-14-44-19-image.png)
  
  ㅤ

### 최종 모델 구성

- 총 3가지 모델로 구성
  
  1. 손그림 이미지 분류 모델
  
  2. 손그림 이미지에 흑백 음영을 넣는 GAN 모델
  
  3. 흑백 음영에 색을 입히는 채색 모델
  
  ![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-14-46-20-image.png)
  
  ㅤ
  
  ㅤ

# 3. 분류모델

### 학습데이터

X: Canny 에지 검출기로 컬러이미지의 외곽선만 딴 이미지로 학습
Y: 총 8가지 포켓몬(이상해씨, 잠만보, 이브이, 파이리, 꼬부기, 피카츄, 지우, 이슬이)

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-14-56-17-image.png)

ㅤ

컬러이미지, 흑백이미지, 외곽선이미지, 노이즈 제거한 컬러이미지 중 외곽선이미지의 정확도가 가장 높음

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-20-15-image.png)

ㅤ

### Optimizer 선택

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-20-31-image.png)

ㅤ

### 최종 모델 구성

외곽선이미지 + SGD Optimizer

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-20-48-image.png)

ㅤ

### 분류 결과

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-20-57-image.png)

ㅤ

ㅤ

# 4. GAN 모델

### 학습데이터

X: Canny 에지 검출기로 컬러이미지의 외곽선만 딴 이미지로 학습
Y: 흑백이미지로 학습
![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-21-40-image.png)

ㅤ

### GAN(Generative Adversarial Networks) 소개

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-21-51-image.png)

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-21-57-image.png)

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-22-02-image.png)

ㅤ

### GAN 모델 실행

epochs가 커질수록 흑백 음영을 훨씬 더 잘 생성함

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-22-27-image.png)

ㅤ

### 이미지 생성 결과

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-22-42-image.png)

ㅤ

ㅤ

# 5. 채색 모델

### 학습데이터

X: 컬러이미지의 밝기 정보(L)
Y: 컬러이미지의 색상 정보(AB)

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-22-54-image.png)

ㅤ

### 이미지 전처리

RGB 이미지를 밝기(L), 컬러(AB) 값을 가지는 LAB 형식으로 변환

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-23-03-image.png)

ㅤ

### 채색 알고리즘

예측데이터 AB에 입력데이터 L을 결합하여 LAB 이미지를 만들고 이를 RGB로 변환하여 채색이미지 출력

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-23-21-image.png)

ㅤ

### 포켓몬별 최적 모델 & 파라미터

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-23-30-image.png)

ㅤ

### 채색 결과

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-23-50-image.png)

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-23-56-image.png)

ㅤ

ㅤ

# 6. 한계점

![](/Users/sjk/Library/Application%20Support/marktext/images/2023-01-30-15-24-04-image.png)
