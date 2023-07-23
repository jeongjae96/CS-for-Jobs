# Database
- 특정한 규칙 혹은 규약에 의해 구조화된 데이터 모음
- 데이터베이스를 관리하는 통합 시스템을 DBMS라고 함
- 사전 정의된 쿼리 언어(query language)를 사용하여 아래와 같은 작업을 수행함
    - 삽입
    - 삭제
    - 수정
    - 조회
- 실시간 접근과 동시 공유가 가능함
    - 동시 공유?

# Entity
- 사람, 장소, 물건 등 여러 속성(attribute)들을 지닌 명사
    - 예시: *서비스 회원* entity
        - 이름
        - 아이디
        - 전화번호 등의 속성을 지님
- 서비스의 요구 사항에 맞춰 속성을 정의함
    - 예시: *주소* 속성이 필요 없는 서비스는 해당 속성을 정의하지 않음

## Attribute
- 서비스 요구사항을 기반으로 한 attribute들만 효율적으로 관리하는 게 중요

## Weak/Strong Entity
- 어떤 엔터티가 다른 엔터티에 의해 존재 여부가 결정된다면(종속성) weak entity
    - 방/건물: 방은 건물이 있어야만 존재할 수 있기 때문에 weak, 건물은 strong

# Relation
- DB에서 정보를 구분하여 저장하는 기본 단위
- Entity에 관한 데이터를 relation에 담아서 관리
- 엔터티가 DB에서 관리될 때 relation으로 변환됨

## Table and collection
- Entity를 DB의 타입에 따라 부르는 명칭들
- RDBMS: Table
- NoSQL: Collection

# MySQL and MongoDB
- RDBMS/NoSQL의 대표적인 DB들

## MySQL의 구조
- Record -> Table -> Database

## MongoDB의 구조
- Document -> Collection -> Database

# Domain
- Relation의 각 attribute가 가질 수 있는 값의 집합
- 예시: `gender` attribute의 domain = {`male`, `female`}

# Field and record
- Attribute들을 field로 표현
- Relation에 쌓이는 행 단위의 데이터를 record라 함
    - Record는 tuple이라고도 함

## Example
- `책`이라는 entity를 정의하고 이를 기반으로 relation(table)을 정의한다면?
- Attributes
    - title
    - price
    - isbn
    - author
    - created_at
    - ...
- 이제 각 attribute들에 맞는 **type**을 정의해야 함
- Type은 DB마다 다를 수 있고, 아래는 MySQL을 기준으로 함
    - id:               INT
    - title:            VARCHAR(255)
    - author_id:        INT
    - publishing_year:  VARCHAR(255)
    - genre:            VARCHAR(255)
    - created_at:       DATETIME
    - updated_at:       DATETIME
- 테이블 생성 코드 예시
    ```SQL
    CREATE TABLE book(
        id                  INT NOT NULL AUTO_INCREMENT,
        title               VARCHAR(255),
        author_id           INT,
        publishing_tear     VARCHAR(255),
        genre               VARCHAR(255),
        created_at          DATETIME,
        updated_at          DATETIME,
        PRIMARY KEY (id)
    )
    ```

# Field type
- Field's type(in MySQL)
- CHAR, DATE, DATETIME, TIMESTAMP, INT, BIGINT, ...

# Date types
## DATE
- 날짜 O, 시간 X
- 3bytes
- 1000-01-01 ~ 9999-12-31

## DATETIME
- 날짜 O, 시간 O
- 8bytes
- 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59

## TIMESTAMP
- 날짜 O, 시간 O
- 4bytes
- 1970-01-01 00:00:**01** ~ 2038-01-19 03:14:07

# Character types
- CHAR, VARCHAR는 모두 최대 글자수를 미리 지정해야 함

## CHAR
- 0 ~ 255bytes
- VARCHAR와의 차이점
    - 고정된 최대 글자수만큼의 byte를 차지
    - 최대 글자수보다 실제 입력된 글자수가 적더라도, 최대 글자수만큼의 바이트로 저장

## VARCHAR
- 0 ~ 65,535bytes
- 실제 입력된 데이터에 따라 용량을 가변하여 저장
- 예시: VARCHAR(10000)로 선언하고, 10byte만큼의 이메일 주소를 저장할 경우 아래만큼만 사용
    - 10byte
    - +\) 1byte(길이 기록용)
- 유동적인 길이를 가진 데이터에 활용하면 좋음
- MySQL이 VARCHAR 데이터를 검색할 때, 실제 데이터 길이만큼의 메모리를 할당해야 하는 작업이 필요하여 CHAR 타입보다 추가적인 step이 필요함
    - 성능에 중요한 영향을 끼치진 않는 마이너한 이슈
    - CHAR 타입은 공간을 효율적으로 사용할 수 있으므로, 정답은 없음

