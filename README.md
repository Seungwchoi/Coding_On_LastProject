# 🍅 스마트 팩토리 토마토 상태 분류 시스템

## 📌 프로젝트 소개

- **주제:** 딥러닝 기반의 토마토 품질 분류 및 자동 물류 제어 시스템
- **기획 의도:**  
  산업 현장에서 사람이 수행하던 과일 선별 작업을 자동화함으로써 인건비 절감, 효율성 향상, 품질 관리 체계화를 목표로 함.  
  특히 카메라 기반 실시간 상태 감지를 통해 곰팡이, 상처, 덜 익은 상태를 식별하고, 등급별로 물류 자동 제어 및 HMI와의 통합도 구현.

- **기간:** 2025.05.01 ~ 2025.06.22  
  (개인 프로젝트, 실제 PLC 및 HMI 테스트 포함)

---

## ⚙ 사용 기술 스택

| 분야        | 기술 |
|-------------|------|
| 객체 감지   | YOLOv8 (Ultralytics) |
| 영상 캡처   | OpenCV, mss |
| 모델 학습   | Python, PyTorch |
| PLC 제어    | Modbus TCP (LS산전 XG5000, XGI-CPUE) |
| DB 저장     | MySQL, pymysql |
| 로그 관리   | CSV 자동 저장 (날짜별 분리) |
| UI/HMI 연동 | XP-Builder 3.90 |
| 환경 구성   | Python 3.12, Anaconda (sf8 가상환경) |

---

## 🔧 추가 기술 상세 설명

| 기술/도구 | 설명 |
|-----------|------|
| **YOLOv8 (Ultralytics)** | 실시간 객체 감지 프레임워크로, 토마토 상태(정상, 상처, 곰팡이, 덜익음, 무름)를 고속으로 분류합니다. |
| **OpenCV + MSS** | 화면 캡처 기반 실시간 영상 스트리밍 처리 및 디스플레이. GUI 없이도 YOLO로 프레임 처리 가능. |
| **Modbus TCP/IP** | Python ↔ PLC 간 통신 프로토콜로 사용. pymodbus 라이브러리를 통해 등급 및 로그 메시지를 PLC에 전송합니다. |
| **MySQL** | 감지 결과를 DB에 저장하여 이력 관리 가능. `sql_module.py`에서 자동 저장 기능 구현. |
| **XP-Builder HMI** | LS산전 HMI에서 PLC 데이터를 실시간 표시. MW2000에 ASCII 문자열을 전송하여 감지 로그를 표시합니다. |
| **Matplotlib + Pandas** | 감지된 로그 데이터를 기반으로 일/주/월/분기/연도별 통계 그래프 자동 생성. `visual/plot_generator.py` 사용. |

---

## 🚀 주요 구현 기능 요약

- **실시간 토마토 상태 감지**
  - YouTube 영상 또는 특정 화면을 실시간 캡처하여 YOLO 모델로 등급 분류

- **등급별 자동 분기 제어**
  - 정상(0): 프리미엄 박스로 이동
  - 상처(1): 일반 박스로 이동
  - 곰팡이/덜익음/무름(2~4): 폐기 분기로 이동

- **PLC 제어 기능**
  - 감지 결과를 PLC(MW1000)에 전송
  - 로그 요약 문자열을 ASCII 형식으로 MW2000에 전송

- **로그 기록 기능**
  - CSV 파일(logs/log_YYYY-MM-DD.csv)에 자동 저장
  - MySQL DB에도 동일 내용 저장

- **HMI 연동 기능**
  - ASCII 문자열을 HMI에서 실시간 감지 결과로 표시

- **통계 및 시각화 기능**
  - 일/주/월/분기/연도별 통계 그래프 자동 생성
  - `visual/` 폴더에 시각화된 이미지 저장

---

## 🔍 주요 기술 채택 이유

- **YOLOv8**: 실시간 객체 감지 성능이 뛰어나고, Ultralytics API 덕분에 Python 기반 통합이 쉬움
- **mss**: 특정 화면 영역만 빠르게 캡처하여 모델 입력으로 사용
- **Modbus TCP**: Python과 PLC 간 통신이 간단하며, 산업용에서 광범위하게 사용됨
- **MySQL**: 결과 데이터를 안정적으로 저장, 추후 통계 활용 가능
- **XP-Builder**: LS산전 계열 PLC와 연동 가능한 HMI UI 제작 도구

---

## 🛠️ 내가 구현한 기능

- **실시간 토마토 감지 및 등급 분류**
  - 정상 / 상처 / 곰팡이 / 덜익음 / 무름 등 5단계 분류
- **YOLOv8 학습 및 배포**
  - Roboflow 기반 라벨링 → 학습 → 모델 최적화
- **화면 캡처 기반 실시간 프레임 추출 (`mss`)**

- **등급별 자동 물류 제어 로직**
  - 프리미엄 박스 이동 / 스탠다드 박스 이동 / 불량품 폐기
- **PLC(Modbus TCP) 제어 통합**
  - 감지 결과를 MW 주소로 전송, HMI에 반영
- **CSV 로그 저장**
  - 날짜별 `logs/log_YYYY-MM-DD.csv` 자동 생성 및 결과 이어쓰기
