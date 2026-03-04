# [Code] Quill Editor 도입 및 이미지 정렬 커스텀 핸들러 구현

## 1. 핵심 리팩토링 코드

### 이미지 정렬 핸들러 (Custom Align Handler)
이미지를 클릭하여 선택된 상태일 때, 툴바의 정렬 버튼을 누르면 해당 이미지에 직접 스타일을 주입합니다.

```javascript
handlers: {
  align: (value) => {
    if (selectedImg) {
      // 이미지 스타일 초기화
      selectedImg.style.display = 'block';
      selectedImg.style.margin = '';

      if (value === 'center') {
        selectedImg.style.margin = '0 auto';
      } else if (value === 'right') {
        selectedImg.style.marginLeft = 'auto';
      }
      
      // 변경된 HTML 강제 업데이트
      emit('update:modelValue', quill.root.innerHTML);
    } else {
      // 일반 텍스트는 기본 정렬 로직 수행
      quill.format('align', value);
    }
  }
}
```

### 이미지 선택 감지 (Selection Logic)
에디터 내 이미지를 클릭했을 때 시각적 피드백(Outline)을 주고, 정렬 대상(selectedImg)으로 지정합니다.

```JavaScript
quill.root.addEventListener('click', e => {
  if (e.target.tagName === 'IMG') {
    selectedImg = e.target;
    // 모든 이미지 아웃라인 초기화 후 선택된 이미지만 강조
    document.querySelectorAll('.ql-editor img').forEach(img => img.style.outline = '');
    selectedImg.style.outline = '2px solid #4096ff';
  } else {
    if (selectedImg) {
      selectedImg.style.outline = '';
      selectedImg = null;
    }
  }
});
```
---

## 2. 주요 개선 사항

- 스타일 확장: header, size, color, background, align 등 툴바 옵션을 대폭 확장하여 편집 자유도 제공.
- 이미지 정렬 이슈 해결: Quill 기본 기능에서 지원하지 않는 이미지 중앙/우측 정렬을 display: block과 margin 속성 제어를 통해 구현.
- 무한 루프 방지: watch와 text-change 이벤트 사이에서 데이터가 반복적으로 업데이트되는 문제를 isUpdating 플래그로 차단.

---

## 3. 구현 팁 (Pro-tips)
- Attributor 등록: Quill.import('attributors/style/align')를 통해 정렬 정보가 클래스(ql-align-center)가 아닌 인라인 스타일(style="text-align: center")로 저장되게 하여 외부 뷰어에서의 호환성을 높였습니다.
- CSS 오버라이딩: .ql-editor img { max-width: 100%; } 설정을 통해 모바일 환경에서도 이미지가 본문을 벗어나지 않도록 방어 코드를 작성했습니다.

---

> **관련 코드:** [Quill Editor 구현](../../code-notes/frontend/ui/quill-editor-implementation.md)
