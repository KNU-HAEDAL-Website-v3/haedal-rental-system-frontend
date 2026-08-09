# 아키텍처

---

## 1. 계층 구조

### 가. 계층 구성

Django 기본 구조에 Service 계층을 더합니다. 대여 자격 확인처럼 여러 모델을 다루는 로직이 뷰에 몰리는 것을 막기 위함입니다.

1) Presentation 계층  
가) View  
나) HTTP 요청 수신  
다) 응답 형식 반환  

2) Serialization 계층  
가) Serializer  
나) 입력 검증  
다) 출력 형식 변환  

3) Business 계층  
가) Service  
나) 핵심 업무 로직 처리  
다) 트랜잭션 관리  

4) Persistence 계층  
가) Manager·QuerySet  
나) 데이터 접근  
다) 쿼리 실행  

5) Domain 계층  
가) Model  
나) 도메인 모델  
다) 업무 상태 보유  

### 나. 의존성 방향

1) 단방향 의존성 유지  
가) View → Service 참조  
나) Service → Manager·Model 참조  
다) 역방향 참조 금지  
라) 순환 참조 금지  

2) Model 계층 독립  
가) 상위 계층 참조 불가  
나) 모든 계층에서 참조 가능  

### 다. 계층별 책임

| 구분 | 위치 | 책임 | 금지 사항 |
| --- | --- | --- | --- |
| View | views.py | 요청 수신·응답 반환 | 업무 로직 포함 |
| Serializer | serializers.py | 검증·변환 | DB 조회 |
| Service | services.py | 업무 로직·트랜잭션 | HTTP 객체 참조 |
| Manager | models.py | 쿼리 정의 | 업무 판단 |
| Model | models.py | 상태 보유·상태 변경 | 상위 계층 참조 |

---

## 2. 프로젝트 구조

### 가. 도메인 기반 구성

1) 기능별 앱 분리  
가) members 앱  
나) books 앱  
다) rentals 앱  

2) 각 앱 내 구성  
가) views.py  
나) serializers.py  
다) services.py  
라) models.py  
마) urls.py  
바) tests/  

### 나. 공통 구성

1) config 패키지  
가) settings  
나) urls  

2) common 앱  
가) exceptions  
나) responses  
다) utils  

### 다. 설정 분리

1) 공통·개발·운영 설정을 파일로 나눕니다.  
2) 비밀 값은 코드에 넣지 않습니다.  
3) 환경 변수로 주입합니다.  

---

## 3. Serializer와 Model 분리

### 가. Model 규칙

1) DB 테이블에 대응합니다.  
2) 외부에 직접 노출하지 않습니다.  
3) 상태 변경 메서드를 포함합니다.  

### 나. Serializer 규칙

1) 요청: RequestSerializer  
2) 응답: ResponseSerializer  
3) 요청과 응답을 분리합니다.  
4) 필요한 필드만 노출합니다.  

| 구분 | 위치 | 역할 | 비고 |
| --- | --- | --- | --- |
| Model | Domain 계층 | DB 매핑 | 외부 노출 금지 |
| RequestSerializer | Serialization 계층 | 요청 수신 | 입력 검증 포함 |
| ResponseSerializer | Serialization 계층 | 응답 전달 | 필요 필드만 포함 |

### 다. 노출 금지 필드

회원 정보는 최소한만 내보냅니다.

1) 응답에 내부 식별자를 그대로 쓰지 않습니다.  
2) 다른 회원의 학번과 이메일을 노출하지 않습니다.  
3) 운영진 화면과 일반 화면의 응답을 구분합니다.  

---

## 4. 예외 처리 구조

### 가. 공통 예외 클래스

1) BusinessException을 정의합니다.  
2) ErrorCode를 열거형으로 작성합니다.  
3) 코드와 메시지를 포함합니다.  

### 나. 전역 예외 처리

1) 예외 처리기를 한 곳에 둡니다.  
2) 전체 예외를 일괄 처리합니다.  
3) 통일된 응답 형식을 반환합니다.  
4) 예상하지 못한 예외는 로그를 남기고 일반 메시지를 반환합니다.  

### 다. 응답 형식

```json
{
  "success": false,
  "errorCode": "MEMBER_NOT_FOUND",
  "message": "회원을 찾을 수 없습니다."
}
```

### 라. 오류 코드 예시

| 코드 | HTTP 상태 | 메시지 |
| --- | --- | --- |
| MEMBER_NOT_FOUND | 404 | 회원을 찾을 수 없습니다. |
| INVALID_INPUT | 400 | 잘못된 입력값입니다. |
| UNAUTHORIZED | 401 | 인증이 필요합니다. |
| FORBIDDEN | 403 | 접근 권한이 없습니다. |
| ALREADY_RENTED | 409 | 이미 대여 중인 도서입니다. |
| OVERDUE_EXISTS | 409 | 연체 도서가 있어 대여할 수 없습니다. |

---

## 5. API 설계 규칙

### 가. REST 원칙

1) 리소스 중심으로 URL을 설계합니다.  
2) HTTP 메서드를 구분합니다.  
가) GET: 조회  
나) POST: 생성  
다) PUT: 전체 수정  
라) PATCH: 부분 수정  
마) DELETE: 삭제  

### 나. URL 규칙

1) 소문자와 하이픈을 사용합니다.  
2) 복수형 명사를 사용합니다.  
3) 동사를 사용하지 않습니다.  
4) 끝에 슬래시를 붙이는 규칙을 프로젝트 전체에서 통일합니다.  

| 구분 | 좋은 예 | 나쁜 예 |
| --- | --- | --- |
| 리소스 URL | /members | /getMember |
| 단어 구분 | /rental-histories | /rentalHistories |
| 중첩 리소스 | /members/{id}/rentals | /getMemberRentals |

### 다. 상태 변경 요청

대여와 반납은 리소스 생성과 갱신으로 표현합니다.

| 구분 | 메서드·경로 | 설명 |
| --- | --- | --- |
| 대여 | POST /rentals | 대여 이력 생성 |
| 반납 | POST /rentals/{id}/return | 반납 처리 |
| 연장 | POST /rentals/{id}/extend | 연장 처리 |

---

## 6. 프론트엔드 구조

### 가. 폴더 구성

1) 기능 단위로 나눕니다.  
가) features/books  
나) features/rentals  
다) features/members  

2) 공통 요소를 분리합니다.  
가) components  
나) hooks  
다) api  
라) types  

### 나. 서버 통신 규칙

1) 통신 코드를 화면 컴포넌트에 직접 쓰지 않습니다.  
2) API 호출을 별도 모듈로 분리합니다.  
3) 응답 타입을 정의해 사용합니다.  

### 다. 검증 위치

1) 화면 검증은 편의를 위한 것입니다.  
2) 대여 자격 판단은 서버에서 수행합니다.  
3) 화면 검증만 믿고 서버 검증을 생략하지 않습니다.  
