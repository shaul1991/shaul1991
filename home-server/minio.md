# MinIO + Filestash - 오브젝트 스토리지 서비스

## 📋 개요

MinIO는 고성능 오브젝트 스토리지 서버로, Amazon S3 호환 API를 제공합니다. Filestash는 MinIO와 함께 사용할 수 있는 웹 기반 파일 관리자입니다.

## 🎯 주요 기능

### MinIO
- 🗄️ S3 호환 오브젝트 스토리지
- 🚀 고성능, 확장 가능한 아키텍처
- 🔐 암호화 및 접근 제어
- 📊 버킷 정책 및 라이프사이클 관리
- 🔄 버전 관리 및 복제

### Filestash
- 🌐 웹 기반 파일 브라우저
- 📁 여러 스토리지 백엔드 지원 (S3, FTP, SFTP 등)
- 🖼️ 미디어 파일 미리보기
- ✏️ 온라인 문서 편집
- 📱 반응형 웹 UI

## 🛠️ 기술 스택

### MinIO
- **언어**: Go
- **프로토콜**: S3 API
- **Storage**: 분산 파일 시스템
- **Container**: Docker

### Filestash
- **Backend**: Go
- **Frontend**: JavaScript
- **Container**: Docker

## 📦 설치 방법

### Docker Compose 설정

```yaml
version: '3.8'

services:
  minio:
    container_name: minio
    image: minio/minio:latest
    command: server /data --console-address ":9001"
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      MINIO_ROOT_USER: ${MINIO_ROOT_USER}
      MINIO_ROOT_PASSWORD: ${MINIO_ROOT_PASSWORD}
    volumes:
      - minio-data:/data
    restart: always

  filestash:
    container_name: filestash
    image: machines/filestash:latest
    ports:
      - "8334:8334"
    environment:
      APPLICATION_URL: ${FILESTASH_URL}
    volumes:
      - filestash-data:/app/data/state
    restart: always
    depends_on:
      - minio

volumes:
  minio-data:
  filestash-data:
```

### 환경 변수 설정

`.env` 파일 예시:

```env
# MinIO
MINIO_ROOT_USER=admin
MINIO_ROOT_PASSWORD=your-secure-password

# Filestash
FILESTASH_URL=http://localhost:8334
```

### 실행

```bash
docker-compose up -d
```

## 🔧 설정 및 구성

### MinIO 초기 설정

1. 웹 브라우저에서 `http://localhost:9001` 접속
2. 루트 사용자로 로그인
3. 버킷 생성
   - Buckets > Create Bucket
   - 버킷 이름 입력 및 설정
4. Access Key 생성
   - Identity > Users > Create User
   - Access Key와 Secret Key 발급

### Filestash 설정

1. 웹 브라우저에서 `http://localhost:8334` 접속
2. 관리자 설정 진행
3. S3 백엔드 연결
   - Backend: S3
   - Endpoint: `http://minio:9000`
   - Access Key ID: MinIO에서 생성한 Access Key
   - Secret Access Key: MinIO에서 생성한 Secret Key
   - Region: `us-east-1` (기본값)

### 버킷 정책 설정 예시

공개 읽기 전용 정책:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {"AWS": "*"},
      "Action": ["s3:GetObject"],
      "Resource": ["arn:aws:s3:::your-bucket-name/*"]
    }
  ]
}
```

## 🔐 보안 설정

### HTTPS 설정 (권장)

Nginx 또는 Traefik을 사용한 리버스 프록시 설정:

```nginx
server {
    listen 443 ssl;
    server_name minio.yourdomain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    location / {
        proxy_pass http://localhost:9000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## 📊 운영 현황

- **상태**: 🟢 운영 중
- **도입 시기**: [날짜 입력]
- **버킷 수**: [숫자 입력]
- **총 저장 용량**: [용량 입력]
- **주요 용도**: [용도 설명]

## 💡 사용 사례

- 정적 파일 호스팅 (이미지, CSS, JS)
- 백업 스토리지
- 애플리케이션 데이터 저장
- 미디어 파일 저장소
- CI/CD 아티팩트 저장

## 🔗 참고 링크

### MinIO
- 공식 사이트: https://min.io
- 공식 문서: https://docs.min.io
- GitHub: https://github.com/minio/minio

### Filestash
- 공식 사이트: https://www.filestash.app
- 공식 문서: https://www.filestash.app/docs
- GitHub: https://github.com/mickael-kerjean/filestash

## 📝 참고 사항

- MinIO는 최소 4개의 드라이브를 권장 (Erasure Coding)
- 정기적인 데이터 백업 수행
- Access Key는 안전하게 보관
- 프로덕션 환경에서는 HTTPS 사용 필수
