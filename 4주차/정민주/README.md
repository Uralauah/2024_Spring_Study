# 2024_Spring_Study

## 우선적으로 로그인, 회원가입 구현
다들 해봤으니 설명은 넘어감

## 파일 업로드 및 관리 애플리케이션
### 개요
이 애플리케이션은 사용자가 파일을 서버에 업로드하고, 업로드된 파일을 관리(조회, 다운로드, 삭제)할 수 있는 기능을 제공합니다. 각 사용자는 자신의 파일을 관리할 수 있으며, 파일의 메타데이터는 데이터베이스에 저장됩니다.


## 1. 데이터베이스 설계
### User 테이블
설명: 애플리케이션 사용자 정보를 저장하는 테이블입니다. 

컬럼 정의:

* user_id: Integer (사용자의 고유 ID)

* username: 
Varchar(255), 사용자 이름

* email: 
Varchar(255), 사용자 이메일 주소

* password: 
Varchar(255), 사용자 비밀번호


### FileMetaData 테이블
설명: 업로드된 파일의 메타데이터를 저장하는 테이블입니다.

컬럼 정의:


* id: Integer, Primary Key, Auto Increment, 파일의 고유 ID
* file_name: Varchar(255), 업로드된 파일의 원본 이름
* file_path: Varchar(255), 파일이 저장된 url(s3에서 반환 예정)
* file_size: Long, 파일의 크기 (바이트 단위)
* upload_time: Timestamp, 파일이 업로드된 시간 (기본값: 현재 시간)
* content_type: Varchar(100), 파일의 MIME 타입
* user_id: Integer, Foreign Key, 파일을 업로드한 사용자의 ID (참조: User.user_id)


## 2. API 정의
### 2-1. 파일 업로드 API
* Endpoint: POST /api/files/upload
* 설명: 사용자가 파일을 업로드하면 서버에 저장되고, 파일의 메타데이터가 데이터베이스에 저장됩니다. 파일은 업로드한 사용자와 연관됩니다.
* Request Body:
multipart/form-data 형식으로 파일을 업로드
업로드하는 사용자의 정보
* Response:
Status: 
  * 200 OK (업로드 성공 시)
  *  Error: 400 Bad Request (파일이 없거나 잘못된 요청)

### 2-2. 파일 목록 조회 API
* Endpoint: GET /api/files/{id}
설명: 서버에 본인이 업로드 한 파일의 목록을 조회합니다. 
id: 사용자의 고유 id를 의미합니다.
* Response:
Status: 200 OK (정상 처리 시)

### 2-3. 파일 다운로드 API
* Endpoint: GET /api/files/{id}/download
* 설명: 지정된 ID의 파일을 다운로드합니다. 
* Path Variable:
id: 파일의 고유 ID
* Response:
Status:
  *  200 OK (정상 처리 시)
  * Error: 404 Not Found (지정된 ID의 파일이 존재하지 않는 경우)

### 2-4. 파일 삭제 API
* Endpoint: DELETE /api/files/{id}
* 설명: 지정된 ID의 파일을 서버와 데이터베이스에서 삭제합니다.
* Path Variable:
id: 파일의 고유 ID
* Response: Status:
  *  200 OK (삭제 성공 시)
   * Error: 404 Not Found (지정된 ID의 파일이 존재하지 않는 경우)

## 3. 주요 기능 설명
### 사용자 관리:

각 사용자는 고유한 계정(ID, 이메일, 비밀번호)을 가지고 있으며, 파일을 업로드하고 관리할 수 있습니다.

### 파일 업로드:

사용자가 파일을 업로드할 수 있으며, 서버는 해당 파일을 지정된 디렉토리에 저장합니다.
파일의 메타데이터(파일 이름, 경로, 크기, MIME 타입, 업로드한 사용자 등)가 데이터베이스에 저장됩니다.

### 파일 목록 조회:

사용자는 자신의 업로드된 파일들의 목록을 조회할 수 있습니다. 이 목록에는 파일의 주요 정보(이름, 업로드 시간 등)가 포함됩니다.

### 파일 다운로드:

사용자는 자신이 업로드한 파일을 선택하여 다운로드할 수 있습니다. 파일은 원본 이름과 함께 다운로드됩니다.

### 파일 삭제:

사용자는 자신이 업로드한 파일을 삭제할 수 있으며, 삭제 시 서버에서 파일과 관련된 메타데이터가 모두 제거됩니다.

----

## 조건
- s3를 이용한 파일 crud를 진행할 것.
- 예외처리는 본인 재량껏 처리