# Bigdata types
## TEXT
- 긴 문자열 저장에 사용
- 예시: 게시판의 본문(게시글들을 모아놓은 걸 말하는 건가...?)

## BLOB
- 이미지, 동영상 등 큰 데이터를 저장하는 용도
- 보통은 잘 사용하지 않고, 원격 저장소에 저장해놓은 파일 경로를 VARCHAR로 저장해놓음

# ENUM, SET
## ENUM
- 데이터를 미리 정의된 내용에 매핑하는 타입
- 아래와 같은 형태로 사용할 수 있음
    ```SQL
    CREATE TABLE shirts (
        name    VARCHAR(40),
        size    ENUM('x-small', 'small', 'medium', 'large', 'x-large')
    )
    ```
- 최대 원소 수는 65,535개
- 매핑되지 않는 값은 빈 문자열로 취급
- 요소 단일 선택만 가능
- ENUM 항목을 변경하려면 스키마를 변경해야 하고, 삭제된 항목의 값을 가지는 컬럼에 대한 처리도 필요하다는 단점이 있음
    - 스키마 변경?

## SET
- 요소 다중 선택 가능
- 비트 단위 연산 가능
- 최대 원소 수는 64개
- 어플리케이션(서비스)의 수정에 따라 DB의 ENUM, SET에서 정의한 목록을 수정해야 한다는 단점(이건 ENUM도 마찬가지!)

# 관계(Relation?)
- 여러 테이블간에는 관계가 정의되어 있음
- 그들을 `관계화살표`로 표현함

## 1:1 관계
- `유저` 테이블과 `유저_이메일` 테이블
- 유저당 유저 이메일은 한 개이므로, 1:1 관계

## 1:N 관계
- `쇼핑몰_유저` 테이블과 `상품` 테이블
- 쇼핑몰 이용자는 여러 상품을 장바구니에 담을 수 있으므로, 1:N 관계

## N:M 관계
- `학생` 테이블과 `강의` 테이블
- 한 학생은 여러 강의를 들을 수 있고,
- 한 강의는 여러 학생들을 수용할 수 있으므로, N:M 관계
- 보통 두 개의 테이블을 직접 연결하지 않고 1:N, 1:M의 관계를 갖는 테이블 두 개로 나눔[Junction table]
    - `학생_강의` 테이블까지 총 3개
    - 데이터 참조 무결성(integrity)를 위한 것
        - Data integrity: Lifecycle동안 data의 정확성과 일관성을 유지하고 보증하는 것

# Key
- 테이블간의 관계를 명확하게 하고,
- 테이블 자체의 인덱스를 위한 장치
- 포함 관계
    - PK, AK -> CK -> SK

## Primary key
- 유일성과 최소성을 만족하는 key
- 테이블에 중복값이 없고 고유해야 함
    - 중복값이 있는 경우 해당 필드는 PK가 될 수 없음
- 두 개 이상의 필드를 조합해 고유함을 충족시킬 수 있지만, 최소성을 만족하지 않으므로 PK가 될 수 없음
- Natural key와 artificial key로 나뉨

### Natural key
- 테이블의 여러 필드 중 중복값이 들어있는 필드들을 제외하면 남는 필드를 PK로 설정하는 방식
- 향후 가변성이 있음

### Artificial key
- 레코드에 대한 새로운 필드를 인위적으로 부여하는 방식
- ID 등이 예시
    - Oracle: SEQUENCE
    - MySQL: AUTO INCREMENT
        ```SQL
        CREATE TABLE persons (
            person_id       INT NOT NULL AUTO_INCREMENT,
            last_name       VARCHAR(255) NOT NULL,
            first_name      VARCHAR(255),
            age             INT,
            PRIMARY KEY     (person_id)
        )
        ```
- 자연키와 대비되는 불변성이 있음
    - 그래서 보통 PK는 인조키로 설정함

## Foreign key
- 다른 테이블의 PK를 변형없이 참조한 값
- Entity와의 관계를 식별하는 데 사용
- 중복값 허용

## Candidate key
- 최소성: 필드를 조합하지 않고 최소 필드만을 사용해 키를 형성할 수 있는 속성
- PK의 후보들
- 유일성또한 만족함

## Alternate key
- CK 중 PK가 되지 못한 key
- CK가 2개 이상일 경우, PK로 지정된 키를 제외한 나머지 CK들

## Super key
- 유일성: 중복값이 없는 속성
- 각 레코드를 유일하게 식별할 수 있는 `유일성`
