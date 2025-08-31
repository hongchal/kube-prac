# Voting App Helm Chart

이 Helm 차트는 Kubernetes에서 voting application을 배포하기 위한 것입니다.

## 구성 요소

- **DB**: PostgreSQL 데이터베이스
- **Redis**: 캐시 및 메시지 큐
- **Vote**: 투표 애플리케이션 (NodePort: 30001)
- **Result**: 결과 표시 애플리케이션 (NodePort: 30002)
- **Worker**: 백그라운드 작업 처리

## 설치

### 기본 설치
```bash
helm install voting-app .
```

### 특정 네임스페이스에 설치
```bash
helm install voting-app . --namespace voting-app --create-namespace
```

### 커스텀 값으로 설치
```bash
helm install voting-app . -f custom-values.yaml
```

## 업그레이드

```bash
helm upgrade voting-app .
```

## 제거

```bash
helm uninstall voting-app
```

## 구성

### 주요 설정 옵션

| 매개변수 | 설명 | 기본값 |
|---------|------|--------|
| `db.enabled` | DB 배포 여부 | `true` |
| `redis.enabled` | Redis 배포 여부 | `true` |
| `vote.enabled` | Vote 앱 배포 여부 | `true` |
| `result.enabled` | Result 앱 배포 여부 | `true` |
| `worker.enabled` | Worker 배포 여부 | `true` |
| `vote.service.nodePort` | Vote 앱 NodePort | `30001` |
| `result.service.nodePort` | Result 앱 NodePort | `30002` |

### 이미지 설정

각 컴포넌트의 이미지를 변경할 수 있습니다:

```yaml
vote:
  image:
    repository: your-registry/vote-app
    tag: "v1.0.0"
```

### 리소스 설정

각 컴포넌트의 리소스 요청/제한을 설정할 수 있습니다:

```yaml
vote:
  resources:
    limits:
      cpu: 200m
      memory: 256Mi
    requests:
      cpu: 100m
      memory: 128Mi
```

### 영구 저장소 설정

DB와 Redis에 영구 저장소를 활성화할 수 있습니다:

```yaml
db:
  persistence:
    enabled: true
    size: 10Gi
    storageClass: "fast-ssd"

redis:
  persistence:
    enabled: true
    size: 5Gi
    storageClass: "fast-ssd"
```

## 접근 방법

설치 후 다음 명령어로 접근 정보를 확인할 수 있습니다:

```bash
helm get notes voting-app
```

### Vote 앱 접근
- NodePort: 30001
- URL: `http://<node-ip>:30001`

### Result 앱 접근
- NodePort: 30002
- URL: `http://<node-ip>:30002`

## 모니터링

### Pod 상태 확인
```bash
kubectl get pods -l app.kubernetes.io/instance=voting-app
```

### 서비스 확인
```bash
kubectl get svc -l app.kubernetes.io/instance=voting-app
```

### 로그 확인
```bash
kubectl logs -l app=vote
kubectl logs -l app=result
kubectl logs -l app=worker
```

## 문제 해결

### 일반적인 문제들

1. **이미지 풀 오류**: 프라이빗 레지스트리를 사용하는 경우 `imagePullSecrets`를 설정하세요.
2. **포트 충돌**: NodePort가 이미 사용 중인 경우 `values.yaml`에서 변경하세요.
3. **리소스 부족**: 클러스터 리소스가 부족한 경우 리소스 요청을 줄이세요.

### 디버깅

```bash
# Helm 릴리스 상태 확인
helm status voting-app

# 템플릿 렌더링 확인
helm template voting-app . --debug

# 차트 검증
helm lint .
```
