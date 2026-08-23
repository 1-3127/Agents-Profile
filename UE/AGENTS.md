# Unreal Engine Agent Instructions

## 적용 범위와 우선순위

- 이 파일은 `D:\VSCODE-WorkSpace\UE` 아래의 Unreal Engine 및 UE MCP 작업에 적용합니다.
- 루트 `AGENTS.md`와 `Agents/workflow.md`를 함께 따릅니다.
- 더 가까운 경로의 `AGENTS.md`가 저장소·플러그인·소스 영역의 구체적 규칙을 정의하면 해당 규칙을 우선합니다.
- `repo_install-MCP/UEMCP_561` 작업에서는 `UE_MCP_CODEX_GUIDELINES.md`를 먼저 확인합니다.

## 작업 전 확인

- Unreal Engine 설치 루트와 실제 Engine 버전
- 대상 `.uproject` 절대 경로와 `EngineAssociation`
- 프로젝트의 실제 `Plugins` 위치
- 기준 저장소, 프로젝트 설치본과 배포 패키지의 역할
- `.uplugin` 버전, 모듈 유형과 의존 플러그인
- Unreal Editor와 Live Coding 실행 상태
- 기준 저장소의 기존 변경 상태
- 사용 중인 MCP 전송 방식, endpoint와 port

경로, 버전, port와 기준 복사본을 추측하지 않습니다.

## 설치와 빌드

- 소스 플러그인과 Blueprint-only 프로젝트용 사전 빌드 패키지를 구분합니다.
- 프로젝트를 열기 전에 Editor Target 빌드가 필요한지 확인합니다.
- 빌드 전에 Unreal Editor와 Live Coding의 DLL 점유 가능성을 확인합니다.
- 빌드 실패만으로 `Binaries`, `Intermediate`, `Saved`를 삭제하지 않고 로그에서 원인을 먼저 확인합니다.
- 패키징 후 `.uplugin`, `Installed=true`, `Binaries/Win64`, 필요한 `Source`, `Config`, `Resources`와 소스 수정 반영 여부를 확인합니다.

## UE MCP 이용 수칙

### 연결 방식

- Native MCP와 TypeScript stdio + WebSocket Bridge 중 현재 사용 방식을 명시합니다.
- Native MCP는 플러그인의 HTTP/SSE endpoint에 직접 연결합니다.
- TypeScript 방식은 MCP 클라이언트가 stdio server를 실행하고 WebSocket으로 Unreal 플러그인과 통신합니다.
- 두 방식이 함께 존재할 수 있으므로 설정 파일만 보고 실행 주체나 연결 성공을 단정하지 않습니다.

### 보안

- 네트워크 바인딩은 기본적으로 loopback만 사용합니다.
- 명시적 승인과 인증 계획 없이 `0.0.0.0` 또는 LAN으로 공개하지 않습니다.
- LAN 공개, TLS 해제 또는 capability token 변경은 Critical 작업으로 취급합니다.
- token과 비밀값을 설정 파일, 코드, 문서 또는 로그에 기록하지 않습니다.
- Unreal package 경로와 호스트 파일 시스템 절대 경로를 구분합니다.

### 연결 검증

낮은 위험의 읽기 작업부터 다음 순서로 검증합니다.

```text
서버·플러그인 상태와 tool 조회
→ 현재 프로젝트·레벨 조회
→ 에셋 목록 조회
→ 허가된 작은 쓰기 작업
→ 재조회·컴파일·저장 검증
→ 필요한 경우 PIE 검증 및 종료
```

- MCP 응답의 `success`만으로 완료를 선언하지 않습니다.
- 작업에 맞는 재조회, Blueprint·Material 컴파일, 저장 상태 또는 런타임 결과로 교차검증합니다.
- PIE는 런타임 동작 검증이 필요할 때만 사용하고 종료 여부까지 확인합니다.

### Unreal 변경

- Unreal 에셋은 가능한 한 Unreal Editor API 또는 MCP handler로 조작합니다.
- `.uasset`을 파일 시스템에서 직접 편집하거나 임의 복사하지 않습니다.
- 레벨과 에셋 경로는 `/Game/...` 형식을 사용합니다.
- 저장은 프로젝트의 안전한 save/load wrapper를 우선하며 새 코드에 원시 `UPackage::SavePackage()` 호출을 도입하지 않습니다.
- 각 쓰기 작업 뒤에 에셋 경로, Actor·Component 이름, 컴파일 결과와 저장 여부를 확인합니다.
- 에셋 생성, 그래프 구성, 레벨 배치와 런타임 검증은 독립적인 작업 모듈로 나눕니다.

## Blueprint 설계

- 최상위 Event Graph에서 전체 실행 흐름을 파악할 수 있게 유지합니다.
- 노드별 `NodeComment` 대신 `C` 단축키로 만드는 실제 Blueprint Comment Box를 기능 모듈별로 사용합니다.
- Comment Box 제목에는 역할과 결과가 드러나는 이름을 사용합니다.
- 반복되는 원자적 로직이나 명확한 공개 API만 함수로 분리합니다.
- 정리만을 위해 모든 실행 흐름을 함수 트리 안에 숨기지 않습니다.
- 모듈 간 실행 핀 결합을 줄이고 긴 데이터 선에는 의미가 분명한 custom reroute를 사용합니다.
- 확장이 예상되는 데이터는 고정 번호 변수보다 구조체 배열, Actor Component 또는 각 Actor가 상태를 소유하는 구조를 검토합니다.

## 변수 노출

- 사용자가 인스턴스별로 조정해야 하는 값만 Parameter로 노출합니다.
- 런타임 캐시, DMI, Component 참조와 임시 상태는 Internal로 분류합니다.
- Internal 변수는 기본적으로 private이며 `Instance Editable`과 `Expose on Spawn`을 사용하지 않습니다.
- 함수 안에서만 쓰는 임시값은 Blueprint 멤버보다 local variable을 우선합니다.

## UE 백업 연계

- Critical 삭제·덮어쓰기는 `Agents/workflow.md`의 `.backups` 정책을 따릅니다.
- UE 에셋은 안전한 저장이 완료됐는지 먼저 확인합니다.
- PIE, package save, garbage collection 또는 async loading 중에는 에셋 백업·삭제를 수행하지 않습니다.
- 파일 시스템 백업이 필요한 경우 프로젝트 상대 경로를 보존하고, 백업 검증 후 Unreal Editor API 또는 MCP를 통해 원래 작업을 수행합니다.
