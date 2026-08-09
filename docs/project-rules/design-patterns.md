# 디자인 패턴

---

## 1. 생성 패턴

### 가. 키워드 인자와 기본값

1) 선택적 필드가 많을 때 적용  
2) 인자 순서에 의존하지 않음  
3) Python은 별도 빌더가 필요하지 않음  

```python
# 좋은 예
member = Member.objects.create(
    email="hong@example.com",
    student_id="20250101",
    name="홍길동",
    status=MemberStatus.PENDING,
)

# 나쁜 예 (인자 순서에 의존)
member = Member("hong@example.com", "20250101", "홍길동", 1, None)
```

### 나. Factory 패턴

1) 생성 로직 은닉  
2) 타입별 분기 처리  

```python
# 알림 종류에 따라 생성
notification = NotificationFactory.create(notification_type, message)
```

### 다. 전역 객체 주의사항

1) 싱글턴을 직접 구현하지 않음  
2) 모듈 수준 객체 또는 Django 설정에 위임  
3) 무상태 유지  
가) 상태를 담으면 요청 간에 값이 섞임  

---

## 2. 구조 패턴

### 가. Manager·QuerySet 패턴

1) 데이터 접근을 모델에 모음  
2) 쿼리 이름으로 의도를 드러냄  
3) 뷰와 서비스에서 쿼리 조건을 반복하지 않음  

```python
class RentalQuerySet(models.QuerySet):
    def active(self):
        return self.filter(returned_at__isnull=True)

    def overdue(self, today):
        return self.active().filter(due_date__lt=today)


class Rental(models.Model):
    objects = RentalQuerySet.as_manager()
```

### 나. Serializer 패턴

1) 계층 간 데이터 전달  
2) Model 직접 노출 금지  
3) 요청과 응답 분리  

| 클래스 | 방향 | 역할 |
| --- | --- | --- |
| RentalCreateRequest | 클라이언트 → 서버 | 대여 요청 데이터 |
| RentalResponse | 서버 → 클라이언트 | 응답 데이터 |

### 다. Service 패턴

1) 여러 모델을 다루는 로직을 모음  
2) 뷰에 단순한 호출부만 남김  
3) 트랜잭션 경계를 서비스에 둠  

```python
@transaction.atomic
def rent_book(member, item):
    _validate_rentable(member, item)
    ...
```

---

## 3. 행동 패턴

### 가. Strategy 패턴

1) 알고리즘 교체 가능 구조  
2) 조건 분기 최소화  
3) 공통 인터페이스로 전략 정의  

```python
# 알림 발송 수단 교체 예시
sender = NotificationSenderFactory.of(channel)
sender.send(member, message)
```

### 나. Signal 사용 기준

Django Signal은 호출 흐름을 감춥니다. 따라서 제한적으로 사용합니다.

1) 도메인 간 결합을 끊어야 할 때만 사용  
2) 업무 로직의 주 흐름에는 사용 금지  
3) 사용 시 등록 위치를 문서화  

### 다. Template Method 패턴

1) 공통 흐름은 상위 클래스에 정의  
2) 세부 처리는 하위 클래스에서 구현  

---

## 4. 예외 처리 패턴

### 가. 사용자 정의 예외

1) 공통 예외를 상속  
2) ErrorCode 열거형 활용  
3) 도메인별 예외 분리  

```python
class MemberNotFoundError(BusinessException):
    def __init__(self, member_id):
        super().__init__(ErrorCode.MEMBER_NOT_FOUND, f"member_id: {member_id}")
```

### 나. ErrorCode 정의

| 코드 | HTTP 상태 | 메시지 |
| --- | --- | --- |
| MEMBER_NOT_FOUND | 404 | 회원을 찾을 수 없습니다. |
| INVALID_INPUT | 400 | 잘못된 입력값입니다. |
| UNAUTHORIZED | 401 | 인증이 필요합니다. |
| FORBIDDEN | 403 | 접근 권한이 없습니다. |
| ALREADY_RENTED | 409 | 이미 대여 중인 도서입니다. |
| OVERDUE_EXISTS | 409 | 연체 도서가 있어 대여할 수 없습니다. |

### 다. 전역 예외 처리

1) 예외 처리기를 한 곳에 등록  
2) 공통 응답 형식 반환  
3) 전체 예외 일괄 처리  

---

## 5. 동시성 처리

같은 도서를 두 사람이 동시에 대여하면 기록이 두 건 생길 수 있습니다. 대여와 반납은 아래 규칙을 지킵니다.

### 가. 처리 규칙

1) 대여 처리를 하나의 트랜잭션으로 묶음  
2) 대상 품목 행을 잠근 뒤 상태를 확인  
3) 대여 중인 품목에 중복 이력이 생기지 않도록 제약 조건 적용  

### 나. 중복 요청 대응

1) 같은 라벨을 연속으로 읽어도 이력은 한 건만 생성  
2) 이미 처리된 요청은 처리 완료로 안내  

---

## 6. 금지 패턴

### 가. 안티패턴

| 안티패턴 | 문제점 | 대안 |
| --- | --- | --- |
| Fat View | 뷰에 업무 로직 집중 | Service로 이동 |
| N+1 쿼리 | 목록 조회 시 쿼리 폭증 | select_related·prefetch_related |
| Model 직접 응답 | 내부 구조 노출 | Serializer 사용 |
| Signal 남용 | 호출 흐름 추적 불가 | 명시적 호출 |
| God Object | 한 클래스가 과도한 역할 | 단일 책임 원칙 적용 |
| Magic Number | 의미 불명확 | 상수화 |
| 서비스에서 직접 HTTP 호출 | 계층 혼재 | 별도 클라이언트 분리 |

### 나. 대여 로직 특유의 주의점

1) 반납예정일을 클라이언트가 보낸 값으로 정하지 않습니다.  
2) 연체 여부를 화면에서 계산해 신뢰하지 않습니다.  
3) 대여 자격 검증을 건너뛰는 우회 경로를 만들지 않습니다.  
