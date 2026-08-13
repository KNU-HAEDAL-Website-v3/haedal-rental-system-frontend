# Dark-Mode Color Palette

색조는 라이트 base(`#FFF3B0`, 따뜻한 노랑)의 반대편인 청보라 네이비로 잡았다. 밤바다를 배경으로 두기 위해 선택했다.

---

## 1. Surface & Background

- color.surface.layer-0(base, 웹사이트 최하단 배경) : #0E1426
- color.surface.layer-1(base 위에 배경, 카드 영역) : #182034
- color.surface.layer-2(layer-1 위에 배경) : #222B44
- color.surface.brand(브랜드 배경 컬러) : #FFE500
- color.surface.disabled(비활성화 페이지용) : #1B2133

| 구분 | 라이트 | 다크 | 비고 |
| --- | --- | --- | --- |
| layer-0 | #FFF3B0 | #0E1426 | 순검정을 쓰지 않는다. 그림자가 보일 여지를 남긴다 |
| layer-1 | #FFFDF5 | #182034 | 카드 |
| layer-2 | #FFFEE0 | #222B44 | 카드 위 칩·입력 배경 |
| brand | #FFE500 | #FFE500 | 유지 |
| disabled | #F2EFE9 | #1B2133 | 카드보다 어둡게 두어 눌리지 않음을 보인다 |

> 층 사이 명도차는 layer-0 대 layer-1이 1.13, layer-1 대 layer-2가 1.16

---

## 2. Border & Stroke

- color.border.default(기본 테두리) : #333D5B
- color.border.subtle(구분선) : #262E47
- color.border.strong(진한 테두리) : #4B577A
- color.border.focus(포커스 상태일 때의 테두리) : #FFE500

| 구분 | 라이트 | 다크 | 비고 |
| --- | --- | --- | --- |
| default | #E6DECC | #333D5B | |
| subtle | #F0EADD | #262E47 | |
| strong | #D1C4A9 | #4B577A | |
| focus | #FFE500 | #FFE500 | 다크에서는 정식 값을 그대로 쓴다 |

---

## 3. Interaction & State

- color.interaction.hover(마우스 올렸을 때) : #FFD166
- color.interaction.active(클릭/눌린 상태) : #FEE140

---

## 4. Text

- color.text.primary(가장 기본적인 텍스트 색상) : #F5F1E3
- color.text.second(조금 옅은 텍스트 색상) : #B6BED2
- color.text.tertiary(가장 옅은 텍스트 색상) : #8892A8
- color.text.disabled(비활성화된 텍스트 색상) : #5A6480

| 구분 | 라이트 | 다크 | 비고 |
| --- | --- | --- | --- |
| primary | #111827 | #F5F1E3 | 순백 대신 따뜻한 흰색. 크림 계열을 이어받는다 |
| second | #4B5563 | #B6BED2 | |
| tertiary | #9CA3AF | #8892A8 | layer-2 위에서는 4.49:1이라 본문에 쓰지 않는다 |
| disabled | #D1D5DB | #5A6480 | 대비를 일부러 낮춘 값이다 |

---

## 5. Status

- color.status.error(에러, 연체) : #E8564E
- color.status.warning(경고, 연체) : #FFD166
- color.status.success(성공, 대여) : #00FD8F
- color.status.info(정보, 운영진) : #8FD3EC

> 어두운 화면에서 지나치게 튀는 경우가 있어 info와 success를 배경으로 쓸 때는 알파를 .18까지 낮춤

---

## 6. 다크 전용 역할 토큰

| 토큰 | 값 | 역할 | 비고 |
| --- | --- | --- | --- |
| on-brand | #111827 | 노랑 표면 위의 글자·아이콘·테두리 | 노랑 위 대비 13.90:1 |
| stick-edge | rgba(245,241,227,.82) | 어두운 표면 위 스티커 테두리 | 카드 위에 얹으면 #CDC9BC |
| stick-shadow | #04070E | 하드 오프셋 섀도 | base보다 어두워 빛이 닿지 않은 구멍으로 읽힘 |

---

## 7. 대비 측정

| 구분 | layer-0 | layer-1 | layer-2 | 비고 |
| --- | --- | --- | --- | --- |
| text.primary | 16.20 | 14.34 | 12.41 | |
| text.second | 9.84 | 8.71 | 7.54 | |
| text.tertiary | 5.86 | 5.19 | 4.49 | layer-2에서 AA 미달 |
| status.error | 5.12 | 4.53 | 3.92 | 실제 사용은 layer-1까지 |
| status.info | 11.07 | 9.80 | 8.48 | |
| status.success | 13.47 | 11.93 | 10.32 | |
| surface.brand | 14.35 | 12.71 | 10.99 | |
