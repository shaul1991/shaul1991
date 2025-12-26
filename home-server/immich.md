# Immich 설치 계획

## 개요

Immich는 셀프 호스팅 사진/동영상 백업 솔루션입니다. Google Photos와 유사한 기능을 제공하며, 머신러닝 기반의 얼굴 인식, 객체 탐지 등을 지원합니다.

- **공식 문서**: https://docs.immich.app/
- **설치 방식**: Docker Compose (권장)
- **설치 경로**: `/opt/immich/`
- **설치 버전**: v2.3.1
- **내부 접속**: `http://<서버IP>:2283`
- **외부 접속**: `https://immich.shaul.link`

---

## 1. 시스템 요구사항 검토

### Immich 권장 사양

| 항목 | 최소 | 권장 | 현재 서버 | 상태 |
|------|------|------|-----------|------|
| RAM | 4GB | 6GB | 32GB | ✅ 충족 |
| CPU | 2코어 | 4코어 | 4코어/8스레드 | ✅ 충족 |
| 파일시스템 | EXT4, ZFS 등 | EXT4, ZFS 등 | EXT4 | ✅ 충족 |
| Docker | 필수 | 필수 | 설치됨 | ✅ 충족 |

---

## 2. 스토리지 구성

### 2.1 스토리지 할당

| 용도 | 장치 | 마운트 위치 | 용량 |
|------|------|-------------|------|
| 원본 미디어 (upload/library) | HDD (sdc1) | `/mnt/storage/immich` | 4TB |
| 썸네일/캐시 | SSD (sda1) | `/mnt/ssd-cache/immich` | 250GB |
| PostgreSQL DB | NVMe SSD | `/opt/immich/postgres` | - |

### 2.2 마운트 구조

```
/mnt/
├── disk1/                    # HDD 물리 마운트 (4TB)
│   └── immich/               # Immich 데이터
│       ├── upload/           # 업로드 원본
│       └── library/          # 정리된 라이브러리
├── storage -> disk1          # 심볼릭 링크 (확장 대비)
└── ssd-cache/                # SSD 캐시 마운트 (250GB)
    └── immich/
        ├── thumbs/           # 썸네일
        ├── encoded-video/    # 인코딩된 비디오
        └── profile/          # 프로필 이미지
```

### 2.3 디스크 UUID

| 장치 | UUID | 라벨 |
|------|------|------|
| sdc1 (HDD) | `e5c64d9d-63e6-4396-806a-6436c4070a64` | `immich-disk1` |
| sda1 (SSD) | `230b8598-5f19-4db0-b5cf-f6695905bb9c` | `immich-cache` |

---

## 3. 디렉토리 구조

### 설치 구조

```
/opt/immich/                     # Immich 설치 루트 (NVMe SSD)
├── docker-compose.yml           # Docker Compose 설정 파일
├── .env                         # 환경 변수 파일
└── postgres/                    # PostgreSQL 데이터
```

---

## 4. Docker 설정 (완료)

### 4.1 환경 변수 (.env)

```env
# 미디어 저장 위치 (HDD - 원본)
UPLOAD_LOCATION=/mnt/storage/immich

# DB 위치 (NVMe SSD)
DB_DATA_LOCATION=/opt/immich/postgres

# 타임존
TZ=Asia/Seoul

# Immich 버전
IMMICH_VERSION=v2

# PostgreSQL 설정
DB_PASSWORD=<비밀번호>
DB_USERNAME=postgres
DB_DATABASE_NAME=immich
```

### 4.2 docker-compose.yml 볼륨 설정

```yaml
immich-server:
  volumes:
    - ${UPLOAD_LOCATION}:/data                              # HDD: 원본
    - /mnt/ssd-cache/immich/thumbs:/data/thumbs             # SSD: 썸네일
    - /mnt/ssd-cache/immich/encoded-video:/data/encoded-video  # SSD: 인코딩
    - /mnt/ssd-cache/immich/profile:/data/profile           # SSD: 프로필
    - /etc/localtime:/etc/localtime:ro
```

### 4.3 Immich 폴더 검증 파일

각 디렉토리에 `.immich` 마커 파일 필요:

```bash
touch /mnt/storage/immich/upload/.immich
touch /mnt/storage/immich/library/.immich
touch /mnt/ssd-cache/immich/thumbs/.immich
touch /mnt/ssd-cache/immich/encoded-video/.immich
touch /mnt/ssd-cache/immich/profile/.immich
```

---

## 5. fstab 설정 (완료)

```bash
# /etc/fstab
UUID=e5c64d9d-63e6-4396-806a-6436c4070a64 /mnt/disk1 ext4 defaults,nofail,x-systemd.device-timeout=60 0 2
UUID=230b8598-5f19-4db0-b5cf-f6695905bb9c /mnt/ssd-cache ext4 defaults 0 2
```

