# Cursor AI 상호작용 예시

Task Master를 사용할 때 Cursor AI와의 일반적인 상호작용 예시입니다:

## 새 프로젝트 시작하기

```
I've just initialized a new project with Claude Task Master. I have a PRD at scripts/prd.txt.
Can you help me parse it and set up the initial tasks?
```

## 태스크 작업하기

```
What's the next task I should work on? Please consider dependencies and priorities.
```

## 특정 태스크 구현하기

```
I'd like to implement task 4. Can you help me understand what needs to be done and how to approach it?
```

## 서브태스크 관리하기

```
I need to regenerate the subtasks for task 3 with a different approach. Can you help me clear and regenerate them?
```

## 변경 사항 처리하기

```
We've decided to use MongoDB instead of PostgreSQL. Can you update all future tasks to reflect this change?
```

## 작업 완료하기

```
I've finished implementing the authentication system described in task 2. All tests are passing.
Please mark it as complete and tell me what I should work on next.
```

## 복잡성 분석하기

```
Can you analyze the complexity of our tasks to help me understand which ones need to be broken down further?
```

## 복잡성 보고서 보기

```
Can you show me the complexity report in a more readable format?
```

### 복잡한 태스크 분할하기

```
Task 5 seems complex. Can you break it down into subtasks?
```

(에이전트 실행: `task-master expand --id=5`)

```
Please break down task 5 using research-backed generation.
```

(에이전트 실행: `task-master expand --id=5 --research`)

### 연구를 통한 태스크 업데이트

```
We need to update task 15 based on the latest React Query v5 changes. Can you research this and update the task?
```

(에이전트 실행: `task-master update-task --id=15 --prompt="Update based on React Query v5 changes" --research`)

### 연구를 통한 태스크 추가

```
Please add a new task to implement user profile image uploads using Cloudinary, research the best approach.
```

(에이전트 실행: `task-master add-task --prompt="Implement user profile image uploads using Cloudinary" --research`)