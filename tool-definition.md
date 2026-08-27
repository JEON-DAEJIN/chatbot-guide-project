# 도구 정의서 — storytowebtoon Phase 1

`design-packet.md` ②의 내용을 채점 편의를 위해 단독 파일로도 제공합니다. 원본 함수는 `src/module_a.py`, `src/module_b.py`에 있습니다(코드 자체는 이 저장소에 포함하지 않음).

## 1. `extract_synopsis`

| 항목 | 내용 |
|---|---|
| 이름 | `extract_synopsis` |
| 하는 일 한 문장 | 원고 전체 텍스트를 읽고 제목/장르 추정, 로그라인, 시놉시스, 주요 인물을 추출합니다 |
| **언제 쓰는 때** | 사용자가 "원고 전체"를 입력했을 때(F2 전용) |
| **언제 안 쓰는 때** | 이미 시놉시스가 확보된 경우, 또는 원고가 빈 문자열인 경우 |
| 인자 스키마 | `manuscript_text: string` (필수, 빈 문자열 불가) |
| 반환 형식 | `{title_guess, genre_guess, logline, synopsis, main_characters[]}` |
| **실패 뒤 행동** | `LLMCallError`(API키 없음/네트워크/JSON 파싱 실패) 발생 시 1회 재시도. 재시도도 실패하면 추측하지 않고 원문 에러를 그대로 노출하며 중단. 원고 300,000자 초과 시 경고 문구 표시(현재는 자동 진행되나, 사람 확인 게이트를 거치도록 개선 예정) |

## 2. `extract_scenario`

| 항목 | 내용 |
|---|---|
| 이름 | `extract_scenario` |
| 하는 일 한 문장 | 확정된 시놉시스를 씬-컷-대사 구조의 시나리오 JSON으로 변환합니다 |
| **언제 쓰는 때** | 시놉시스 텍스트(직접 입력 또는 `extract_synopsis` 결과)가 확정된 뒤 |
| **언제 안 쓰는 때** | 시놉시스가 비어있는 경우 |
| 인자 스키마 | `synopsis_text: string`(필수) · `work_id: string`(필수) · `title/genre: string`(선택) · `input_type: "synopsis"\|"full_manuscript"` · `source_char_count: int`(선택) |
| 반환 형식 | `{meta, logline, characters[], scenes[{scene_id, location, purpose, cuts[]}]}` — 씬 5~8개, 컷 3~6개 규칙 |
| **실패 뒤 행동** | `LLMCallError` 시 1회 재시도 후 실패 표시, 추측 금지. 씬/컷 개수 규칙을 벗어난 응답은 강제로 재시도시키지 않고(창작 다양성 보호) 저장 후 UI에 경고만 표시(개선 예정, 현재 미구현) |

## 3. `evaluate_personas`

| 항목 | 내용 |
|---|---|
| 이름 | `evaluate_personas` |
| 하는 일 한 문장 | 시나리오 JSON을 작가·PD·플랫폼관계자·독자 4개 페르소나 관점에서 병렬로 채점합니다 |
| **언제 쓰는 때** | 시나리오 JSON이 확정된 뒤 |
| **언제 안 쓰는 때** | 시나리오가 없는 경우. 이미 평가된 `work_id`를 재평가하려는 경우는 승인 없이 쓰지 않음(비용 재발생) |
| 인자 스키마 | `scenario_json: object` (필수) |
| 반환 형식 | `[{persona, dimension_scores:{5항목×{score,reason}}, weighted_total}]` × 4 |
| **실패 뒤 행동** | 개별 페르소나 호출은 1회씩 재시도. 4개 중 하나라도 최종 실패하면 현재는 나머지 3개 성공분까지 통째로 폐기됨 — 성공분 보존 + 실패분만 재시도하도록 개선 예정 |

## 4. `generate_report`

| 항목 | 내용 |
|---|---|
| 이름 | `generate_report` |
| 하는 일 한 문장 | 4인 평가를 가중평균해 종합 점수·추천 등급을 계산하고, 공통 강점/리스크를 LLM으로 종합합니다 |
| **언제 쓰는 때** | `evaluate_personas`가 4개 결과를 모두 반환한 뒤 |
| **언제 안 쓰는 때** | 페르소나 결과가 4개 미만인 경우(가중평균 왜곡 방지) |
| 인자 스키마 | `work_id: string` · `persona_results: array`(정확히 4개) |
| 반환 형식 | `{work_id, persona_scores[], aggregate_score, recommendation, top_strength, top_risk}` |
| **실패 뒤 행동** | 강점/리스크 종합 호출 실패 시 1회 재시도, 그래도 실패하면 로컬 계산 가능한 `aggregate_score`/`recommendation`은 보존하고 종합 문장만 보류 표시하도록 개선 예정(현재는 전체가 함께 사라짐) |
