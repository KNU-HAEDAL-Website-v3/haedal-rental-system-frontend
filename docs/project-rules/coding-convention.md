# 코딩 컨벤션

백엔드는 Python, 프론트엔드는 TypeScript를 사용합니다. 두 언어의 관례가 다르므로 구분하여 적용합니다.

---

## 1. 명명 규칙

### 가. Python 표기법

| 구분 | 표기법 | 예시 |
| --- | --- | --- |
| 클래스 | PascalCase | Member, RentalService |
| 함수·메서드 | snake_case | get_member(), create_rental() |
| 변수 | snake_case | member_name, due_date |
| 상수 | UPPER_SNAKE_CASE | MAX_RENTAL_COUNT, DEFAULT_RENTAL_DAYS |
| 모듈·패키지 | snake_case | rental_service, book_label |
| DB 컬럼 | snake_case | student_id, created_at |

### 나. TypeScript 표기법

| 구분 | 표기법 | 예시 |
| --- | --- | --- |
| 컴포넌트 | PascalCase | BookList, RentalForm |
| 타입·인터페이스 | PascalCase | Book, RentalResponse |
| 함수·변수 | camelCase | fetchBooks(), dueDate |
| 상수 | UPPER_SNAKE_CASE | MAX_RENTAL_COUNT |
| 컴포넌트 파일 | PascalCase | BookList.tsx |
| 그 외 파일 | camelCase | rentalApi.ts |

### 다. 명명 원칙

1) 의미 있는 이름 사용  
가) data, info, temp 사용 금지  
나) get_member_list() 형태 권장  

2) 약어 최소화  
가) usr → user  
나) 잘 알려진 약어 허용  
다) URL, HTTP, ISBN 등 허용  

3) 불리언 접두사  
가) Python: is_active, has_permission  
나) TypeScript: isActive, hasPermission  

4) 용어 통일  
가) 요구사항 정의서의 용어를 그대로 사용  
나) 품목·도서·대여이력·라벨코드  

---

## 2. 코드 구조 규칙

### 가. 클래스 구조 순서

1) 클래스 변수·상수  
2) 필드 정의  
3) Meta 클래스  
4) 매직 메서드  
5) public 메서드  
6) private 메서드  

### 나. 함수 규칙

1) 단일 책임 원칙  
2) 50줄 이내  
3) 인자 3개 이하  
가) 초과 시 객체나 데이터 클래스로 묶기  

### 다. 들여쓰기·공백 규칙

| 구분 | Python | TypeScript |
| --- | --- | --- |
| 들여쓰기 | 4칸 | 2칸 |
| 탭 사용 | 금지 | 금지 |
| 줄 길이 | 88자 이내 | 100자 이내 |
| 연산자 앞뒤 공백 | 적용 | 적용 |

---

## 3. 주석 규칙

### 가. 주석 작성 원칙

1) 코드 자체 설명 우선  
2) 주석 의존 지양  
3) '무엇'보다 '왜' 기술  

### 나. Python docstring 규칙

1) 공개 함수와 클래스에 작성  
2) Args, Returns 포함  
3) Raises 포함  

```python
def get_member(member_id: int) -> MemberResponse:
    """회원 ID로 회원을 조회한다.

    Args:
        member_id: 조회할 회원의 ID

    Returns:
        회원 정보 응답 객체

    Raises:
        MemberNotFoundError: 회원이 없을 때
    """
```

### 다. TypeScript 주석 규칙

1) 공개 함수와 타입에 작성  
2) 복잡한 로직에만 인라인 주석 적용  
3) 코드 위에 위치  

### 라. 요구사항 연결

업무 규칙을 구현한 곳에는 요구사항 ID를 남깁니다. 나중에 규칙이 바뀔 때 찾기 쉬워집니다.

```python
# FR-RNT-005: 연체 도서가 있으면 신규 대여를 막는다
if rentals.overdue(today).exists():
    raise OverdueExistsError(member.id)
```

---

## 4. 린터·포맷터 설정

### 가. Python

1) Ruff  
가) 린트와 포맷 동시 적용  
나) 커밋 전 자동 검사  

2) 타입 힌트  
가) 공개 함수에 적용  
나) 정적 검사 도구는 선택 도입  

### 나. TypeScript

1) ESLint  
가) 권장 규칙 적용  
2) Prettier  
가) 저장 시 자동 포맷  

### 다. .editorconfig 공통 설정

```ini
root = true

[*]
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.py]
indent_style = space
indent_size = 4

[*.{ts,tsx,js,jsx,json}]
indent_style = space
indent_size = 2
```

---

## 5. 금지 사항

### 가. 공통 금지 사항

| 금지 항목 | 이유 | 대안 |
| --- | --- | --- |
| 매직 넘버 사용 | 의미 불명확 | 상수 정의 |
| 비밀 값 코드 포함 | 유출 위험 | 환경 변수 사용 |
| 예외 묵살 | 원인 추적 불가 | 로그 출력 또는 재처리 |
| 불필요한 주석 | 혼란 유발 | 삭제 |

### 나. Python 금지 사항

| 금지 항목 | 이유 | 대안 |
| --- | --- | --- |
| print 사용 | 운영 로그 혼재 | logging 사용 |
| except 빈 블록 | 예외 묵살 | 예외 종류 명시 후 처리 |
| 가변 기본 인자 | 호출 간 값 공유 | None 기본값 후 내부 생성 |
| Model 직접 응답 | 내부 구조 노출 | Serializer 사용 |

### 다. TypeScript 금지 사항

| 금지 항목 | 이유 | 대안 |
| --- | --- | --- |
| any 사용 | 타입 검사 무력화 | 구체 타입 또는 unknown |
| console.log 잔류 | 운영 환경 노출 | 제거 또는 로거 사용 |
| 화면 검증만으로 처리 | 우회 가능 | 서버 검증 병행 |
