# EXP-000: [제목 예시: Cilium vs Calico eBPF 네트워크 성능 및 리소스 오버헤드 비교]

- **연관 ADR**: `ADR-0001: K8s CNI 플러그인 선정`
- **관련 Issue**: `#12`
- **일자**: YYYY-MM-DD
- **테스트 환경**: On-Premise Bare-metal / Cloud (AWS EKS)
- **작성자**: @username

## 1. 실험 목적 및 가설 (Objective & Hypothesis)

* **목적**: 10Gbps 네트워크 환경에서 서비스 간 Latency를 최소화하고, 높은 동시 요청 처리 시 CPU 오버헤드가 적은 CNI를 검증한다.
* **가설**: eBPF 기반 바이패스 기술을 사용하는 Cilium이 Calico eBPF 모드 대비 p99 Latency를 15% 이상 단축할 것이다.

## 2. 테스트 환경 (Environment)

### 2.1 클러스터 인프라 사양

| 구분 | 스펙 / 구성 | 비고 |
| :--- | :--- | :--- |
| **K8s Version** | v1.30.2 | Containerd v1.7.x |
| **Control Plane** | 3 Nodes (4 vCPU, 8GB RAM, Ubuntu 22.04 LTS) | - |
| **Worker Nodes** | 5 Nodes (16 vCPU, 32GB RAM, Intel 10Gbps NIC) | MTU 1500 설정 |
| **테스트 대상 CNI** | Cilium v1.15.x vs Calico v3.27.x (eBPF 모드) | 기본 Config 유지 |

### 2.2 제약 및 변수 통제
* 테스트 중 타 Pod의 간섭을 최소화하기 위해 전용 노드 그룹(Taint/Toleration) 지정
* 백그라운드 크론탭 및 옵저버빌리티 에이전트 수집 주기 일시적 고정

## 3. 테스트 시나리오 및 측정 도구 (Scenario & Tools)

* **사용 도구**: `iperf3` (Throughput), `fortio` / `k6` (HTTP Latency/RPS), `Prometheus` (Resource Usage)
* **시나리오**:
  1. **Pod-to-Pod raw TCP 대역폭 측정**: 동일 노드 및 타 노드 간 60초간 대역폭 테스트
  2. **HTTP Stress Test**: 10,000 RPS 부하 조건에서 10분간 p95 / p99 Latency 및 패킷 드랍율 측정
  3. **DaemonSet 오버헤드 측정**: 부하 피크 시 CNI Agent의 CPU millicores 및 Memory(MiB) 사용량 측정

### 재현 명령어 (Reproduction Commands)
```bash
# 1. iperf3 부하 테스트 실행
kubectl apply -f ./manifests/iperf3-bench.yaml
kubectl exec -it iperf3-client -- iperf3 -c iperf3-server-pod-ip -t 60 -P 8

# 2. k6 HTTP Stress Test 실행
k6 run --vus 100 --duration 10m ./scripts/http-load-test.js
```

## 4. 결과 (Measured Data)

### 4.1 Pod to Pod 성능
[표 등으로 표현]

## 5. 결과 분석

### 5.1 장점
[ㅇㅇㅇ]

### 5.2 Trade-Off
[ㅇㅇㅇ]

## 6. 최종 결론
[ㅇㅇㅇ]

### 6.1 선정 결과
[ㅇㅇㅇ]

### 6.2 사유
[ㅇㅇㅇ]

### 6.3 후속 조치
[ㅇㅇㅇ]