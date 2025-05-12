# Task Master

### by [@eyaltoledano](https://x.com/eyaltoledano)

Cursor AI와 원활하게 작동하도록 설계된 Claude 기반 AI 주도 개발을 위한 태스크 관리 시스템입니다.

## 요구 사항

- Node.js 14.0.0 이상
- Anthropic API 키 (Claude API)
- Anthropic SDK 버전 0.39.0 이상
- OpenAI SDK (Perplexity API 통합을 위한 옵션)

## 설정

Task Master는 두 가지 주요 설정 방법을 사용합니다:

1. **`.taskmasterconfig` 파일 (프로젝트 루트)**

   - 대부분의 설정을 저장: AI 모델 선택(main, research, fallback), 매개변수(최대 토큰, 온도), 로깅 수준, 기본 우선순위/서브태스크, 프로젝트 이름.
   - **`task-master models --setup` CLI 명령 또는 `models` MCP 도구를 사용하여 생성 및 관리합니다.**
   - 정확히 알고 있지 않다면 수동으로 편집하지 마세요.

2. **환경 변수 (`.env` 파일 또는 MCP `env` 블록)**
   - **API 키**(예: `ANTHROPIC_API_KEY`, `PERPLEXITY_API_KEY` 등)와 특정 엔드포인트(예: `OLLAMA_BASE_URL`)와 같은 민감한 정보에만 사용됩니다.
   - **CLI용:** 프로젝트 루트의 `.env` 파일에 키를 배치합니다.
   - **MCP/Cursor용:** 사용 중인 AI IDE 또는 클라이언트에 따라 `.cursor/mcp.json`(또는 다른 MCP 구성)의 `env` 섹션에 `taskmaster-ai` 서버 정의 아래에 키를 배치합니다.

**중요:** 모델 선택, 최대 토큰, 온도, 로그 수준과 같은 설정은 **더 이상 환경 변수를 통해 구성되지 않습니다.** `task-master models` 명령 또는 도구를 사용하세요.

자세한 내용은 [설정 가이드](docs/configuration.md)를 참조하세요.

## 설치

```bash
# 전역으로 설치
npm install -g task-master-ai

# 또는 프로젝트 내에 로컬로 설치
npm install task-master-ai
```

### 새 프로젝트 초기화

```bash
# 전역으로 설치한 경우
task-master init

# 로컬로 설치한 경우
npx task-master init
```

이 명령은 프로젝트 세부 정보를 입력하라는 메시지를 표시하고 필요한 파일과 구조로 새 프로젝트를 설정합니다.

### 중요 사항

1. **ES 모듈 구성:**

   - 이 프로젝트는 CommonJS 대신 ES 모듈(ESM)을 사용합니다.
   - 이는 package.json에서 `"type": "module"`로 설정됩니다.
   - `require()` 대신 `import/export` 구문을 사용하세요.
   - 파일은 `.js` 또는 `.mjs` 확장자를 사용해야 합니다.
   - CommonJS 모듈을 사용하려면 다음 중 하나를 수행하세요:
     - `.cjs` 확장자로 이름을 변경합니다.
     - 동적 가져오기에 `await import()`를 사용합니다.
   - 프로젝트 전체에서 CommonJS가 필요한 경우 package.json에서 `"type": "module"`을 제거하세요. 단, Task Master 스크립트는 ESM을 예상합니다.

2. Anthropic SDK 버전은 0.39.0 이상이어야 합니다.

## 전역 명령어로 빠르게 시작하기

패키지를 전역적으로 설치한 후, 모든 디렉토리에서 다음 CLI 명령을 사용할 수 있습니다:

```bash
# 새 프로젝트 초기화
task-master init

# PRD를 분석하고 태스크 생성
task-master parse-prd your-prd.txt

# 모든 태스크 목록 표시
task-master list

# 다음에 작업할 태스크 표시
task-master next

# 태스크 파일 생성
task-master generate
```

## 문제 해결

### `task-master init`이 응답하지 않는 경우:

Node를 직접 사용하여 실행해 보세요:

