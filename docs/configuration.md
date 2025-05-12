# 설정

Task Master는 두 가지 주요 설정 방법을 사용합니다:

1.  **`.taskmasterconfig` 파일 (프로젝트 루트 - 대부분의 설정에 권장)**

    - 이 JSON 파일은 AI 모델 선택, 매개변수, 로깅 수준 및 프로젝트 기본값을 포함한 대부분의 설정을 저장합니다.
    - **위치:** 이 파일은 `task-master models --setup` 대화형 설정을 실행할 때 프로젝트 루트 디렉토리에 생성됩니다. 일반적으로 초기화 시퀀스 중에 이 작업을 수행합니다. 모델에 따라 Temperature와 Max Tokens를 조정하는 것 외에는 이 파일을 수동으로 편집하지 마세요.
    - **관리:** `task-master models --setup` 명령(또는 `models` MCP 도구)을 사용하여 이 파일을 대화식으로 생성하고 관리하세요. `task-master models --set-<role>=<model_id>`를 사용하여 특정 모델을 직접 설정할 수도 있으며, 사용자 지정 모델의 경우 `--ollama` 또는 `--openrouter` 플래그를 추가할 수 있습니다. 구조를 이해하지 않는 한 수동 편집은 가능하지만 권장하지 않습니다.
    - **예시 구조:**
      ```json
      {
      	"models": {
      		"main": {
      			"provider": "anthropic",
      			"modelId": "claude-3-7-sonnet-20250219",
      			"maxTokens": 64000,
      			"temperature": 0.2
      		},
      		"research": {
      			"provider": "perplexity",
      			"modelId": "sonar-pro",
      			"maxTokens": 8700,
      			"temperature": 0.1
      		},
      		"fallback": {
      			"provider": "anthropic",
      			"modelId": "claude-3-5-sonnet",
      			"maxTokens": 64000,
      			"temperature": 0.2
      		}
      	},
      	"global": {
      		"logLevel": "info",
      		"debug": false,
      		"defaultSubtasks": 5,
      		"defaultPriority": "medium",
      		"projectName": "Your Project Name",
      		"ollamaBaseUrl": "http://localhost:11434/api",
      		"azureOpenaiBaseUrl": "https://your-endpoint.openai.azure.com/"
      	}
      }
      ```

2.  **환경 변수 (`.env` 파일 또는 MCP `env` 블록 - API 키만 해당)**
    - 민감한 API 키와 특정 엔드포인트 URL에 **전적으로** 사용됩니다.
    - **위치:**
      - CLI 사용: 프로젝트 루트에 `.env` 파일을 생성합니다.
      - MCP/Cursor 사용: `.cursor/mcp.json` 파일의 `env` 섹션에 키를 구성합니다.
    - **필요한 API 키 (구성된 제공자에 따라 다름):**
      - `ANTHROPIC_API_KEY`: Anthropic API 키
      - `PERPLEXITY_API_KEY`: Perplexity API 키
      - `OPENAI_API_KEY`: OpenAI API 키
      - `GOOGLE_API_KEY`: Google API 키
      - `MISTRAL_API_KEY`: Mistral API 키
      - `AZURE_OPENAI_API_KEY`: Azure OpenAI API 키 (`AZURE_OPENAI_ENDPOINT`도 필요)
      - `OPENROUTER_API_KEY`: OpenRouter API 키
      - `XAI_API_KEY`: X-AI API 키
    - **선택적 엔드포인트 재정의 (.taskmasterconfig에서):**
      - `AZURE_OPENAI_ENDPOINT`: Azure OpenAI 키를 사용하는 경우 필수
      - `OLLAMA_BASE_URL`: 기본 Ollama API URL 재정의 (기본값: `http://localhost:11434/api`)

**중요:** 모델 ID 선택(`main`, `research`, `fallback`), `maxTokens`, `temperature`, `logLevel`, `defaultSubtasks`, `defaultPriority` 및 `projectName`과 같은 설정은 환경 변수가 아닌 **`.taskmasterconfig`에서 관리**됩니다.

## `.env` 파일 예시 (API 키용)

```
# .taskmasterconfig에 구성된 제공자에 필요한 API 키
ANTHROPIC_API_KEY=sk-ant-api03-your-key-here
PERPLEXITY_API_KEY=pplx-your-key-here
# OPENAI_API_KEY=sk-your-key-here
# GOOGLE_API_KEY=AIzaSy...
# 기타

# 선택적 엔드포인트 재정의
# AZURE_OPENAI_ENDPOINT=https://your-azure-endpoint.openai.azure.com/
# OLLAMA_BASE_URL=http://custom-ollama-host:11434/api
```

## 문제 해결

### 설정 오류

- Task Master가 누락된 설정에 대한 오류를 보고하거나 `.taskmasterconfig`를 찾을 수 없는 경우, 프로젝트 루트에서 `task-master models --setup`을 실행하여 파일을 생성하거나 수정하세요.
- API 키가 `.env` 파일(CLI용) 또는 `.cursor/mcp.json`(MCP용)에 올바르게 배치되어 있고 `.taskmasterconfig`에서 선택한 제공자에 대해 유효한지 확인하세요.

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