# plus_poc_gitops — 배포 매니페스트

DevOps 플랫폼 연동 PoC의 **배포 소스 오브 트루스**.
Argo CD 가 이 저장소를 바라보고 클러스터 상태를 맞춘다.

## 이 저장소를 다루는 규칙

**플랫폼은 Argo CD 를 직접 sync 하지 않는다** (절대 규칙 3).
플랫폼이 하는 일은 여기에 커밋하는 것까지고, 나머지는 Argo CD 가 알아서 한다.
`POST /api/v1/applications/{name}/sync` 는 호출하지 않는다.

**이미지는 tag 가 아니라 digest 로 고정한다** (절대 규칙 4).
tag 는 같은 이름이 다른 내용을 가리킬 수 있어 "무엇이 배포됐는가"를
확정할 수 없다. `@sha256:...` 만 쓴다.

**커밋에는 상관관계 키를 남긴다.**

```
chore(deploy): plus-poc-app 이미지 갱신

Jira-Issue: TPS-456
```

## 구조

```
apps/plus-poc-app/
  deployment.yaml   ← S6 이 image digest 를 여기에 커밋한다
  service.yaml
```

## 현재 상태

`deployment.yaml` 의 image 는 **placeholder** 다.
아직 첫 빌드가 없어 실제 digest 가 존재하지 않는다.
S6 이 처음 동작하면 실제 digest 로 교체되고, 그때부터 Argo CD 가 배포할 수 있다.
