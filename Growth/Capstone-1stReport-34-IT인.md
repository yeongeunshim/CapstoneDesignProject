<!-- Template for PROJECT REPORT of CapstoneDesign 2025-2H, initially written by khyoo -->
<!-- 본 파일은 2025년도 컴공 졸업프로젝트의 <1차보고서> 작성을 위한 기본 양식입니다. -->
<!-- 아래에 "*"..."*" 표시는 italic체로 출력하기 위해서 사용한 것입니다. -->
<!-- "내용"에 해당하는 부분을 지우고, 여러분 과제의 내용을 작성해 주세요. -->

# Team-Info
| (1) 과제명 | *Linceiver IO: Reducing Computational Cost in Federated Learning with Adaptive Low-Rank Perceiver IO*
|:---  |---  |
| (2) 팀 번호 / 팀 이름 | *34-IT인* |
| (3) 팀 구성원 | 심영은 (2194058): 리더, *실험용 서버구축 및 운영, 연합학습 환경 설계 및 구현, 통계분석 및 시각화* <br> 김경희 (2276034): 팀원, *모델 경량화 및 효율성 최적화, seqeunce length에 적응적으로 대응하는 모델 설계 및 구현* <br> 이채원 (2171039) : 팀원, *팀 레포지토리 및 산출물 관리, 모델 최적화 및 성능 개선, 실험 확장을 위한 다중 데이터셋 통합 구축 및 데이터 전처리*			 |
| (4) 팀 지도교수 | *이형준 교수님* |
| (5) 과제 분류 | *연구 과제* |
| (6) 과제 키워드 | *Perceiver IO, Linformer, Federated Learning*  |
| (7) 과제 내용 요약 | *전체 과제 내용을 10줄이내로 설명* |


<br>

# Project-Summary
| 항목 | 내용 |
|:---  |---  |
| (1) 문제 정의 | *본 과제를 통하여 해결/완화하고자하는 문제에 대하여 기술. Target Customer 정의와 해결해야 할 문제점들(pain points)대한 내용 기술*  |
| (2) 기존연구와의 비교 | *유사한 과제/연구/서비스/시스템의 예를 들고, 각각의 장단점을 기술할 것. 특히, 본 과제가 유사과제에 대하여 갖는 장점을 부각할 것* |
| (3) 제안 내용 | *본 프로젝트에서 제시한 문제를 해결하기 위해 새롭제 제안하는 해결책 or 해결책들에 대하여 기술 .* |
| (4) 기대효과 및 의의 | *프로젝트의 결과물을 통하여 얻을 수 있는 기대효과 및 의의에 대하여 기술 .* |
| (5) 주요 기능 리스트 | *(3)에서 제안한 해결책들을 지원or구현하기 위하여 필요한 주요 기능 혹은 기능을을 List-up하고, <br> 각각에 대하여 설명* <br> * 본 항목의 내용을 충실히 기재 바람니다. *|

<br>
 

# Project-Design & Implementation