```bash
node node_modules/claude-task-master/scripts/init.js
```

또는 저장소를 복제하고 실행:

```bash
git clone https://github.com/eyaltoledano/claude-task-master.git
cd claude-task-master
node scripts/init.js
```

## 태스크 구조

tasks.json의 태스크는 다음 구조를 가집니다:

- `id`: 태스크의 고유 식별자 (예: `1`)
- `title`: 태스크의 간결한 설명적 제목 (예: `"Initialize Repo"`)
- `description`: 태스크가 포함하는 내용에 대한 간결한 설명 (예: `"Create a new repository, set up initial structure."`)
- `status`: 태스크의 현재 상태 (예: `"pending"`, `"done"`, `"deferred"`)
- `dependencies`: 이 태스크 전에 완료해야 하는 태스크의 ID (예: `[1, 2]`)
  - 종속성은 상태 표시기와 함께 표시됩니다 (완료된 항목은 ✅, 대기 중인 항목은 ⏱️)
  - 이는 작업을 차단하는 전제 조건 태스크를 빠르게 식별하는 데 도움이 됩니다
- `priority`: 태스크의 중요도 수준 (예: `"high"`, `"medium"`, `"low"`)
- `details`: 심층적인 구현 지침 (예: `"Use GitHub client ID/secret, handle callback, set session token."`)
- `testStrategy`: 검증 접근 방식 (예: `"Deploy and call endpoint to confirm 'Hello World' response."`)
- `subtasks`: 주요 태스크를 구성하는 더 작고 구체적인 태스크 목록 (예: `[{"id": 1, "title": "Configure OAuth", ...}]`)

## Cursor AI와 통합