---

## 6. USB 외장 HDD 설정

### 6.1 USB 절전 비활성화

```bash
# 즉시 적용
echo -1 | sudo tee /sys/module/usbcore/parameters/autosuspend

# 영구 적용 (GRUB)
sudo sed -i 's/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.autosuspend=-1"/' /etc/default/grub
sudo update-grub
```

### 6.2 외장 HDD 주의사항

| 항목 | 대응 |
|------|------|
| 스핀업 지연 | fstab `device-timeout=60` 설정 |
| 장치명 변경 | UUID로 마운트 |
| 연결 끊김 | `nofail` 옵션으로 부팅 보장 |
| USB 절전 | autosuspend 비활성화 |

---

## 7. 스토리지 확장 계획

### mergerfs를 이용한 확장 (향후)

```bash
# mergerfs 설치
sudo apt install mergerfs

# 디스크 추가 시 fstab 수정
/mnt/disk1:/mnt/disk2 /mnt/storage fuse.mergerfs defaults,allow_other,use_ino,category.create=mfs 0 0
```

---

## 8. 백업 정책

### 8.1 3-2-1 백업 원칙

- **3**: 데이터 3개 복사본 유지
- **2**: 2개의 다른 미디어 타입에 저장
- **1**: 1개는 오프사이트(외부) 보관

### 8.2 백업 대상

| 우선순위 | 대상 | 위치 | 백업 주기 |
|----------|------|------|-----------|
| 🔴 Critical | PostgreSQL DB | `/opt/immich/postgres/` | 매일 |
| 🔴 Critical | .env 설정 | `/opt/immich/.env` | 변경 시 |
| 🟡 High | 원본 미디어 | `/mnt/storage/immich/` | 주간 |
| 🟢 Low | 썸네일/캐시 | `/mnt/ssd-cache/immich/` | 불필요 (재생성 가능) |

### 8.3 백업 스크립트

```bash
#!/bin/bash
# /opt/immich/backup.sh

BACKUP_DIR="/mnt/backup/immich"
DATE=$(date +%Y%m%d_%H%M%S)

# PostgreSQL 백업
docker exec immich_postgres pg_dumpall -U postgres > "$BACKUP_DIR/db_backup_$DATE.sql"

# 설정 파일 백업
cp /opt/immich/.env "$BACKUP_DIR/env_backup_$DATE"

# 오래된 백업 삭제 (30일)
find "$BACKUP_DIR" -name "db_backup_*" -mtime +30 -delete

echo "Backup completed: $DATE"
```

---

## 9. 설치 체크리스트

### 완료된 항목

- [x] Docker 설치 확인
- [x] HDD 파티셔닝 (4TB) 및 마운트
- [x] SSD 캐시 파티셔닝 (250GB) 및 마운트
- [x] fstab 설정
- [x] 심볼릭 링크 생성 (/mnt/storage)
- [x] Immich 디렉토리 생성
- [x] docker-compose.yml 설정
- [x] .env 파일 설정
- [x] Docker 컨테이너 실행

### 진행 필요

- [x] Immich 정상 작동 확인
- [x] http://<서버IP>:2283 접속
- [x] 관리자 계정 생성
- [x] 역방향 프록시 설정 (Caddy)
- [x] HTTPS 외부 접속 설정 (immich.shaul.link)
- [ ] 모바일 앱 연결
- [ ] 백업 스크립트 설정

---

## 10. 보안 고려사항

### 네트워크 보안

- [x] 역방향 프록시 설정 완료 (Caddy)
- [x] HTTPS 활성화 (Let's Encrypt 자동 인증서)
- [x] 외부 접속 주소: `https://immich.shaul.link`

### 접근 제어

- 강력한 DB 비밀번호 설정
- 관리자 계정 비밀번호 강화

> 📌 네트워크 상세 구성은 [NETWORK_SETUP.md](./NETWORK_SETUP.md) 참조

---

## 11. 향후 고려사항

### 하드웨어 가속

- AMD RX580 GPU로 트랜스코딩 가속 가능
- Docker GPU 패스스루 설정 필요

### 스토리지 확장

- HDD 미할당 4TB 추가 파티션 생성
- 또는 새 HDD 추가 후 mergerfs 통합

---

## 참고 자료

- [Immich 공식 문서 - Quick Start](https://docs.immich.app/overview/quick-start/)
- [Immich 요구사항](https://docs.immich.app/install/requirements/)
- [Immich Docker Compose 설치](https://docs.immich.app/install/docker-compose/)

---

*최종 수정일: 2025-11-28*
