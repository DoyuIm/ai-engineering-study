# 임도유 (doyu) · AI 엔지니어링 스터디

![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.162-009688?logo=fastapi&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-7-DC382D?logo=redis&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-kind-326CE5?logo=kubernetes&logoColor=white)

5명이 7주 동안 **Docker → FastAPI → RAG → Kubernetes** 순서로 익히고, 마지막 주에 "k8s 위에 배포된 RAG API"를 팀으로 만드는 스터디의 개인 작업물이에요.
매주 직접 실습해서 확인한 숫자와 막힌 점을 같이 남겨요.

> 팀 저장소: [SoleMin/Study-service_basic](https://github.com/SoleMin/Study-service_basic) · 이 저장소는 팀 저장소의 `members/doyu/` 폴더를 옮겨 온 것이에요.

## 로드맵

| 주차 | 주제 | 상태 |
| --- | --- | --- |
| 1 | Docker 기초 | ✅ |
| 2 | FastAPI + docker compose + Redis 캐시 | ✅ |
| 3 | RAG 기초 (LangChain) | ⬜ |
| 4 | RAG 고도화 + `/ask` API | ⬜ |
| 5 | Kubernetes 기초 (kind) | ⬜ |
| 6 | RAG 앱 k8s 배포 | ⬜ |
| 7 | 팀 프로젝트 통합 · 데모데이 | ⬜ |

## 주차별 결과물

| 주차 | 폴더 | 내용 | 상태 |
| --- | --- | --- | --- |
| 1 | `week1/` | Python 앱 이미지 빌드·실행, 빌드 캐시 순서 실험, `-p`·`-v` 실습, slim vs full 크기 비교 | 완료 |
| 2 | `week2/` | FastAPI `/chat`(가짜 LLM) + Redis TTL 캐시, compose, async 실험 | 완료 |

### 직접 잰 숫자

| 실험 | 결과 | 기록 |
| --- | --- | --- |
| Dockerfile 레이어 순서 (app.py만 수정 후 재빌드) | 의존성 먼저 **0.3s** vs 코드 먼저 2.6s | [week1-notes](wiki/raw/week1-notes.md) |
| 베이스 이미지 크기 | `python:3.11-slim` **234MB** vs `python:3.11` 1.62GB | [week1-notes](wiki/raw/week1-notes.md) |
| `/chat` 같은 질문 두 번 (LLM 2초 지연 가정) | 2,014ms → Redis 캐시 **0.6ms** | [week2-notes](wiki/raw/week2-notes.md) |
| 동시 요청 3개, 각 3초 대기 | `async` + `await` 3s/3s/3s, `async` 안 `time.sleep` **3s/6s/9s** | [async-fastapi](wiki/pages/async-fastapi.md) |

## 위키 페이지

| 페이지 | 팀 위키 반영 |
| --- | --- |
| `wiki/pages/image-vs-container.md` | 리뷰 대기 |
| `wiki/pages/async-fastapi.md` | 리뷰 대기 |

## 실행해 보기

```bash
# 1주차: 이미지 빌드와 실행
cd week1 && docker build -t week1-app . && docker run --rm week1-app

# 2주차: FastAPI + Redis (실제 LLM은 3주차에 연결)
cd week2 && cp .env.example .env
docker compose up -d --build
open http://localhost:8000/docs
```

## 폴더 구조

```
.
├── week1/          Dockerfile 실습 (Dockerfile.bad, .full은 비교 실험용)
├── week2/          FastAPI 앱, docker-compose.yml, .env.example
└── wiki/
    ├── raw/        실습 메모, 에러 로그, 참고 링크
    └── pages/      개념 정리 (한 줄 정의 / 왜 필요한가 / 최소 예제 / 막힌 점 / 출처)
```