- **DB 연동 (MySQL)**
  - 감지 결과 (시간, 등급, 신뢰도 등)를 데이터베이스에 저장
- **HMI 디스플레이 연동**
  - PLC 메모리(MW2000~)를 통해 실시간 감지 결과 및 통계 문자열 표시

---

## 📂 프로젝트 폴더 구조

```
C:\Users\hufs0\Documents\project_root
project_root/
├── config/
│   ├── __init__.py               // 파이썬 패키지 인식용 (내용 없음)
│   ├── settings.py               // 경로, 모델 위치, DB/PLC 파라미터 등 전체 설정 정의
│   └── __pycache__/
│       ├── __init__.cpython-312.pyc
│       └── settings.cpython-312.pyc
├── database/
│   ├── __init__.py               // 파이썬 패키지 인식용
│   ├── restock.py                // 등급에 따른 물류 이동 함수 정의
│   ├── sql_main.py               // DB 통신 메인 코드 (연결 테스트 등)
│   ├── sql_module.py             // 감지 결과를 MySQL에 기록하는 함수
│   └── __pycache__/
│       ├── restock.py
│       ├── sql_main.py
│       └── sql_module.py
├── datasets/
│   └── tomato_defect/
│       ├── images/
│       │   ├── train/            // 학습 이미지
│       │   ├── val/              // 검증 이미지
│       │   └── test/             // 테스트 이미지
│       ├── labels/
│       │   ├── train/            // 학습 라벨
│       │   ├── val/              // 검증 라벨
│       │   └── test/             // 테스트 라벨
│       └── data.yaml             // 클래스 정의 및 경로 설정
├── detection/
│   ├── detector.py               // YOLOv8 실시간 감지 로직
│   └── __pycache__/
│       └── detector.cpython-312.pyc
├── logs/
│   └── log.csv                   // 감지 결과 로그 파일
├── plc/
│   ├── log_sender.py             // 로그를 HMI로 전송
│   ├── modbus_client.py          // PLC에 등급값 송신
│   ├── modbus1-1.py              // 테스트 - 데이터 전송 및 초기화
│   ├── modbus1-2.py              // 테스트 - 전체 데이터 초기화
│   ├── plc_test2-1.py            // 테스트 - ModRSsim 연결 확인
│   ├── PLC_TEST2-2.py            // 테스트 - XG5000 통신 테스트
│   └── __pycache__/
│       └── modbus_client.cpython-312.pyc
├── runs/
│   └── detect/
│       ├── tomato_yolov8m_fast/
│       │   ├── args.yaml
│       │   ├── labels.jpg
│       │   └── labels_correlogram.jpg
│       ├── tomato_yolov8m_fast2/
│       │   └── args.yaml
│       └── tomato_yolov8m_fast3/
│           ├── weights/
│           │   ├── best.pt       // 최종 최고 성능 모델
│           │   └── last.pt       // 마지막 에폭 모델
│           ├── args.yaml
│           ├── confusion_matrix.png
│           ├── confusion_matrix_normalized.png
│           ├── F1_curve.png
│           ├── P_curve.png
│           ├── PR_curve.png
│           ├── R_curve.png
│           ├── results.csv
│           ├── results.png
│           ├── labels.jpg
│           ├── labels_correlogram.jpg
│           ├── train_batch0.jpg
│           ├── train_batch1.jpg
│           ├── train_batch2.jpg
│           ├── train_batch28560.jpg
│           ├── train_batch28561.jpg
│           ├── train_batch28562.jpg
│           ├── val_batch0_labels.jpg
│           ├── val_batch0_pred.jpg
│           ├── val_batch1_labels.jpg
│           ├── val_batch1_pred.jpg
│           ├── val_batch2_labels.jpg
│           └── val_batch2_pred.jpg
├── main.py                       // 시스템 전체 실행 파일
├── yolov8_tomato_train.py
├── yolov8m.pt                    // YOLOv8 모델 가중치
└── 파일구조.txt
```

## 💬 느낀점 및 회고

이 프로젝트를 통해 단순한 객체 감지에서 그치지 않고, **제어 시스템(Python ↔ PLC)**과 **실시간 현장 시각화(HMI)**까지 통합한 **스마트팩토리 라인**을 직접 설계하고 구현할 수 있었습니다.

특히 다음과 같은 점에서 기술적 이해도 높이는데 도움이 됐습니다 :

- YOLO의 감지 결과를 **PLC 신호로 직접 변환**하여 산업 현장에 적용
- **Modbus TCP/IP 프로토콜**을 활용해 Python ↔ PLC 통신 구조 확실히 이해
- **HMI의 ASCII 문자 디스플레이 방식**을 통해 제한된 워드 자원을 효과적으로 사용하는 법 습득
- CSV + MySQL에 이중 저장된 로그 데이터를 **pandas와 matplotlib**로 시각화하여 통계 자동화

이 과정을 통해 단순한 인공지능 모델 개발을 넘어, **실제 스마트팩토리 환경에서 필요한 전반적인 자동화 시스템 구성 능력**을 체득할 수 있었습니다.