Claude Task Master는 [Cursor AI](https://www.cursor.so/)와 원활하게 작동하도록 설계되어 AI 주도 개발을 위한 구조화된 워크플로우를 제공합니다.

### Cursor 설정

1. 프로젝트를 초기화한 후 Cursor에서 엽니다
2. `.cursor/rules/dev_workflow.mdc` 파일은 Cursor에 의해 자동으로 로드되어 AI에 태스크 관리 시스템에 대한 지식을 제공합니다
3. PRD 문서를 `scripts/` 디렉토리에 배치합니다 (예: `scripts/prd.txt`)
4. Cursor의 AI 채팅을 열고 Agent 모드로 전환합니다

### Cursor에서 MCP 설정

Model Control Protocol(MCP)을 사용하여 Cursor 내에서 직접 향상된 태스크 관리 기능을 활성화하려면:

1. Cursor 설정으로 이동
2. MCP 섹션으로 이동
3. "Add New MCP Server" 클릭
4. 다음 세부 정보로 구성:
   - 이름: "Task Master"
   - 유형: "Command"
   - 명령: "npx -y task-master-ai"
5. 설정 저장

구성이 완료되면 Cursor의 인터페이스를 통해 직접 Task Master의 태스크 관리 명령과 상호 작용할 수 있어 더 통합된 경험을 제공합니다.

### 초기 태스크 생성

Cursor의 AI 채팅에서 에이전트에게 PRD에서 태스크를 생성하도록 지시합니다:

```
Please use the task-master parse-prd command to generate tasks from my PRD. The PRD is located at scripts/prd.txt.
```

에이전트는 다음을 실행합니다:

```bash
task-master parse-prd scripts/prd.txt
```

이는 다음과 같은 작업을 수행합니다:

- PRD 문서 파싱
- 태스크, 종속성, 우선순위 및 테스트 전략이 포함된 구조화된 `tasks.json` 파일 생성
- Cursor 규칙으로 인해 에이전트는 이 프로세스를 이해합니다

### 개별 태스크 파일 생성

다음으로, 에이전트에게 개별 태스크 파일을 생성하도록 요청합니다:

```
Please generate individual task files from tasks.json
```

에이전트는 다음을 실행합니다:

```bash
task-master generate
```

이는 `tasks/` 디렉토리에 개별 태스크 파일(예: `task_001.txt`, `task_002.txt`)을 생성하여 특정 태스크를 참조하기 쉽게 만듭니다.

## AI 주도 개발 워크플로우

Cursor 에이전트는 (규칙 파일을 통해) 다음 워크플로우를 따르도록 사전 구성되어 있습니다:

### 1. 태스크 발견 및 선택

에이전트에게 사용 가능한 태스크를 나열하도록 요청합니다:

```
What tasks are available to work on next?
```

에이전트는 다음 작업을 수행합니다:

- 모든 태스크를 보기 위해 `task-master list` 실행
- 다음에 작업할 태스크를 결정하기 위해 `task-master next` 실행
- 종속성을 분석하여 작업할 준비가 된 태스크 파악
- 우선순위 수준 및 ID 순서에 따라 태스크 우선순위 지정
- 구현할 다음 태스크를 제안

### 2. 태스크 구현

태스크를 구현할 때, 에이전트는 다음을 수행합니다:

- 구현 세부 사항을 위해 태스크의 상세 섹션 참조
- 이전 태스크에 대한 종속성 고려
- 프로젝트의 코딩 표준 준수
- 태스크의 testStrategy를 기반으로 적절한 테스트 생성

다음과 같이 물어볼 수 있습니다:

```
Let's implement task 3. What does it involve?
```

### 3. 태스크 검증

태스크를 완료로 표시하기 전에, 다음에 따라 확인합니다:

- 태스크의 지정된 testStrategy
- 코드베이스의 자동화된 테스트
- 필요한 경우 수동 검증

### 4. 태스크 완료

태스크가 완료되면 에이전트에게 알립니다:

```
Task 3 is now complete. Please update its status.
```

에이전트는 다음을 실행합니다:

```bash
task-master set-status --id=3 --status=done
```

### 5. 구현 변경 사항 처리

구현 중에 다음을 발견한 경우:

- 현재 접근 방식이 계획된 것과 크게 다른 경우
- 현재 구현 선택으로 인해 향후 태스크를 수정해야 하는 경우
- 새로운 종속성이나 요구 사항이 나타난 경우

에이전트에게 다음과 같이 말합니다:

```
We've changed our approach. We're now using Express instead of Fastify. Please update all future tasks to reflect this change.
```

에이전트는 다음을 실행합니다:

```bash
task-master update --from=4 --prompt="Now we are using Express instead of Fastify."
```

이는 완료된 작업을 보존하면서 tasks.json의 후속 태스크를 다시 작성하거나 범위를 다시 지정합니다.

### 6. 복잡한 태스크 분할

더 세분화가 필요한 복잡한 태스크의 경우:

```
Task 5 seems complex. Can you break it down into subtasks?
```

에이전트는 다음을 실행합니다:

```bash
task-master expand --id=5 --num=3
```

추가 컨텍스트를 제공할 수 있습니다:

```
Please break down task 5 with a focus on security considerations.
```

에이전트는 다음을 실행합니다:

```bash
task-master expand --id=5 --prompt="Focus on security aspects"
```

또한 모든 대기 중인 태스크를 확장할 수 있습니다:

```
Please break down all pending tasks into subtasks.
```

에이전트는 다음을 실행합니다:

```bash
task-master expand --all
```

Perplexity AI를 사용한 연구 기반 서브태스크 생성:

```
Please break down task 5 using research-backed generation.
```

에이전트는 다음을 실행합니다:

```bash
task-master expand --id=5 --research
```

## 명령어 참조

다음은 사용 가능한 모든 명령어에 대한 종합적인 참조입니다:

### PRD 파싱

```bash
# PRD 파일을 파싱하고 태스크 생성
task-master parse-prd <prd-file.txt>

# 생성된 태스크 수 제한
task-master parse-prd <prd-file.txt> --num-tasks=10
```

### 태스크 목록

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

### 다음 태스크 표시

```bash
# 종속성 및 상태에 따라 다음 작업할 태스크 표시
task-master next
```

### 특정 태스크 표시

```bash
# 특정 태스크의 세부 정보 표시
task-master show <id>
# 또는
task-master show --id=<id>

# 특정 서브태스크 보기 (예: 태스크 1의 서브태스크 2)
task-master show 1.2
```

### 태스크 업데이트

```bash
# 특정 ID에서 태스크를 업데이트하고 컨텍스트 제공
task-master update --from=<id> --prompt="<prompt>"
```

### 태스크 파일 생성

```bash
# tasks.json에서 개별 태스크 파일 생성
task-master generate
```

### 태스크 상태 설정

```bash
# 단일 태스크의 상태 설정
task-master set-status --id=<id> --status=<status>

# 여러 태스크의 상태 설정
task-master set-status --id=1,2,3 --status=<status>

# 서브태스크의 상태 설정
task-master set-status --id=1.1,1.2 --status=<status>
```

태스크를 "done"으로 표시하면 모든 서브태스크도 자동으로 "done"으로 표시됩니다.

### 태스크 확장

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

### 서브태스크 지우기

```bash
# 특정 태스크에서 서브태스크 지우기
task-master clear-subtasks --id=<id>

# 여러 태스크에서 서브태스크 지우기
task-master clear-subtasks --id=1,2,3

# 모든 태스크에서 서브태스크 지우기
task-master clear-subtasks --all
```

### 태스크 복잡성 분석

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

### 복잡성 보고서 보기

```bash
# 태스크 복잡성 분석 보고서 표시
task-master complexity-report

# 사용자 지정 위치의 보고서 보기
task-master complexity-report --file=my-report.json
```

### 태스크 종속성 관리

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

### 새 태스크 추가

```bash
# AI를 사용하여 새 태스크 추가
task-master add-task --prompt="Description of the new task"

# 종속성이 있는 태스크 추가
task-master add-task --prompt="Description" --dependencies=1,2,3

# 우선순위가 있는 태스크 추가
task-master add-task --prompt="Description" --priority=high
```

## 기능 세부 정보

### 태스크 복잡성 분석

`analyze-complexity` 명령은 다음을 수행합니다:

- AI를 사용하여 각 태스크를 분석하여 1-10 척도로 복잡성을 평가
- 구성된 DEFAULT_SUBTASKS를 기반으로 최적의 서브태스크 수 권장
- 각 태스크 확장을 위한 맞춤형 프롬프트 생성
- 사용 가능한 명령이 포함된 포괄적인 JSON 보고서 생성
- 기본적으로 보고서를 scripts/task-complexity-report.json에 저장

생성된 보고서에는 다음이 포함됩니다:

- 각 태스크에 대한 복잡성 분석 (1-10점 평가)
- 복잡성에 기반한 권장 서브태스크 수
- 각 태스크에 맞춤화된 AI 생성 확장 프롬프트
- 각 태스크 분석 내에 직접 실행 가능한 확장 명령

### 복잡성 보고서 보기

`complexity-report` 명령은 다음을 수행합니다:

- 복잡성 분석 보고서의 형식이 지정된, 읽기 쉬운 버전 표시
- 복잡성 점수별로 정렬된 태스크 표시 (높은 것부터 낮은 것까지)
- 복잡성 분포 통계 제공 (낮음, 중간, 높음)
- 임계값 점수에 기반하여 확장이 권장되는 태스크 강조
- 각 복잡한 태스크에 대한 사용 가능한 확장 명령 포함
- 보고서가 없는 경우 즉석에서 생성 제안

### 스마트 태스크 확장

`expand` 명령은 자동으로 복잡성 보고서를 확인하고 사용합니다:

복잡성 보고서가 존재할 때:

- 태스크는 권장 서브태스크 수와 프롬프트를 사용하여 자동으로 확장됩니다
- 모든 태스크를 확장할 때, 복잡성 순서대로 처리됩니다 (가장 높은 것부터)
- 복잡성 분석에서 연구 기반 생성이 보존됩니다
- 명시적 명령줄 옵션으로 권장 사항을 재정의할 수 있습니다

워크플로우 예시:

```bash
# 연구 기능이 있는 복잡성 분석 보고서 생성
task-master analyze-complexity --research

# 읽기 쉬운 형식으로 보고서 검토
task-master complexity-report

# 최적화된 권장 사항을 사용하여 태스크 확장
task-master expand --id=8
# 또는 모든 태스크 확장
task-master expand --all
```

### 다음 태스크 찾기

`next` 명령은 다음을 수행합니다:

- 대기 중/진행 중이고 모든 종속성이 충족된 태스크 식별
- 우선순위 수준, 종속성 수, 태스크 ID에 따라 태스크 우선순위 지정
- 선택된 태스크에 대한 종합적인 정보 표시:
  - 기본 태스크 세부 정보 (ID, 제목, 우선순위, 종속성)
  - 구현 세부 정보
  - 서브태스크 (있는 경우)
- 상황에 맞는 제안 작업 제공:
  - 태스크를 진행 중으로 표시하는 명령
  - 태스크를 완료로 표시하는 명령
  - 서브태스크 작업을 위한 명령

### 특정 태스크 세부 정보 보기

`show` 명령은 다음을 수행합니다:

- 특정 태스크 또는 서브태스크에 대한 종합적인 세부 정보 표시
- 태스크 상태, 우선순위, 종속성 및 자세한 구현 노트 표시
- 부모 태스크의 경우, 모든 서브태스크와 상태 표시
- 서브태스크의 경우, 부모 태스크 관계 표시
- 태스크의 상태에 기반한 상황별 작업 제안 제공
- 일반 태스크와 서브태스크 모두에서 작동 (taskId.subtaskId 형식 사용)

## AI 주도 개발을 위한 모범 사례

1. **자세한 PRD로 시작**: PRD가 자세할수록 생성된 태스크가 더 좋아집니다.

2. **생성된 태스크 검토**: PRD를 파싱한 후, 태스크가 의미가 있고 적절한 종속성을 가지고 있는지 확인합니다.

3. **태스크 복잡성 분석**: 복잡성 분석 기능을 사용하여 어떤 태스크를 더 세분화해야 하는지 식별합니다.

4. **종속성 체인 따르기**: 항상 태스크 종속성을 존중합니다 - Cursor 에이전트가 이를 도울 것입니다.

5. **진행하면서 업데이트**: 구현이 계획에서 벗어나면 update 명령을 사용하여 향후 태스크를 현재 접근 방식과 일치시킵니다.

6. **복잡한 태스크 분할**: expand 명령을 사용하여 복잡한 태스크를 관리 가능한 서브태스크로 분할합니다.

7. **태스크 파일 재생성**: tasks.json을 업데이트한 후 태스크 파일을 재생성하여 동기화 상태를 유지합니다.

8. **에이전트에게 컨텍스트 전달**: Cursor 에이전트에게 태스크 지원을 요청할 때, 달성하려는 목표에 대한 컨텍스트를 제공합니다.

9. **종속성 검증**: 주기적으로 validate-dependencies 명령을 실행하여 잘못되거나 순환적인 종속성을 확인합니다.

## Cursor AI 상호 작용 예시

### 새 프로젝트 시작

```
I've just initialized a new project with Claude Task Master. I have a PRD at scripts/prd.txt.
Can you help me parse it and set up the initial tasks?
```

### 태스크 작업

```
What's the next task I should work on? Please consider dependencies and priorities.
```

### 특정 태스크 구현

```
I'd like to implement task 4. Can you help me understand what needs to be done and how to approach it?
```

### 서브태스크 관리

```
I need to regenerate the subtasks for task 3 with a different approach. Can you help me clear and regenerate them?
```

### 변경 사항 처리

```
We've decided to use MongoDB instead of PostgreSQL. Can you update all future tasks to reflect this change?
```

### 작업 완료

```
I've finished implementing the authentication system described in task 2. All tests are passing.
Please mark it as complete and tell me what I should work on next.
```

### 복잡성 분석

```
Can you analyze the complexity of our tasks to help me understand which ones need to be broken down further?
```

### 복잡성 보고서 보기

```
Can you show me the complexity report in a more readable format?
```