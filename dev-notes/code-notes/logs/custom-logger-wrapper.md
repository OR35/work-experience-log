# [Code] 환경 변수 기반 Custom Logger Wrapper

## 1. 구현 코드 (`logger.js`)

```javascript
/**
 * 환경 변수(VITE_APP_EVN_NM)에 따른 콘솔 제어 유틸리티
 */
const envNm = import.meta.env.VITE_APP_EVN_NM;

export const logger = {
  // 로컬 전용: 일반 로그
  log: (...args) => {
    if (envNm === 'local') console.log('[LOCAL_LOG]:', ...args);
  },
  // 개발 서버까지 허용: 주요 정보 로그
  info: (...args) => {
    if (envNm === 'local' || envNm === 'dev') console.info('[INFO]:', ...args);
  },
  // 로컬 전용: 객체/배열 시각화 디버깅
  debug: (label, data) => {
    if (envNm === 'local') {
      console.group(`%c[DEBUG] ${label}`, 'color: #FF9800; font-weight: bold');
      console.table(data);
      console.groupEnd();
    }
  },
  // 공통: 에러 로그 (모든 환경 출력)
  error: (...args) => {
    console.error('[ERROR]:', ...args);
  },
  // 객체 구조 상세 확인
  dir: (obj) => {
    if (envNm === 'local') console.dir(obj);
  }
};
```
---

## 2. 전역 설정 (`main.js` & `eslintrc`)

```javascript
// main.js
import { logger } from '@/assets/js/logger';
window.logger = logger;

// eslintrc.cjs
globals: {
  logger: 'readonly', // 전역 변수 인식 및 수정 방지
},
```
---

## 3. 사용법

기존 console.log() 대체
-> `logger.log()`
