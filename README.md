# Agent Instruction Profiles

이 폴더는 현재 작업공간에서 사용 중인 `AGENTS.md` 지침의 무손실 GitHub 원본입니다. 이 저장소의 경로와 파일 내용은 실제 작업공간의 지침 트리를 그대로 반영합니다.

## 구조

```text
AGENTS.md                         # 모든 작업공간에 적용할 공통 규칙
Blender/AGENTS.md                 # Blender 하위 폴더용 추가 규칙
UE/AGENTS.md                      # Unreal Engine 하위 폴더용 추가 규칙
Others/AGENTS.md                  # 독립 도구·로컬 AI 하위 폴더용 추가 규칙
Others/DOCS/TTS.md                # TTS 구축·운영 참고 문서
```

## 배포 원칙

1. 이 저장소는 실제 작업공간과 같은 상대 경로 트리를 사용합니다.
2. 파일 내용은 축약·번역·재해석하지 않고 실제 지침 전문을 유지합니다.
3. 공통 규칙은 루트 파일에만 두고, 하위 `AGENTS.md`에는 해당 영역의 추가 규칙만 둡니다.
4. 실제 작업공간에 직접 적용하려면 이 저장소의 Git metadata를 작업공간 루트의 worktree에 연결하거나, 명시적인 동기화 스크립트를 사용합니다.
5. 사용자 작성 문서와 목록을 자동 생성·정렬·덮어쓰는 규칙을 추가하지 않습니다.

## 현재 작업공간 배포 대상

| 원본 파일 | 대상 파일 |
| --- | --- |
| `AGENTS.md` | `./AGENTS.md` |
| `Blender/AGENTS.md` | `./Blender/AGENTS.md` |
| `UE/AGENTS.md` | `./UE/AGENTS.md` |
| `Others/AGENTS.md` | `./Others/AGENTS.md` |
| `Others/DOCS/TTS.md` | `./Others/DOCS/TTS.md` |

원격 GitHub URL과 인증 정보는 이 저장소에 기록하지 않습니다.
