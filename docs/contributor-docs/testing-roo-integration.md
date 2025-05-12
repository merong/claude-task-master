# Roo 통합 테스트

이 문서는 Task Master 패키지에서 Roo 통합 테스트를 위한 지침을 제공합니다.

## 테스트 실행

Roo 통합 테스트를 실행하려면:

```bash
# 모든 테스트 실행
npm test

# Roo 통합 테스트만 실행
npm test -- -t "Roo"

# 특정 테스트 파일 실행
npm test -- tests/integration/roo-files-inclusion.test.js
```

## 수동 테스트

Roo 파일이 패키지에 올바르게 포함되어 있는지 수동으로 확인하려면:

1. 테스트 디렉토리 생성:

   ```bash
   mkdir test-tm
   cd test-tm
   ```

2. package.json 파일 생성:

   ```bash
   npm init -y
   ```

3. task-master-ai 패키지를 로컬에 설치:

   ```bash
   # claude-task-master 저장소의 루트에서
   cd ..
   npm pack
   # task-master-ai-0.12.0.tgz와 같은 파일이 생성됩니다

   # 테스트 디렉토리로 돌아가기
   cd test-tm
   npm install ../task-master-ai-0.12.0.tgz
   ```

4. 새 Task Master 프로젝트 초기화:

   ```bash
   npx task-master init --yes
   ```

5. 모든 Roo 파일과 디렉토리가 생성되었는지 확인:

   ```bash
   # .roomodes 파일이 존재하는지 확인
   ls -la | grep .roomodes

   # .roo 디렉토리가 존재하고 모든 모드 디렉토리가 포함되어 있는지 확인
   ls -la .roo
   ls -la .roo/rules
   ls -la .roo/rules-architect
   ls -la .roo/rules-ask
   ls -la .roo/rules-boomerang
   ls -la .roo/rules-code
   ls -la .roo/rules-debug
   ls -la .roo/rules-test
   ```

## 확인 사항

테스트를 실행하거나 수동 검증을 수행할 때 다음 사항을 확인하세요:

1. 패키지가 package.json의 `files` 배열에 `.roo/**`와 `.roomodes`를 포함하는지 확인
2. `prepare-package.js` 스크립트가 모든 필요한 Roo 파일의 존재를 확인하는지 확인
3. `init.js` 스크립트가 필요한 모든 .roo 디렉토리를 생성하고 .roomodes 파일을 복사하는지 확인
4. Roo 통합을 위한 모든 소스 파일이 `assets/roocode/.roo`와 `assets/roocode/.roomodes`에 존재하는지 확인

## 호환성

Roo 통합이 기존 Cursor 기능과 함께 작동하는지 확인:

1. Cursor와 Roo를 모두 사용하는 새 프로젝트 초기화:

   ```bash
   npx task-master init --yes
   ```

2. `.cursor`와 `.roo` 디렉토리가 모두 생성되었는지 확인
3. `.windsurfrules`와 `.roomodes` 파일이 모두 생성되었는지 확인
4. 기존 기능이 예상대로 계속 작동하는지 확인