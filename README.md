# 챗봇프로젝트 — 도구 설계 과제 (N026)

AIFFEL N026(도구 설계) 챕터7 과제 "서비스 가이드 챗봇 설계" 제출용 저장소입니다.

> **저장소 공개 범위 안내**: 과제 안내에는 "개인 저장소를 하나 만들고(비공개 권장)"라고 되어 있으나, 채점자가 URL만으로 접근할 수 있도록 이 저장소는 **의도적으로 공개(Public)로 전환**했습니다(비공개 저장소는 협업자로 초대된 계정만 열람 가능해 URL 제출과 맞지 않음). 저장소 내용에 API 키·개인정보 등 민감정보는 포함되어 있지 않습니다.

과거에 만든 실제 서비스 `storytowebtoon`(시놉시스 → 시나리오 JSON 추출 → 4인 페르소나 평가 파이프라인, Phase 1 — 로컬에서 개발 중인 프로젝트, 별도 GitHub 저장소 없음)을 대상으로, "도구는 함수가 아니라 계약이다"라는 N026의 원칙을 실제 코드에 그대로 적용했습니다. 도구를 새로 상상해서 만든 것이 아니라, 이미 동작하는 파이프라인의 핵심 함수 4개를 도구 계약으로 공식화하고, 실제 API를 호출하는 실증 테스트까지 진행했습니다.

## 평가기준 대응

| 평가기준 | 이 저장소에서 확인할 곳 |
|---|---|
| 다양한 상황에 대비한 서비스 설계 — 공격 시도 1개 + 기대 동작 기록 | [design-packet.md](design-packet.md) ⑥ 테스트 문항 7·8번 (공격 유형 2개), 실제 실행 증거는 [traces/trace-08_injection-test.txt](traces/trace-08_injection-test.txt) |
| 적절한 프로젝트 설계 — 과거 생성한 서비스에서 유의미한 챗봇 가이드 서비스 설계 | [design-packet.md](design-packet.md) 목적/①시스템 지시 — storytowebtoon Phase 1 파이프라인을 안내·오케스트레이션하는 가이드 챗봇으로 설계 |
| 도구 정의서 직접 설계 — '쓰는 때·안 쓰는 때' + '실패 뒤 행동' 모두 기록 | [tool-definition.md](tool-definition.md) — 도구 4개 전부 두 항목 포함 |

## 문서 구성

| 파일 | 내용 |
|---|---|
| [PRD.md](PRD.md) | 이 가이드 챗봇이 왜 필요하고 무엇을 하는지 (목표/범위/사용자) |
| [design-packet.md](design-packet.md) | 과제 7항목 전체(①시스템 지시 ~ ⑦트레이스) |
| [tool-definition.md](tool-definition.md) | 도구 정의서 4개 (design-packet.md ②를 채점 편의를 위해 별도 파일로도 제공) |
| [evaluation-and-antipatterns.md](evaluation-and-antipatterns.md) | 챕터8 이진 판정 4항목·회귀 확인 / 챕터9 4대 안티패턴 자가점검 |
| `traces/` | 실제 API 호출 실행 기록 (정상 케이스 1건 + 공격 시도 방어 실증 1건) |

## 원본 서비스

storytowebtoon(웹소설 시놉시스 → 시나리오 JSON → 4인 전문가 페르소나 평가, Phase 1)은 로컬(`~/Documents/projects/storytowebtoon`)에서 개발 중인 실제 프로젝트로, 아직 별도 GitHub 저장소로 공개하지 않았습니다. 이 저장소는 그 파이프라인의 도구 계층을 설계 문서로 공식화한 결과물이며, 코드 자체는 포함하지 않습니다(설계 문서와 실행 트레이스만 담습니다).
