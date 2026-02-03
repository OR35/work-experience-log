# [Code] Composable 도입을 통한 검색 상태 복구 리팩토링

## 1. 리팩토링 전/후 비교 (As-Is vs To-Be)

### 리팩토링 전 (As-Is)
각 페이지의 `onMounted`마다 수동으로 데이터를 체크하고 할당하여 코드가 길고 에러에 취약했습니다.

```javascript
onMounted(() => {
  const saved = searchStore.getCondition(MENU_KEY);
  if (saved) {
    if (saved.pageInfo) pageInfo.value = { ...pageInfo.value, ...saved.pageInfo };
    if (saved.searchInfo && searchInfoRef.value) {
      Object.assign(searchInfoRef.value.searchInfo, saved.searchInfo);
    }
    // ... if문 반복
    searchStore.resetCondition(MENU_KEY);
  }
});
```

### 리팩토링 후 (To-Be)
전용 Composable useSearchRestore를 사용하여 핵심 로직을 은닉했습니다.

```JavaScript
const { restore } = useSearchRestore('OPTIMIZATION');

onMounted(async () => {
  await restore({
    pageInfo,
    searchInfoRef,
    extraFields: ['optimizationStatus'],
  });
  fetchData();
});
```

## 2. 핵심 구현 로직 (useSearchRestore.js)

```JavaScript
import { nextTick } from 'vue';

export function useSearchRestore(MENU_KEY) {
  const restore = async ({ pageInfo, searchInfoRef, extraFields = [] }) => {
    const saved = searchStore.getCondition(MENU_KEY);
    if (!saved) return false;

    // 1. 공통 페이징 정보 복원 (Object.assign으로 반응성 유지)
    if (saved.pageInfo) Object.assign(pageInfo.value, saved.pageInfo);

    // 2. 자식 컴포넌트(Ref) 마운트 완료 대기
    await nextTick();

    // 3. 자식 컴포넌트 내부의 검색 조건 주입
    if (searchInfoRef.value) {
      if (saved.searchInfo) Object.assign(searchInfoRef.value.searchInfo, saved.searchInfo);
      
      // extraFields 및 기타 동적 필드 처리
      // ... (추상화 로직)
    }
    searchStore.resetCondition(MENU_KEY);
    return true;
  };

  return { restore };
}
```
---

## 3. 핵심 포인트
1. await nextTick(): 자식 컴포넌트의 렌더링이 완료된 후 ref에 접근하도록 보장하여 undefined 에러를 원천 차단했습니다.
2. Object.assign: 객체의 참조 주소를 유지하면서 내부 값만 변경하여 Vue의 반응형 시스템이 정상적으로 작동하도록 설계했습니다.
3. 추상화 레벨: 특정 페이지에 종속되지 않도록 필드명을 동적으로 넘겨받아 확장성을 확보했습니다.
