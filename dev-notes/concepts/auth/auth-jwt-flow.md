# JWT Authentication Flow

## 목적
SPA 환경에서 안전한 인증/인가 처리를 위한 JWT 흐름 정리

---

## 전체 인증 흐름

1. 사용자 로그인 요청
2. 서버에서 인증 정보 검증
3. Access Token / Refresh Token 발급
4. 클라이언트에 토큰 저장
5. API 요청 시 Access Token 포함
6. 서버에서 토큰 검증 후 인가 처리
7. 토큰 만료 시 재발급 또는 로그아웃 처리

---

## 토큰 설계

- Access Token
  - 짧은 만료 시간
  - API 요청 시 사용
- Refresh Token
  - 상대적으로 긴 만료 시간
  - Access Token 재발급 용도

## 설계 포인트
- Access / Refresh 분리 이유
- 만료 시 재발급 전략
- 프론트에서 인증 상태 유지 방식

---

## 클라이언트 저장 전략

- Access Token: 메모리 또는 안전한 스토리지
- Refresh Token: HttpOnly Cookie 또는 제한된 저장 방식

> XSS / CSRF 공격을 고려한 저장 위치 선택이 중요

---

## 인증 상태 관리 포인트

- 새로고침 시 인증 상태 유지 문제
- 토큰 만료 시 사용자 UX 고려
- 인증 실패 시 공통 처리 로직 필요

---

## 실무에서 배운 점

- 인증 로직은 반드시 중앙화해야 유지보수가 가능
- UI 단에서도 권한 분기 처리 필요
- “토큰이 있다” ≠ “권한이 있다”