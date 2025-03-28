<!-- Template for PROJECT REPORT of CapstoneDesign 2025-2H, initially written by khyoo -->
<!-- 본 파일은 2025년도 컴공 졸업프로젝트의 <1차보고서> 작성을 위한 기본 양식입니다. -->
<!-- 아래에 "*"..."*" 표시는 italic체로 출력하기 위해서 사용한 것입니다. -->
<!-- "내용"에 해당하는 부분을 지우고, 여러분 과제의 내용을 작성해 주세요. -->

# Team-Info
| (1) 과제명 | Lightweight Part-Aware 3D Texture Editing Using 2D Stable Diffusion |
|:---  |---  |
| (2) 팀 번호 / 팀 이름 | 19-JEY |
| (3) 팀 구성원 | 이다예 (1917026): 리더, 아이디어 및 프로젝트 계획 관리, 2D-to-3D adaptation, 2D semantic segmentation 연구 및 개발, part segmentation 검증 <br> 이상은 (2171036): 팀원, 회의/면담 기록 정리 및 관리, Text to texture 연구 및 개발, 2D-to-3D adaptation, 2D semantic segmentation 검증 <br> 정지우 (2171046) : 팀원, 프로젝트 마일스톤 관리 및 공유 파일 관리, part segmentation 연구 및 개발, Text to texture 검증			 |
| (4) 팀 지도교수 | 김영준 교수님 |
| (5) 과제 분류 | 연구 과제 |
| (6) 과제 키워드 | Semantic Part Segmentation, Controllable Diffusion, Texture generation  |
| (7) 과제 내용 요약 | 본 연구는 고사양 3D 모델링 기술 없이도 특정 부위의 텍스처를 정밀하게 편집할 수 있는 2D 기반의 경량화된 텍스처링 프로세스를 제안한다. SyncDreamer를 활용하여 단일 이미지로부터 멀티뷰를 생성하고, VLPart와 SAM을 통해 2D 이미지에서 의미 기반 부위 분할을 수행한다. 이후 Text2LIVE를 활용해 텍스트 조건에 맞는 부위별 텍스처 편집을 적용한다. Aggregation 모듈을 통해 시점 간 마스크를 통합하고, refinement 단계에서 SyncDreamer 기반 정렬을 수행해 정합성을 높인다. 최종적으로 부위별 제어 정확도와 텍스처 품질을 동시에 확보하면서도 일반 사용자가 쉽게 접근할 수 있는 시스템을 구축하는 것을 목표로 한다. |

<br>

# Project-Summary
| 항목 | 내용 |
|:---  |---  |
| (1) 문제 정의 | 3D 모델링에서 사용자 맞춤형 텍스처 편집은 점점 수요가 증가하고 있으나, 현존하는 3D 텍스처링 기법은 부위별 정밀 제어에 한계가 있으며 고사양 하드웨어와 전문 지식이 필요하다. 이를 해결하고자, 일반 사용자도 쉽게 다룰 수 있는 2D 기반 부위별 텍스처 편집 방식을 제안한다.  |
| (2) 기존연구와의 비교 | 기존 연구들은 TEXTure, Paint3D, Text2Tex 등 고정된 3D mesh 기반 텍스처링 방식을 활용하며, 부위별 정밀 제어에 어려움이 있었다. 본 연구는 VLPart, SAM 등 최신 2D segmentation 기술을 활용해 부위 인식 정확도를 높이고, Text2LIVE 기반의 2D 텍스처 편집을 통해 경량화 및 사용자 경험을 향상시킨다. |
| (3) 제안 내용 | SyncDreamer로 생성한 멀티뷰 이미지에 SAM과 VLPart를 이용해 부위별 마스크를 생성하고, Text2LIVE 기반의 텍스처 편집을 수행한 후, Aggregation module을 통해 정보를 통합하고 refinement 과정에서 2D-3D 간 시점 정렬 및 정확도 비교를 통해 최종 텍스처 결과물을 생성한다. |
| (4) 기대효과 및 의의 | 고사양 3D 모델링 환경 없이도 정밀한 부위별 텍스처 편집이 가능하며, 사용자 친화적이면서도 높은 품질의 결과물을 생성할 수 있다. 이는 XR/VR 콘텐츠 제작, 사용자 맞춤형 가구 디자인, 메타버스 3D asset 제작 등에서 활용 가능하다. |
| (5) 주요 기능 리스트 | - 멀티뷰 이미지 생성: SyncDreamer를 활용해 단일 이미지 기반 멀티뷰 생성<br>- 2D 부위 분할: VLPart → SAM 순으로 의미 기반 및 경계 기반 분할 수행<br>- 텍스처 편집: Text2LIVE로 부위별 텍스트 조건 기반 텍스처 편집<br>- Aggregation 및 렌더링: 멀티뷰 마스크 통합 및 부위별 렌더링<br>- Refinement: SyncDreamer 기반 멀티뷰와 정렬 및 품질 보정<br>- 성능 비교 실험: 다양한 최신 3D 렌더링 방식과의 성능 비교|

<br>
 
# Project-Design & Implementation
| 항목 | 내용 |
|:---  |---  |
| (1) 요구사항 정의 | - 기능 요구사항:<br>① SyncDreamer 멀티뷰 생성<br>② VLPart + SAM 기반 2D segmentation<br>③ Text2LIVE를 활용한 부위별 텍스처 편집<br>④ Aggregation module 설계 및 해당 데이터 기반 3D rendering <br>⑤ 3D/2D 결합 refinement <br><br>- 데이터 요구사항:<br>가구 중심의 라벨링 이미지 (Pascal-Part, ADE20K 등 가공), 3D 렌더링 결과 비교용 ground truth |
| (2) 전체 시스템 구성 | - 모듈 구조:<br>Input → SyncDreamer → Segmentation (VLPart + SAM) → Texture Editing (Text2LIVE) → Aggregation → Refinement<br><br>- 외부 모듈: <br>SyncDreamer (multiview generation), VLPart/SAM (segmentation), Text2LIVE (texture editing) |
| (3) 주요엔진 및 기능 설계 | - Multiview Generator: SyncDreamer를 통해 8-view 동기화 이미지 생성<br>- Segmenter: VLPart로 의미 정보 분할 후 SAM으로 경계 보정<br>- Texturing Module: Text2LIVE를 이용해 텍스트 기반 RGBA 편집<br>- Aggregation & Refinement: 다양한 시점 마스크 통합, SyncDreamer 시점 보정 활용 |
| (4) 주요 기능의 구현 | - 부위 분할 구현: <br>VLPart + SAM 순차 적용하여 부위별 마스크 생성<br><br>- 텍스처 편집 구현: <br>Text2LIVE로 텍스트 조건 기반 RGBA 편집 레이어 생성 및 적용 |
| (5) 기타 | - NeRF, NeUS, Gaussian Splatting 등 최신 렌더링 기법 비교 실험 중 <br>- 가구 데이터셋 가공 및 라벨링 작업 진행 중 <br>- refinement 오류 방지를 위한 fallback 로직 설계 중 (텍스처 대신 마스크 기준 비교)  |

<br>