| 항목 | 내용 |
|:---  |---  |
| (1) 요구사항 정의 |   **1. 경량화** <br> - Linceiver IO는 Perceiver IO에 비해 빠른 학습 속도를 보이고, 더 적은 용량의 모델 크기를 갖춰야 한다. <br> - Attention 연산의 복잡도를 기존 $O(N^2)$에서 $O(kN)$으로 줄이기 위한 Low-Rank Projection 기반의 구조(Linformer 방식)를 적용해야 한다. <br> - 모델 파라미터 수는 가능한 한 최소화하여 연합학습 클라이언트에서 동작할 수 있도록 경량화를 달성해야 한다. <br><br> **2. 범용성** <br> - Linceiver IO는 다양한 크기의 데이터, 더 나아가 다양한 모달리티 데이터에서 안정적인 학습 효과를 보여야 한다. <br> - Linceiver IO의 projection 차원 k는 학습 과정에서 동적으로 변경 가능하며, 정확도와 학습 속도 및 용량에서 최적의 Trade-off를 만족하는 수치로 설정되어야 한다. <br><br> **3. 연합학습 적용성** <br> - 기존 Perceiver IO가 연합학습에 적용된 사례가 없는 만큼, 본 프로젝트는 Linceiver IO의 구조를 연합학습 시스템에 최적화하여 새롭게 설계해야 한다. <br> - 연합학습 환경에서 Shared Latent Perceiver와 개별 클라이언트의 Perceiver Head로 구성된 하이브리드 구조를 적용해야 한다. <br> - 클라이언트 단은 독립적인 데이터셋을 기반으로 로컬 학습을 수행하며, 중앙 서버는 각 클라이언트로부터 데이터를 수합하지 않고, Shared Latent Perceiver의 가중치를 받아 평균화하는 방식으로 학습을 통합해야 한다. <br> - 학습된 모델은 중앙집중형 학습 모델 대비 유사하거나 더 나은 성능을 달성해야 한다. |
| (2) 전체 시스템 구성 | <img src="https://github.com/yeongeunshim/CapstoneDesignProject/blob/main/%EC%8B%9C%EC%8A%A4%ED%85%9C%EA%B5%AC%EC%A1%B0.jpg?raw=true" alt="시스템 구성도" width="480"/> <br><br> **1. User (연구팀원)** <br> - 개인 PC 또는 노트북에서 VS Code를 실행하고 원격 서버에 접속함 <br> - 서버 내부의 코드 작성, 실행, 디버깅을 수행함 <br><br> **2. VS Code Remote - SSH** <br> - 사용자의 로컬 VS Code에서 서버에 안전하게 접속하게 해주는 도구 <br> - 코드 수정, 실험 실행 등을 로컬에서 바로 하듯이 원격으로 수행 가능 <br><br> **3. ML/DL 개발환경** <br> - Python과 PyTorch 기반의 소프트웨어 환경이 구성되어 있음 <br> - 아나콘다 가상환경으로 통일된 패키지 및 라이브러리 버전 유지 <br><br> **4. GPU 서버 환경** <br> - 실제 연산이 수행되는 장소로, 고성능 GPU가 장착된 서버임 <br> - Ubuntu 리눅스 OS를 기반으로 실행되며, PyTorch에서 GPU를 활용해 모델 학습과 추론을 처리함 |
| (3) 주요엔진 및 기능 설계 | **1. VS Code Remote SSH** <br> - 팀원과 서버 간의 연결을 중계하는 원격 개발 도구로, 로컬에서 VS Code를 실행하고 Remote SSH 확장을 통해 서버에 접속함 <br> - 서버 내부 파일을 직접 수정하거나 실행 가능하며, Microsoft에서 개발한 오픈소스 확장 기능임 <br><br> **2. Python 3.8.20** <br> - 프로젝트의 주 개발 언어로, 다양한 머신러닝 라이브러리와의 높은 호환성으로 채택됨 <br><br> **3. PyTorch 1.10.2** <br> - Perceiver IO 및 Linceiver IO 모델 구현, 학습 및 추론에 사용되는 Python 기반 딥러닝 프레임워크 <br><br> **4. Ubuntu 20.04.6** <br> - 서버 운영체제로 사용되는 오픈소스 기반 리눅스 배포판 <br><br> **5. NVIDIA GeForce RTX 3070** <br> - CUDA 기반 GPU 연산 가속이 가능한 고성능 그래픽카드로, PyTorch와 호환되어 빠른 연산을 지원함 <br><br> **6. Anaconda 가상환경** <br> - 패키지 의존성 충돌 방지를 위해 모든 개발자가 동일한 환경을 사용함 <br> - 환경 복제를 통해 실험의 재현성 확보 가능 |
| (4) 주요 기능의 구현 | **1. Perceiver IO의 Attention 연산을 경량화** <br> 본 논문에서는 범용성이 뛰어난 Perceiver IO 모델을 컴퓨팅 자원이 부족한 연합학습 클라이언트에 적용하기 위한 경량화 모델인 Linceiver IO를 제안한다. Linceiver IO의 경우 Perceiver IO의 attention 연산에 low-rank projection을 활용하는 Linformer 구조를 적용하여 계산 복잡도를 $O(N^2)$에서 $O(kN)$로 줄일 수 있다. 설계한 Linceiver IO의 구조는 아래와 같다. <br><br> <img src="https://github.com/yeongeunshim/CapstoneDesignProject/blob/main/linceiver%20io%20%EA%B5%AC%EC%A1%B0.jpg?raw=true" alt="Linceiver IO 구조도" width="480"/> <br><br> **2. 다양한 사이즈 및 모달리티 데이터에 범용적으로 사용 가능한 모델 설계** <br> Low-Rank Projection에서 사용하는 k값은 학습가능한 파라미터를 추가하여 학습과정에서 다른 파라미터들과 같이 업데이트 된다. 입력 데이터에 따라 각 Low-rank Projection 차원의 상대적 중요도를 동적으로 결정하여, 필요한 정보만 효과적으로 선택하고, 불필요한 차원의 정보를 억제함으로써 효율적인 학습을 수행할 수 있다. <br><br> **3. 설계한 모델에 맞는 새로운 연합학습 구조 제안** <br> Perceiver IO 구조를 연합학습에 적용한 사례가 없기 때문에, 설계한 모델이 연합학습 환경에서 잘 동작할 수 있게끔 연합학습 구조와 시스템, 정책 등을 독자적으로 설계하였다. 본 연구에서는 Linceiver IO를 Shared Latent Perceiver, Perceiver Head로 분리하여 연합학습 시스템을 설계했다. Shared Latent Perceiver는 연합학습 환경에서 모든 클라이언트가 공유하는 백본 네트워크로 동작하며, 클라이언트들이 학습한 가중치를 수집하고 평균화하여 지속적으로 업데이트하도록 했다. 한편, 클라이언트는 자체적인 Embedding Layer, Perceiver Head와 함께 학습 가능한 Query Vector를 보유하고 있으며, 독립적인 로컬 데이터셋을 활용하여  학습한다. 전체 연합학습 시스템의 구조도는 아래와 같다. <br><br> <img src="https://github.com/yeongeunshim/CapstoneDesignProject/blob/main/%EC%97%B0%ED%95%A9%ED%95%99%EC%8A%B5%EA%B5%AC%EC%A1%B0.jpg?raw=true" alt="연합학습 구조도" width="480"/> |
| (5) 기타 | *기타 사항을 기술* |

<br>
