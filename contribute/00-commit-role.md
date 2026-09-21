# commit role

## 1. 형식

```
[prefix] #이슈번호 핵심 변경사항 — 확인 필요: 확인할 내용

(선택) 본문
- 결정 사유, 세부 변경, 후속 작업
```

| 요소 | 규칙 |
|--|--|
| `[prefix]` | **필수.** 소문자 한 글자 |
| `#이슈번호` | 이슈에 딸린 작업이면 **필수.** prefix 바로 뒤. GitHub가 자동 링크합니다 |
| 제목 | **두괄식.** 무엇이 바뀌었는지부터. 한글 기준 **40자 안팎** |
| `— 확인 필요:` | 리뷰어가 봐야 할 것이 있을 때만. 없으면 생략하고, 억지로 만들지 않습니다 |
| 본문 | 제목에 다 안 들어가는 사유·세부 내용. 제목과 한 줄 띄우고 `-` 목록으로 |
| 언어 | 한국어 |

## 2. Prefix

| 표기 | 의미 | 예 |
|--|--|--|
| `[a]` | 추가 — 새 파일, 새 문서, 새 기능 | `[a] #3 OS Type 구성 ADR 작성` |
| `[e]` | 수정 — 기존 내용 변경, 상태 변경 | `[e] ADR Template 오탈자 수정` |
| `[d]` | 삭제 — 파일·내용 제거 | `[d] 빈 placeholder 문서 정리` |

**prefix는 하나만 씁니다.** 여러 종류가 섞이면 **비중이 가장 큰 쪽**을 prefix로 두고, 나머지는 쉼표로 이어 제목에 짧게 붙입니다.

```
✗  [a, e] README 오탈자 수정 및 decisions _TEMPLATE 추가
✓  [a] decisions _TEMPLATE 추가, README 오탈자 수정
```

**기본적으로 커밋을 나누지 않습니다.** 커밋이 잘게 쌓이는 것을 지양합니다.
예외는 아래 4절 — 실측 커밋 하나뿐입니다.

## 3. 제목 쓰는 법

**무엇이 바뀌었는지가 먼저, 이유는 본문으로.**

```
✗  [e] #3 OS Type 결정 완료 : Rocky Linux - 낮은 TCO(무료 OS) - SELinux, firewalld,
       eBPF, cgroup v2 모두 공식 지원 - 하이브리드 아키텍처 선정 시 OS 단일화로 ...

✓  [e] #3 ADR-001 accepted: Rocky Linux

   - 라이선스 비용 없음 — 디자인 정책 0번(비용 최소화) 충족
   - 온프렘·클라우드 OS 단일화로 Ansible 모듈 공통화
   - Immutable 미지원은 IaC 접근통제로 보완 (부작용 절 참조)
```

모호한 표현 대신 대상을 적습니다.

```
✗  [e] README 변경
✗  [e] modify filename
✓  [e] README 로드맵에 Phase 표 추가
✓  [e] experiments 파일명 EXP-NNN 형식으로 통일
```

## 4. 저장소 작업 흐름별 커밋

README의 작업 순서(이슈 → ADR → 실측 → platform → docs → 릴리스)를 따라 커밋도 이렇게 남깁니다.

### ADR

```
[a] #3 ADR-001 OS Type 결정 작성                   ← proposed 로 작성
[e] #3 ADR-001 대안에 Talos, Flatcar 추가
[e] #3 ADR-001 accepted: Rocky Linux                ← 상태 변경은 제목에 드러낸다
```

이전 결정을 바꿀 때는 **어느 ADR 을 무엇이 바꾸는지**를 제목에 씁니다.

```
[a] #15 ADR-007 Hosted Control Plane 도입: Kamaji
[e] #15 ADR-002 개정 — K8s 관리 클러스터로 역할 축소 (ADR-007)
```

### 실측 — ⚠ 유일한 커밋 분리 예외

실측은 **반드시 3번에 나눠 커밋합니다.**

```
① [a] #4 EXP-001 측정 계획: Immutable OS 별 R/W 구조      ← 가설·반증 조건. 측정 전
② [a] #4 EXP-001 원시 데이터 (2026-09-25)                ← runs/ 아래 원본
③ [e] #4 EXP-001 결과 — 가설 기각, ADR-001 근거 수준 상향  ← 해석·판정
```
 
가설과 틀린 정보가 있다면 숨기지 않습니다.

### platform · docs

```
[a] #8 platform/80-ops DR 구성 방향 정리
[e] #9 docs 온프레미스 구성도에 egress 노드 반영
```

### 릴리스

```
[a] v2026.09 릴리스 노트
```

태그는 커밋과 별도로 `git tag -a v2026.09` 로 답니다.

## 5. 확인 필요 쓰는 법

리뷰어(미래의 나 포함)가 따로 봐야 할 게 있을 때만 붙입니다.

```
[e] #12 Cilium kube-proxy replacement 활성화 — 확인 필요: k8sServiceHost 를 LB IP 로 고정함
[a] #15 kamaji-etcd values 추가 — 확인 필요: topologySpreadConstraints 기본값이 비어 있어 명시함
[d] RKE2 전용 verified_facts 행 제거 — 확인 필요: Phase 0 기록은 docs/history 로 이동
```

**검증 안 된 로직, 사이드 이펙트, 후속 작업**이 대상입니다. 확인할 게 없으면 쓰지 않습니다.

## 6. 기존 커밋과의 차이

규칙 정리 전 커밋에는 아래 형식이 섞여 있습니다. **다시 쓰지 않고 그대로 둡니다.** 이력은 고치지 않는 것이 원칙입니다.

| 기존 | 이후 |
|--|--|
| `[a, e]` 복수 prefix | 하나만. 나머지는 쉼표로 |
| `[i] init README` | `[a]` |
| `[e] 이미지 추가` | 추가는 `[a]` |
| 영문 제목 (`modify filename`) | 한국어 |
| 사유를 제목에 ` - ` 로 나열 | 본문 목록으로 |

## 7. 자동 검사 (선택)

`.githooks/commit-msg` 가 형식을 검사합니다.

```bash
git config core.hooksPath .githooks
```

- prefix 누락, 복수 prefix, 대문자 prefix → **거부**
- 제목 60자 초과 → 경고만
- 병합·되돌리기 커밋(`Merge`, `Revert`)은 검사하지 않음