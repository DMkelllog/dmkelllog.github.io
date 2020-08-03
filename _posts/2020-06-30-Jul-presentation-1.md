---
title:  "7월 발표 1. 레이블링 조금 잘못돼도 괜찮아요"
excerpt: "Clova가 레이블 노이즈 잡는 법"

categories:
  - Presentations
tags:
  - Weekly
  - Naver
  - Presentations
---

###  [Naver D2] 레이블링 조금 잘못돼도 괜찮아요

####  Clova가 레이블 노이즈 잡는 법  

데이터에는 노이즈가 있는 레이블이 존재한다.  
이는 모델의 Feature Extraction을 어렵게 한다.

##### 접근법

1. 모델 구조  
   복잡한 패턴을 인식할 수 있는 모델을 만든다.  
   but 너무 무거움
2. 훈련 방법  
   커리큘럼 학습(mentorNet)  
   but 훈련 계산량 증가 + 추가데이터 필요
3. 데이터 정제  
   수동/Active learning

사람의 도움없이 레이블 데이터 제거

##### How?

* Split - Train - Check 알고리즘
  1. 전체 데이터셋을 Train-Valid로 나눈다.
  2. Train으로 Checker를 학습
  3. Checker로 Valid의 mislabeled를 Correction
* **Multi Split** - Train - Check - **Vote**
  * multiple branch
  * Voting 전략

![naver1.PNG](https://github.com/DMkelllog/dmkelllog.github.io/blob/master/assets/images/naver1.PNG?raw=true)

* Checker의 확률 결과를 활용하기 위해  
  PICO (Probabilistic Iterative Correction) 핵심
  * Checker 결과 베이지안 확률 결합
  * Iterative probailistic correction
  * 레이블링 히스토리를 HMM로 반영

![naver2.PNG](https://github.com/DMkelllog/dmkelllog.github.io/blob/master/assets/images/naver2.PNG?raw=true)

1. Checker 결과를 확률적 Vote
2. 레이블링 사전 히스토리 업데이트
3. 베이즈 규칙으로 사후확률 계산
4. 레이블 업데이트
5. 다음 iteration을 위한 확률값 전달
6. 다음 Iteration

- 반복적 확률적 Vote을 통해 점진적으로 레이블 노이즈를 제거한다.

##### 결과

데이터셋 - Line Japan FAQ/Chat  
모델 - BERT Japan  
baseline 대비 3%, rule-based 대비 2% gain

이슈
-메모리, GPU, Inference API 서버 부하 이슈

##### Take Home Message

* 데이터 품질 전략이 없는 AI 프로젝트는 성공하기 어려움
* AI 데이터 자동정제 파이프라인은 매우 큰 경쟁력
* PICO를 통해 데이터 자동 정제하여 서비스 품질 개선
* 이미지 등 다른 종류 데이터셋에도 적용 가능


