# Non-contrast Brain CT Lesion Segmentation

본 프로젝트는 **비조영 뇌 CT 영상**에서 **출혈 및 허혈 병변 영역**을 자동 분할하는 딥러닝 모델을 개발하고, 이를 통해 임상 지원을 목표로 합니다.

---

## 1. 핵심 목표

- 출혈성 / 허혈성 병변에 특화된 2D segmentation 모델 개발
- 병변 유형별 영상 특성에 맞춘 구조 설계
- 의료 현장 적용을 위한 정량화된 병변 마스크 생성

---

## 2. 데이터

- **AIHub 공개 데이터셋**
  - 비조영 CT (DICOM 형식)
  - GT 포함 병변 마스크 제공
- **분할 대상**
  - **출혈성 병변**: 경계 명확
  - **허혈성 병변**: 경계 불명확, 저음영
 
---

## 3. 모델 구조

| 병변 유형 | 모델 구조 | 입력 | 특징 |
|-----------|-----------|------|------|
| 출혈 | ResUNet50 | 단일 슬라이스 (512×512) | 경계 명확한 병변 분할 |
| 허혈 | Swin-UNet Lite | CLAHE 후 3채널 | Attention 기반 희미한 병변 탐지 |

---

## 4. 전처리

- HU 변환: DICOM의 slope/intercept 활용
- CLAHE / 윈도우링 적용
- Resize: 512×512
- 채널 확장: 1 → 3 (허혈 모델용)
- Train/Val/Test: 70/15/15

---

## 5. 학습 및 평가

- Optimizer: Adam
- Loss:
  - 출혈: Dice + BCE/Focal
  - 허혈: Dice + Focal
- Scheduler: ReduceLROnPlateau
- 지표: Dice, IoU, Sensitivity, Specificity, Precision, F1

---

### 최종 성능 요약 (epoch 기록 평균치)

| 모델 | Dice | IoU | Sensitivity | Specificity | Precision | F1-score |
|------|------|-----|-------------|-------------|-----------|----------|
| 출혈 | 0.6628 | 0.5357 | 0.7169 | 0.9975 | 0.7226 | 0.6704 |
| 허혈 | 0.6478 | 0.5152 | 0.5596 | 0.9988 | 0.8413 | 0.6461 |

> 허혈 모델은 FN 개선 여지 있음. 출혈 모델은 전반적으로 균형된 성능.

