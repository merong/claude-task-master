# Task Master 명령어 참조

사용 가능한 모든 명령어에 대한 포괄적인 참조입니다:

## PRD 파싱

```bash
# PRD 파일을 파싱하고 태스크 생성
task-master parse-prd <prd-file.txt>

# 생성된 태스크 수 제한
task-master parse-prd <prd-file.txt> --num-tasks=10
```

## 태스크 목록

```bash
# 모든 태스크 나열
task-master list

# 특정 상태의 태스크 나열
task-master list --status=<status>

# 서브태스크가 있는 태스크 나열
task-master list --with-subtasks

# 특정 상태의 태스크와 서브태스크 포함하여 나열
task-master list --status=<status> --with-subtasks
```

## 다음 태스크 표시

```bash
# 종속성 및 상태에 따라 다음 작업할 태스크 표시
task-master next
```

## 특정 태스크 표시

```bash
# 특정 태스크의 세부 정보 표시
task-master show <id>
# 또는
task-master show --id=<id>

# 특정 서브태스크 보기 (예: 태스크 1의 서브태스크 2)
task-master show 1.2
```

## 태스크 업데이트

```bash
# 특정 ID부터 태스크를 업데이트하고 컨텍스트 제공
task-master update --from=<id> --prompt="<prompt>"

# 연구 역할을 사용하여 태스크 업데이트
task-master update --from=<id> --prompt="<prompt>" --research
```

## 특정 태스크 업데이트

```bash
# ID로 단일 태스크를 새 정보로 업데이트
task-master update-task --id=<id> --prompt="<prompt>"

# 연구 기반 업데이트 사용
task-master update-task --id=<id> --prompt="<prompt>" --research
```

## 서브태스크 업데이트

```bash
# 특정 서브태스크에 추가 정보 추가
task-master update-subtask --id=<parentId.subtaskId> --prompt="<prompt>"

# 예시: 태스크 5의 서브태스크 2에 API 속도 제한에 대한 세부 정보 추가
task-master update-subtask --id=5.2 --prompt="Add rate limiting of 100 requests per minute"

# 연구 기반 업데이트 사용
task-master update-subtask --id=<parentId.subtaskId> --prompt="<prompt>" --research
```

`update-task` 명령이 태스크 정보를 대체하는 것과 달리, `update-subtask` 명령은 기존 서브태스크 세부 정보에 새 정보를 _추가_하고 타임스탬프로 표시합니다. 이는 원본 내용을 보존하면서 서브태스크를 반복적으로 개선하는 데 유용합니다.

## 태스크 파일 생성

```bash
# tasks.json에서 개별 태스크 파일 생성
task-master generate
```

## 태스크 상태 설정

```bash
# 단일 태스크의 상태 설정
task-master set-status --id=<id> --status=<status>

# 여러 태스크의 상태 설정
task-master set-status --id=1,2,3 --status=<status>

# 서브태스크의 상태 설정
task-master set-status --id=1.1,1.2 --status=<status>
```

태스크를 "done"으로 표시하면 모든 서브태스크도 자동으로 "done"으로 표시됩니다.

## 태스크 확장

```bash
# 특정 태스크를 서브태스크로 확장
task-master expand --id=<id> --num=<number>

# 추가 컨텍스트로 확장
task-master expand --id=<id> --prompt="<context>"

# 모든 대기 중인 태스크 확장
task-master expand --all

# 이미 서브태스크가 있는 태스크의 서브태스크 강제 재생성
task-master expand --all --force

# 특정 태스크에 대한 연구 기반 서브태스크 생성
task-master expand --id=<id> --research

# 모든 태스크에 대한 연구 기반 생성
task-master expand --all --research
```

## 서브태스크 지우기

```bash
# 특정 태스크에서 서브태스크 지우기
task-master clear-subtasks --id=<id>

# 여러 태스크에서 서브태스크 지우기
task-master clear-subtasks --id=1,2,3

# 모든 태스크에서 서브태스크 지우기
task-master clear-subtasks --all
```

## 태스크 복잡성 분석

```bash
# 모든 태스크의 복잡성 분석
task-master analyze-complexity

# 사용자 지정 위치에 보고서 저장
task-master analyze-complexity --output=my-report.json

# 특정 LLM 모델 사용
task-master analyze-complexity --model=claude-3-opus-20240229

# 사용자 지정 복잡성 임계값 설정 (1-10)
task-master analyze-complexity --threshold=6

# 대체 태스크 파일 사용
task-master analyze-complexity --file=custom-tasks.json

# 연구 기반 복잡성 분석을 위해 Perplexity AI 사용
task-master analyze-complexity --research
```

## 복잡성 보고서 보기

```bash
# 태스크 복잡성 분석 보고서 표시
task-master complexity-report

# 사용자 지정 위치의 보고서 보기
task-master complexity-report --file=my-report.json
```

## 태스크 종속성 관리

```bash
# 태스크에 종속성 추가
task-master add-dependency --id=<id> --depends-on=<id>

# 태스크에서 종속성 제거
task-master remove-dependency --id=<id> --depends-on=<id>

# 수정하지 않고 종속성 검증
task-master validate-dependencies

# 잘못된 종속성 자동으로 찾아 수정
task-master fix-dependencies
```

## 새 태스크 추가

```bash
# AI를 사용하여 새 태스크 추가 (주 역할)
task-master add-task --prompt="Description of the new task"

# AI를 사용하여 새 태스크 추가 (연구 역할)
task-master add-task --prompt="Description of the new task" --research

# 종속성이 있는 태스크 추가
task-master add-task --prompt="Description" --dependencies=1,2,3

# 우선순위가 있는 태스크 추가
task-master add-task --prompt="Description" --priority=high
```

## 프로젝트 초기화

```bash
# Task Master 구조로 새 프로젝트 초기화
task-master init
```

## AI 모델 구성

```bash
# 현재 AI 모델 구성 및 API 키 상태 보기
task-master models

# 생성/업데이트를 위한 기본 모델 설정 (알려진 경우 제공자 추론됨)
task-master models --set-main=claude-3-opus-20240229

# 연구 모델 설정
task-master models --set-research=sonar-pro

# 대체 모델 설정
task-master models --set-fallback=claude-3-haiku-20240307

# 주 역할을 위한 사용자 지정 Ollama 모델 설정
task-master models --set-main=my-local-llama --ollama

# 연구 역할을 위한 사용자 지정 OpenRouter 모델 설정
task-master models --set-research=google/gemini-pro --openrouter

# 사용자 지정 모델을 포함한 모델을 구성하기 위한 대화형 설정 실행
task-master models --setup
```

구성은 프로젝트 루트의 `.taskmasterconfig`에 저장됩니다. API 키는 여전히 `.env` 또는 MCP 구성을 통해 관리됩니다. 사용 가능한 내장 모델을 보려면 플래그 없이 `task-master models`를 사용하세요. 안내형 경험을 위해 `--setup`을 사용하세요.