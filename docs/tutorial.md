# Task Master 튜토리얼

이 튜토리얼은 AI 주도 개발을 위한 Task Master 설정 및 사용 방법을 안내합니다.

## 초기 설정

Task Master를 설정하는 두 가지 방법이 있습니다: MCP 사용(권장) 또는 npm 설치를 통한 방법.

### 옵션 1: MCP 사용 (권장)

MCP(Model Control Protocol)는 에디터에서 직접 Task Master를 시작하는 가장 쉬운 방법을 제공합니다.

1. **패키지 설치**

```bash
npm i -g task-master-ai
```

2. **IDE/MCP 클라이언트에 MCP 설정 추가** (Cursor가 권장되지만 다른 클라이언트에서도 작동합니다):

```json
{
	"mcpServers": {
		"taskmaster-ai": {
			"command": "npx",
			"args": ["-y", "--package=task-master-ai", "task-master-ai"],
			"env": {
				"ANTHROPIC_API_KEY": "YOUR_ANTHROPIC_API_KEY_HERE",
				"PERPLEXITY_API_KEY": "YOUR_PERPLEXITY_API_KEY_HERE",
				"OPENAI_API_KEY": "YOUR_OPENAI_KEY_HERE",
				"GOOGLE_API_KEY": "YOUR_GOOGLE_KEY_HERE",
				"MISTRAL_API_KEY": "YOUR_MISTRAL_KEY_HERE",
				"OPENROUTER_API_KEY": "YOUR_OPENROUTER_KEY_HERE",
				"XAI_API_KEY": "YOUR_XAI_KEY_HERE",
				"AZURE_OPENAI_API_KEY": "YOUR_AZURE_KEY_HERE"
			}
		}
	}
}
```

**중요:** 사용하려는 각 AI 제공자에 대한 API 키가 _필요_합니다. `task-master models` 명령을 실행하여 .env 및 mcp.json 전체에서 선택한 모델과 API 키 상태를 확인하세요.

**CLI에서 AI 명령을 사용하려면** .env 파일에 API 키가 있어야 합니다.
**MCP에서 AI 명령을 사용하려면** .mcp.json 파일(또는 MCP 구성 동등물)에 API 키가 있어야 합니다.

두 곳 모두에 키를 두고 API 키가 git에 체크인되지 않도록 mcp.json을 gitignore에 추가하는 것이 좋습니다.

3. **에디터 설정에서 MCP 활성화**

4. **AI에게 Task Master 초기화 요청**:

```
Can you please initialize taskmaster-ai into my project?
```

AI는 다음을 수행합니다:

- 필요한 프로젝트 구조 생성
- 초기 구성 파일 설정
- 나머지 과정을 안내

5. PRD 문서를 `scripts/` 디렉토리에 배치합니다 (예: `scripts/prd.txt`)

6. **자연어 명령**을 사용하여 Task Master와 상호 작용:

```
Can you parse my PRD at scripts/prd.txt?
What's the next task I should work on?
Can you help me implement task 3?
```

### 옵션 2: 수동 설치

명령줄 인터페이스를 직접 사용하는 것을 선호한다면:

```bash
# 전역으로 설치
npm install -g task-master-ai

# 또는 프로젝트 내에 로컬로 설치
npm install task-master-ai
```

새 프로젝트 초기화:

```bash
# 전역으로 설치한 경우
task-master init

# 로컬로 설치한 경우
npx task-master init
```

이 명령은 프로젝트 세부 정보를 입력하라는 메시지를 표시하고 필요한 파일과 구조로 새 프로젝트를 설정합니다.

## 일반 명령어

Task Master를 설정한 후에는 다음 명령어를 사용할 수 있습니다(AI 프롬프트 또는 CLI를 통해):

```bash
# PRD를 파싱하고 태스크 생성
task-master parse-prd your-prd.txt

# 모든 태스크 나열
task-master list

# 다음에 작업할 태스크 표시
task-master next

# 태스크 파일 생성
task-master generate
```

## Cursor AI 통합 설정

Task Master는 [Cursor AI](https://www.cursor.so/)와 원활하게 작동하도록 설계되어 AI 주도 개발을 위한 구조화된 워크플로우를 제공합니다.

### MCP로 Cursor 사용 (권장)

이미 Cursor에서 MCP로 Task Master를 설정한 경우 통합은 자동입니다. 자연어를 사용하여 Task Master와 상호 작용할 수 있습니다:

```
What tasks are available to work on next?
Can you analyze the complexity of our tasks?
I'd like to implement task 4. What does it involve?
```

### 수동 Cursor 설정

MCP를 사용하지 않는 경우에도 여전히 Cursor 통합을 설정할 수 있습니다:

1. 프로젝트를 초기화한 후 Cursor에서 엽니다
2. `.cursor/rules/dev_workflow.mdc` 파일은 Cursor에 의해 자동으로 로드되어 AI에 태스크 관리 시스템에 대한 지식을 제공합니다
3. PRD 문서를 `scripts/` 디렉토리에 배치합니다 (예: `scripts/prd.txt`)
4. Cursor의 AI 채팅을 열고 Agent 모드로 전환합니다

### Cursor에서 대체 MCP 설정

Cursor 설정에서 MCP 서버를 설정할 수도 있습니다:

1. Cursor 설정으로 이동
2. MCP 섹션으로 이동
3. "Add New MCP Server" 클릭
4. 다음 세부 정보로 구성:
   - 이름: "Task Master"
   - 유형: "Command"
   - 명령: "npx -y --package=task-master-ai task-master-ai"
5. 설정 저장

구성이 완료되면 Cursor의 인터페이스를 통해 직접 Task Master의 태스크 관리 명령과 상호 작용할 수 있어 더 통합된 경험을 제공합니다.

## 초기 태스크 생성

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

에이전트는 다음을 수행합니다:

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
We've decided to use MongoDB instead of PostgreSQL. Can you update all future tasks (from ID 4) to reflect this change?
```

에이전트는 다음을 실행합니다:

```bash
task-master update --from=4 --prompt="Now we are using MongoDB instead of PostgreSQL."

# 또는 MongoDB에 대한 모범 사례를 찾기 위해 연구가 필요한 경우:
task-master update --from=4 --prompt="Update to use MongoDB, researching best practices" --research
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

구성된 연구 모델을 사용한 연구 기반 서브태스크 생성:

```
Please break down task 5 using research-backed generation.
```

에이전트는 다음을 실행합니다:

```bash
task-master expand --id=5 --research
```

## Cursor AI 상호작용 예시

### 새 프로젝트 시작하기

```
I've just initialized a new project with Claude Task Master. I have a PRD at scripts/prd.txt.
Can you help me parse it and set up the initial tasks?
```

### 태스크 작업하기

```
What's the next task I should work on? Please consider dependencies and priorities.
```

### 특정 태스크 구현하기

```
I'd like to implement task 4. Can you help me understand what needs to be done and how to approach it?
```

### 서브태스크 관리하기

```
I need to regenerate the subtasks for task 3 with a different approach. Can you help me clear and regenerate them?
```

### 변경 사항 처리하기

```
We've decided to use MongoDB instead of PostgreSQL. Can you update all future tasks to reflect this change?
```

### 작업 완료하기

```
I've finished implementing the authentication system described in task 2. All tests are passing.
Please mark it as complete and tell me what I should work on next.
```

### 복잡성 분석하기

```
Can you analyze the complexity of our tasks to help me understand which ones need to be broken down further?
```

### 복잡성 보고서 보기

```
Can you show me the complexity report in a more readable format?
```