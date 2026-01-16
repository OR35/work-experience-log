# 지능형 센서 플랫폼

## 📌 Project Summary

IoT 센서 데이터를 기반으로 한 플랫폼 서비스로,  
사용자 및 관리자 기능을 분리하여 운영 관리가 가능한 시스템입니다.

본 프로젝트에서 저는 **Frontend + Backend 기능 개발**을 담당했습니다.

---

## 🧑‍💻 My Role

- 센서·에이전트·모델 학습·최적화 기능과 연동된 사용자 / 관리자 UI 개발
- 센서 / 에이전트 / 데이터셋 / 모델 / 최적화 관리 기능의 화면 및 API 구현
- 사용자별 과금 정책을 반영한 비즈니스 로직 개발
- 접근 / 발송 / 이벤트 / 작업 이력 관리 화면 구현
- 시스템 로그 조회 및 운영 관리 기능 개발
- JWT 기반 인증 / 권한 분리 처리
- 공통 CRUD 패턴 및 화면 흐름 구조화
- 기능 개발 중 발생한 이슈 해결 및 구조 개선

---

## 🧱 System Overview

- Frontend: Vue.js 기반 SPA
- Backend: Spring Boot REST API
- 인증 방식: JWT 기반 인증/인가

자세한 구조는 👉 [`architecture.md`](./architecture.md)

---

## ⚙️ Implemented Features

### 🔹 지능형 센서 플랫폼 핵심 기능
- 센서 · 에이전트 · 데이터셋 · 모델 · 최적화 관리 기능
- 모델 학습·최적화 기능과 연동된 사용자 / 관리자 UI
- 사용자별 과금 정책을 반영한 동적 과금 관리

### 🔹 플랫폼 운영 관리 시스템
- 접근 / 발송 / 이벤트 / 작업 이력 관리
- 시스템 로그 조회 및 상태 확인 기능
- 운영자가 빠르게 판단할 수 있는 화면 흐름 구성

### 🔹 협업 및 개발 환경 개선
- ESLint / Prettier 기반 코드 컨벤션 정리
- Husky + Lint-staged를 활용한 Commit 단계 품질 관리

👉 기능 상세: [`features.md`](./features.md)

---

## 🚧 Issues & Troubleshooting

실제 개발 중 마주한 문제와 해결 과정을 정리했습니다.

👉 [`issues.md`](./issues.md)

---

## 🧠 Technical Decisions

구현 과정에서의 설계 선택과 이유를 정리했습니다.

👉 [`decisions.md`](./decisions.md)