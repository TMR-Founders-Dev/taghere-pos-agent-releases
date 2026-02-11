# TagHere POS Agent - Releases

TagHere POS Agent 자동 업데이트 배포용 저장소입니다.

이 저장소는 소스 코드를 포함하지 않으며, [GitHub Releases](https://github.com/TMR-Founders-Dev/taghere-pos-agent-releases/releases)를 통해 Velopack 업데이트 패키지(.nupkg)만 배포합니다.

## 배포 방법

### 1. 빌드

```powershell
# taghere-pos-agent 프로젝트에서
.\build-installer.ps1 -Version "1.0.1"
```

### 2. Release 생성

```bash
# GitHub CLI로 릴리스 생성 + nupkg 업로드
gh release create v1.0.1 \
  releases/TagherePosAgent-1.0.1-full.nupkg \
  releases/RELEASES \
  --repo TMR-Founders-Dev/taghere-pos-agent-releases \
  --title "v1.0.1" \
  --notes "변경 사항 작성"
```

### 3. 고객 측 자동 적용

설치된 에이전트가 앱 시작 시 자동으로 이 저장소의 최신 Release를 확인하고, 새 버전이 있으면 백그라운드에서 다운로드하여 다음 실행 시 적용합니다.
