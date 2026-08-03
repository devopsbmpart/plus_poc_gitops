# Argo CD 구성

## 적용 방법 (S7 에서 수행)

로컬 클러스터에 Argo CD 를 올린 뒤 Application 을 등록한다.

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl apply -f argocd/application.yaml
```

이 저장소가 private 이므로 Argo CD 에 저장소 자격증명 등록이 필요하다.

```bash
argocd repo add https://github.com/devopsbmpart/plus_poc_gitops.git \
  --username <github-id> --password <PAT>
```

## 왜 sync 를 호출하지 않는가

`syncPolicy.automated` 를 켜두면 Argo CD 가 저장소 변경을 스스로 감지해 반영한다.
플랫폼이 `POST /applications/{name}/sync` 를 호출하면 배포 결정 권한이
Argo CD 에서 플랫폼으로 넘어와, Argo CD 가 배포 SoT 라는 전제가 깨진다.
플랫폼은 커밋까지만 하고 결과를 조회할 뿐이다.
