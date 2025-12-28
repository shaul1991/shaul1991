# 🏠 홈 서버 사이드 프로젝트

## 운영 중인 Self-Hosted 서비스

- **Caddy** - 리버스 프록시 / HTTPS 자동화
- **Immich** - 사진/동영상 백업 및 관리
- **MinIO** - S3 호환 오브젝트 스토리지
- **Jenkins** - CI/CD 자동화
- **Uptime Kuma** - 서비스 모니터링

## 인프라 구성도

```mermaid
graph TB
    subgraph Internet["🌐 인터넷"]
        ISP["인터넷 공급자<br/>(ISP)"]
        Client["외부 클라이언트<br/>(웹/모바일)"]
        Cloudflare["☁️ Cloudflare<br/>(DDNS)"]
    end

    subgraph HomeNetwork["🏠 홈 네트워크"]
        Modem["인터넷 모뎀<br/>(브릿지 모드)"]
        Router["메인 공유기<br/>(포트 포워딩)"]

        subgraph Server["🖥️ 홈 서버 (AMD Ryzen 5 2400G, 32GB RAM, RX 580 8GB)"]
            subgraph CaddyService["🐳 Caddy"]
                Caddy["리버스 프록시<br/>HTTPS/Let's Encrypt<br/>(docker-compose)"]
            end

            subgraph ImmichService["🐳 Immich"]
                Immich["사진/동영상 관리<br/>(docker-compose)"]
            end

            subgraph MinIOService["🐳 MinIO"]
                MinIO["오브젝트 스토리지<br/>(docker-compose)"]
            end

            subgraph JenkinsService["🐳 Jenkins"]
                Jenkins["CI/CD<br/>(docker-compose)"]
            end

            subgraph KumaService["🐳 Uptime Kuma"]
                Kuma["모니터링<br/>(docker-compose)"]
            end

            subgraph Storage["💾 스토리지"]
                NVMe["NVMe SSD"]
                SSD["SSD"]
                HDD["HDD"]
            end
        end
    end

    ISP -->|인터넷 회선| Modem
    Modem --> Router
    Router -->|유동 IP| Caddy
    Router -.->|IP 변경 감지| Cloudflare

    Client -->|DNS 조회| Cloudflare
    Cloudflare -.->|도메인 → IP| Client
    Client -->|HTTPS| Router

    Caddy -->|Proxy| Immich
    Caddy -->|Proxy| MinIO
    Caddy -->|Proxy| Jenkins
    Caddy -->|Proxy| Kuma

    Immich -.->|DB| NVMe
    Immich -.->|썸네일/캐시| SSD
    Immich -.->|원본 미디어| HDD
    MinIO -.->|저장소| HDD
    Jenkins -.->|빌드| NVMe

    style Server fill:#e1f5ff
    style CaddyService fill:#a8e6cf
    style ImmichService fill:#b2ebf2
    style MinIOService fill:#c5e1a5
    style JenkinsService fill:#ffcc80
    style KumaService fill:#ce93d8
    style Storage fill:#f0f0f0
    style Cloudflare fill:#f4a460
    style Router fill:#ffcccb
```

## 하드웨어 사양

### 💻 시스템 구성

- **CPU**: AMD Ryzen 5 2400G (8 threads) @ 3.600GHz
- **GPU**: AMD ATI Radeon RX 580 8GB OC
- **메모리**: 32GB RAM
- **OS**: Ubuntu 24.04.3 LTS x86_64
- **커널**: 6.8.0-90-generic

### 💾 스토리지 계층

- **NVMe SSD**: 시스템, 데이터베이스, 빌드 캐시
- **SSD**: Immich 썸네일 및 캐시
- **HDD**: Immich 원본 미디어, MinIO 오브젝트 스토리지

## 네트워크 구성

### 외부 접속 흐름

1. **ISP** → 인터넷 공급자로부터 회선 제공
2. **인터넷 모뎀** (브릿지 모드) → 단순 회선 연결
3. **메인 공유기** → 포트 포워딩 설정
4. **Cloudflare DDNS** → 유동 IP 변경 자동 감지 및 DNS 업데이트
5. **Caddy** → HTTPS 리버스 프록시, Let's Encrypt 자동 인증서
6. **Docker 서비스들** → 각 서비스별 독립적인 docker-compose 구성
