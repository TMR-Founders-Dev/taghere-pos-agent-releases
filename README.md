# TagHere POS Agent - Releases

TagHere POS Agent 자동 업데이트 배포용 저장소입니다.

이 저장소는 소스 코드를 포함하지 않으며, [GitHub Releases](https://github.com/TMR-Founders-Dev/taghere-pos-agent-releases/releases)를 통해 Velopack 업데이트 패키지(.nupkg)만 배포합니다.

## 배포 방법

### 1. 빌드

#### 옵션 A: Windows에서 전체 빌드 (PowerShell)

```powershell
# taghere-pos-agent 프로젝트 루트에서
.\build-installer.ps1 -Version "1.0.1"
```

#### 옵션 B: macOS에서 publish + Windows에서 vpk 패키징

```bash
# 1) macOS에서 dotnet publish (taghere-pos-agent 프로젝트 루트에서)
# self-contained를 설정해야 .NET 별도 설치 없이 실행 가능
# 상황에 맞게 버전 수정해야함
rm -rf publish/Agent-installer
dotnet publish src/TagherePosAgent/TagherePosAgent.csproj \
  --configuration Release \
  --runtime win-x86 \
  --self-contained true \
  --output publish/Agent-installer \
  /p:Version=1.0.1 \
  /p:PublishSingleFile=false \
  /p:IncludeNativeLibrariesForSelfExtract=false
```

```powershell
# 2) Windows에서 vpk pack (publish/Agent-installer 폴더 동기화 후)
vpk pack `
  --packId TagherePosAgent `
  --packVersion 1.0.1 `
  --packDir publish\Agent-installer `
  --mainExe TagherePosAgent.exe `
  --outputDir releases `
  --icon src\TagherePosAgent\Resources\app.ico `
  --packTitle "TagHere POS Agent" `
  --packAuthors "TagHere"
```

빌드 완료 후 `releases/` 폴더에 다음 파일들이 생성됩니다:

| 파일 | 설명 | 필수 |
|------|------|------|
| `TagherePosAgent-{ver}-full.nupkg` | 전체 업데이트 패키지 | O |
| `TagherePosAgent-{ver}-delta.nupkg` | 델타 업데이트 패키지 (용량 절약) | 권장 |
| `releases.win.json` | Velopack 버전 메타데이터 | O |
| `RELEASES` | Velopack 레거시 메타데이터 | O |

### 2. Release 생성

#### 방법 A: GitHub CLI

```powershell
gh release create v1.0.1 `
  releases/TagherePosAgent-1.0.1-full.nupkg `
  releases/TagherePosAgent-1.0.1-delta.nupkg `
  releases/releases.win.json `
  releases/RELEASES `
  --repo TMR-Founders-Dev/taghere-pos-agent-releases `
  --title "v1.0.1" `
  --notes "변경 사항 작성"
```

#### 방법 B: GitHub 웹에서 수동 업로드

1. [Releases 페이지](https://github.com/TMR-Founders-Dev/taghere-pos-agent-releases/releases) 접속
2. **Draft a new release** 클릭
3. Tag: `v1.0.1` 입력 → **Create new tag** 선택
4. Title: `v1.0.1` 입력
5. 아래 파일들을 드래그 앤 드롭으로 업로드:
   - `TagherePosAgent-1.0.1-full.nupkg`
   - `TagherePosAgent-1.0.1-delta.nupkg` (있는 경우)
   - `releases.win.json`
   - `RELEASES`
6. **Publish release** 클릭

### 3. 고객 측 자동 적용

설치된 에이전트가 앱 시작 시 자동으로 이 저장소의 최신 Release를 확인하고, 새 버전이 있으면 백그라운드에서 다운로드하여 다음 실행 시 적용합니다.
