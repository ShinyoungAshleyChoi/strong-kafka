# HTTPS 설정 가이드

## 셀프사인드 인증서 생성

개발 환경에서 HTTPS를 사용하려면 다음 단계를 따르세요:

### 1. 인증서 생성

```bash
cd gateway
./generate_cert.sh
```

이 스크립트는 `certs/` 디렉토리에 다음 파일을 생성합니다:
- `cert.pem` - SSL 인증서
- `key.pem` - 개인 키

### 2. HTTPS 활성화

`.env` 파일에서 SSL 설정을 활성화:

```bash
SSL_ENABLED=true
SSL_CERTFILE=./certs/cert.pem
SSL_KEYFILE=./certs/key.pem
```

### 3. 서버 실행

```bash
python main.py
```

서버가 `https://localhost:3000`에서 실행됩니다.

## Docker에서 사용

Docker Compose를 사용하는 경우:

```yaml
services:
  gateway:
    environment:
      - SSL_ENABLED=true
      - SSL_CERTFILE=/app/certs/cert.pem
      - SSL_KEYFILE=/app/certs/key.pem
    volumes:
      - ./gateway/certs:/app/certs:ro
    ports:
      - "3000:3000"
```

## 주의사항

- 셀프사인드 인증서는 **개발 환경 전용**입니다
- 브라우저에서 보안 경고가 표시될 수 있습니다 (정상)
- 프로덕션 환경에서는 Let's Encrypt 등의 공인 인증서를 사용하세요
- 인증서는 365일 동안 유효합니다

## API 테스트

HTTPS로 API 테스트:

```bash
# curl 사용 (인증서 검증 무시)
curl -k https://localhost:3000/health

# httpx 사용 (Python)
import httpx
response = httpx.get('https://localhost:3000/health', verify=False)
```
