# gitops-sample-app

GitOps CI/CD 파이프라인 실습 프로젝트의 **애플리케이션 레포**입니다.
간단한 정적 웹 페이지(Nginx)를 컨테이너로 빌드하고, GitHub Actions로
이미지 빌드·푸시를 자동화합니다.

배포는 별도의 매니페스트 레포([gitops-sample-manifest](https://github.com/gyu2001/gitops-sample-manifest))와
ArgoCD가 담당합니다. (앱 레포와 배포 레포를 분리하는 GitOps 표준 구조)

---

## 전체 아키텍처

```
[개발자] --- git push ---> [gitops-sample-app] (이 레포)
                                  |
                                  | (GitHub Actions - CI)
                                  v
                     Docker 이미지 빌드 → GHCR에 push
                                  |
                                  v
              [gitops-sample-manifest] (Helm 차트)
                                  |
                                  | (ArgoCD가 Git 변경 감지 - CD)
                                  v
                     kubeadm 클러스터(EC2)에 자동 배포
```

---

## CI 파이프라인 (GitHub Actions)

`main` 브랜치에 push하면 `.github/workflows/build.yml`이 자동 실행됩니다.

1. 소스 체크아웃
2. GHCR(GitHub Container Registry) 로그인
3. Dockerfile로 이미지 빌드
4. `ghcr.io/gyu2001/gitops-sample-app:<커밋SHA>` 태그로 push

커밋마다 고유한 SHA를 이미지 태그로 사용해, 어떤 커밋이 어떤 이미지로
배포됐는지 추적 가능하도록 구성했습니다.

## 구성 파일

| 파일 | 역할 |
|---|---|
| `index.html` | 배포할 정적 웹 페이지 (버전 표시) |
| `Dockerfile` | Nginx 기반 컨테이너 이미지 정의 |
| `.github/workflows/build.yml` | 이미지 빌드·푸시 CI 워크플로우 |

## 사용 기술

`GitHub Actions` `Docker` `GHCR` `Nginx`

---

## 관련 레포

- 배포 매니페스트: [gitops-sample-manifest](https://github.com/gyu2001/gitops-sample-manifest)
