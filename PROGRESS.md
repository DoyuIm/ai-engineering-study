# 진행 기록

7주 AI 엔지니어링 스터디에서 주차마다 무엇을 했고, 무엇을 재 봤고, 어디서 막혔는지 한 곳에 모은 기록이에요.
자세한 실습 과정은 주차별 실습 메모(`wiki/raw/weekN-notes.md`)에, 개념 정리는 위키 페이지(`wiki/pages/`)에 있어요.

| 주차 | 주제 | 상태 | 팀 PR |
| --- | --- | --- | --- |
| 0 | 사전 준비 | 완료 | — |
| 1 | Docker 기초 | 완료 | [#9](https://github.com/SoleMin/Study-service_basic/pull/9) |
| 2 | FastAPI + docker compose + Redis | 완료 | [#3](https://github.com/SoleMin/Study-service_basic/pull/3) |
| 3 | RAG 기초 (LangChain) | 완료 | [#8](https://github.com/SoleMin/Study-service_basic/pull/8) |
| 4 | RAG 고도화 + `/ask` API (발표) | 완료 | [#13](https://github.com/SoleMin/Study-service_basic/pull/13) |
| 5 | Kubernetes 기초 (kind) | 완료 | [#16](https://github.com/SoleMin/Study-service_basic/pull/16) |
| 6 | RAG 앱 k8s 배포 | 예정 | — |
| 7 | 팀 프로젝트 통합 · 데모데이 | 예정 | — |

---

## 0주차 · 사전 준비

**한 것**
- Docker Desktop 29.9.2 / Compose v5.6.1, Python 3.11, Git, kubectl v1.37.1, kind v0.33.0 설치
- `hello-world` 실행, `python:3.11-slim`·`redis` 이미지 미리 받기

**막힌 점**
- Homebrew로 Docker Desktop을 설치할 때 관리자 비밀번호가 필요해서 터미널에서 직접 설치
- conda 환경이 PATH 앞에 있어 `python3`가 3.9로 잡힘 → `~/.zshrc`에서 Homebrew Python 3.11을 앞에 두어 해결

---

## 1주차 · Docker 기초

**한 것** · 코드 [`week1/`](week1/) · 메모 [week1-notes](wiki/raw/week1-notes.md) · 위키 [image-vs-container](wiki/pages/image-vs-container.md)
- Python 앱을 `python:3.11-slim` 이미지로 빌드하고 실행
- 빌드 캐시, 포트 매핑(`-p`), 볼륨 마운트(`-v`), `.dockerignore`, 이미지 크기 실험

**재 본 숫자**
| 실험 | 결과 |
| --- | --- |
| `app.py`만 고치고 재빌드 | 의존성 먼저 COPY **0.3초** (pip CACHED) vs 코드 먼저 2.6초 (pip 다시 실행) |
| 베이스 이미지 크기 | `python:3.11` 1.62GB vs `python:3.11-slim` **234MB** |
| `-p` 없이 / 있을 때 | 연결 실패 / HTTP 200 |
| `-v` 없이 / 있을 때 | 컨테이너와 함께 파일 사라짐 / 호스트에 남음 |

**배운 것**
- Dockerfile 한 줄이 레이어 하나이고, 바뀐 줄부터 아래는 전부 다시 빌드된다. 잘 안 바뀌는 것을 위에 둔다.

**막힌 점**
- Docker Desktop 앱이 꺼져 있으면 `docker.sock`에 연결 실패 → 앱을 먼저 켜기

---

## 2주차 · FastAPI + docker compose + Redis

**한 것** · 코드 [`week2/`](week2/) · 메모 [week2-notes](wiki/raw/week2-notes.md) · 위키 [async-fastapi](wiki/pages/async-fastapi.md)
- `GET /health`, `POST /echo`(Pydantic), `POST /chat` + Redis TTL 캐시를 compose로 함께 띄우기
- `/chat`은 실제 LLM 대신 2초 지연 가짜 응답 (LLM은 3주차에 연결하기로 결정)
- async 실험: `def` / `async def + await` / `async def + time.sleep`

**재 본 숫자**
| 실험 | 결과 |
| --- | --- |
| 같은 질문 두 번 | 1번째 2,014ms → 2번째(캐시) **0.6ms**, TTL 3600 |
| 틀린 타입 요청 | 422 |
| 동시 요청 3개, 각 3초 대기 | `async`+`await` 3/3/3초, `async` 안 `time.sleep` **3/6/9초** |

**배운 것**
- 컨테이너 안의 `localhost`는 자기 자신이라, compose에서는 서비스 이름(`redis`)으로 접속한다.
- `async def` 안에서 동기 함수로 기다리면 이벤트 루프가 막혀 모든 요청이 줄을 선다.

**막힌 점**
- 같은 주소로 동시에 요청하면 브라우저가 하나씩 보내서 async 실험이 안 됨 → `?n=1,2,3`처럼 주소를 바꾸거나 curl로 동시에 보내기

---

## 3주차 · RAG 기초 (LangChain)

**한 것** · 코드 [`week3/`](week3/) · 메모 [week3-notes](wiki/raw/week3-notes.md) · 위키 [rag-pipeline](wiki/pages/rag-pipeline.md)
- 팀원 위키 16페이지를 로드 → `RecursiveCharacterTextSplitter` → 청크 ID → bge-m3 → Chroma → 검색 → Claude(`claude-opus-5-5`) 생성 (LCEL)
- 답변과 함께 출처(페이지 이름) 반환
- 질문 20개를 직접 만들고 채점 스크립트로 청크 크기 비교

**재 본 숫자**
| chunk_size | 청크 수 | Recall@5 | MRR@5 |
| --- | --- | --- | --- |
| 200 | 305 | **1.00** | **0.938** |
| 500 (기준선) | 112 | 0.95 | 0.925 |
| 1000 | 55 | 0.95 | 0.925 |

- 위키에 없는 질문(쿠버네티스 스케일링)에는 지어내지 않고 "위키에 없다"고 답함

**배운 것**
- 질문이 20개면 한 문제가 0.05라서, 작은 점수 차이로 결론 내면 안 된다.

**막힌 점**
- 팀원 두 명의 페이지 이름이 같아(`async-fastapi`) 출처가 헷갈림 → `멤버/페이지`로 이름 붙이기
- `.env`의 빈 값이 기본 경로를 덮어씀 → `os.getenv(...) or 기본값`으로 수정

---

## 4주차 · RAG 고도화 + `/ask` API (발표 담당)

**한 것** · 코드 [`week4/`](week4/) · 메모 [week4-notes](wiki/raw/week4-notes.md) · 위키 [hybrid-search-reranker](wiki/pages/hybrid-search-reranker.md)
- 팀 `evaluate.py`와 질문 25개로 검색 방식 8가지 비교 (벡터, BM25 띄어쓰기·형태소, 하이브리드 RRF 3가지, 리랭커 후보 10·20개)
- RAG를 FastAPI `POST /ask`로 감싸고 Docker 이미지로 빌드
- 발표: "검색을 한 단계 더: 하이브리드 검색과 리랭커" 19장 (대본·예상 질문 포함)

**재 본 숫자** (평가 결과 페이지 제외 30페이지)
| 기법 | MRR@5 | 질문당 검색 |
| --- | --- | --- |
| 벡터 (기준선) | 0.780 | 43ms |
| 하이브리드 0.7 : 0.3 | 0.787 | 72ms |
| **리랭커 (후보 10개)** | **0.863** | 3,237ms |

| 발견 | 결과 |
| --- | --- |
| 평가 질문 원문이 든 위키 페이지가 검색 대상에 섞임 | 빼자 BM25 0.688 → 0.783, 리랭커 0.808 → 0.863 |
| Mac Docker는 GPU를 못 써서 켤 때마다 CPU로 임베딩 | 2시간 넘게 안 뜸 → 색인을 디스크에 저장해 **91초** |
| `/ask` 전체 응답 (호스트) | 검색 4.8초 + Claude 3.4초 ≈ 8초 |

**배운 것**
- 하이브리드 개선(+0.007)은 질문 하나 분량(0.02)보다 작아서 개선으로 보기 어렵다. 리랭커는 확실히 오르지만 시간이 70배 든다.
- 평가 데이터가 검색 대상에 섞이면 모든 점수가 틀어진다. 데이터부터 깨끗하게.

**막힌 점**
- 형태소 분석기(Kiwi)가 `8888:8888`을 `W_SERIAL` 품사로 묶었는데 품사 필터가 버려서 포트 번호 키워드가 사라짐 → `W_*` 품사도 남기게 수정
- 하이브리드 뒤에 리랭커를 붙이니 후보가 40개라 질문당 51초 → 리랭커 앞에서 후보 수를 묶음
- `.env`의 빈 `DOCS_DIR=`이 Dockerfile의 `ENV`를 덮어씀 → `docker run -e`로 다시 지정

---

## 5주차 · Kubernetes 기초 (kind)

**한 것** · 매니페스트 [`week5/`](week5/) · 메모 [week5-notes](wiki/raw/week5-notes.md) · 위키 [k8s-pod-deployment-service](wiki/pages/k8s-pod-deployment-service.md)
- kind 3노드 클러스터, nginx Deployment + Service, `port-forward`
- 2주차 FastAPI + Redis를 k8s로 (`kind load docker-image`)
- 자가 복구, 레플리카 조절, `ImagePullBackOff`·`CrashLoopBackOff` 재현, 롤링 업데이트와 되돌리기

**재 본 숫자**
| 실험 | 결과 |
| --- | --- |
| Pod 하나 삭제 | **0.9초** 만에 새 Pod |
| 레플리카 2 → 5 | 0.6초, 워커 두 대에 3개·2개 |
| api Pod 2개가 Redis 공유 | Pod B 첫 호출도 캐시에서 **7.6ms** |
| 롤링 업데이트 중 요청 실패 | 아무것도 없음 1/60 → readiness만 1/60 → readiness + preStop **0/80** (3회) |

**배운 것**
- Deployment는 원하는 상태를 적어 두는 것이고, 컨트롤러가 현재 상태를 계속 맞춘다.
- 무중단 배포에는 새 Pod 쪽(readiness probe)과 꺼지는 Pod 쪽(preStop) 둘 다 필요하다.

**막힌 점**
- ImagePullBackOff는 `logs`로 안 보이고 `describe`의 Events에서 원인이 보임
- CrashLoopBackOff 직후 `logs`가 실패 → 잠깐 기다렸다 `--previous`로 확인

---

## 6주차 · RAG 앱 k8s 배포 (예정)
- 4주차 `/ask`를 ConfigMap(설정), Secret(API 키), PVC(임베딩 색인·모델 캐시)로 kind에 배포
- readiness/liveness probe(`/health`), 위키 재적재 Job
- 4주차에 확인한 대로 kind 노드도 GPU가 없으니 색인은 PVC로 유지해야 함

## 7주차 · 팀 프로젝트 통합 (예정)
