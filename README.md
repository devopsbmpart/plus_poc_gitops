# plus_poc_gitops — 배포 매니페스트

DevOps 플랫폼 연동 PoC의 **배포 소스 오브 트루스**.
Argo CD 가 이 저장소를 바라보고 클러스터 상태를 맞춘다.

## 구조

```
apps/
  plus-poc-app-stg/     검증 환경 (test-dev, NodePort 30049, replicas 1)
  plus-poc-app-prd/     운영 환경 (test-prd, NodePort 30050, replicas 2)
argocd/
  application-stg.yaml
  application-prd.yaml
```

환경별로 디렉터리와 Application 을 나눈다. STG 배포와 PRD 배포가
서로를 건드리지 않아야 하기 때문이다.

## 수동 게이트

**`syncPolicy.automated` 를 두지 않는다.** 의도적이다.

이 플랫폼은 각 단계를 사용자가 명시적으로 승인하는 방식이다.

```
빌드      [버튼]
GitOps    [버튼]  ← 여기서 커밋. Argo CD 는 OutOfSync 로 대기
Sync      [버튼]  ← 여기서 실제 배포
```

automated 를 켜면 커밋하는 즉시 반영돼 승인 단계가 사라진다.
수동 sync 는 Argo CD 가 정식 지원하는 모드이고, 승인 게이트가 필요한
환경에서는 표준적인 구성이다.

## 이미지 고정

**tag 가 아니라 digest 로 고정한다.** tag 는 같은 이름이 다른 내용을
가리킬 수 있어 "지금 무엇이 배포됐는가"를 확정할 수 없다.

`DIGEST_PLACEHOLDER` 와 `PLACEHOLDER` 는 플랫폼이 GitOps 푸시 단계에서
실제 digest 와 Jira Issue Key 로 치환한다.

## 커밋 규약

```
chore(deploy): plus-poc-app-stg 이미지 갱신

Jira-Issue: TPS-2473
```

## 적용

```bash
kubectl apply -f argocd/application-stg.yaml
kubectl apply -f argocd/application-prd.yaml
```
