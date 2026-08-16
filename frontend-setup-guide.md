# 프론트엔드 프로젝트 설정 가이드

이 문서는 React, TypeScript, Tailwind CSS, Shadcn/ui 환경을 처음 접하는 팀원을 위한 설정 가이드입니다.  
가이드를 따라 순서대로 설정을 진행하면 개발 환경을 구축할 수 있습니다.  

---

## 1. 개발 환경 요구사항

프로젝트를 실행하기 위해 컴퓨터에 다음 소프트웨어가 설치되어 있어야 합니다.  

* **Node.js**: v22.x (LTS) 버전 이상 권장  
* **Package Manager**: npm (Node.js 설치 시 기본 제공)  
* **IDE**: VS Code 권장  

---

## 2. 프로젝트 초기 설정 방법

저장소를 클론한 뒤 아래의 단계를 진행합니다.  

### 가. 의존성 패키지 설치
프로젝트 루트 디렉토리에서 터미널을 열고 다음 명령어를 입력합니다.  
```bash
npm install
```
이 명령어는 `package.json`에 명시된 React, TypeScript, Tailwind CSS 등의 모든 패키지를 설치합니다.  

### 나. 개발 서버 실행
설치가 완료되면 다음 명령어로 로컬 개발 서버를 실행합니다.  
```bash
npm run dev
```
실행 후 터미널에 나타나는 로컬 주소(예: `http://localhost:5173`)로 접속하여 화면이 정상적으로 출력되는지 확인합니다.  

---

## 3. 핵심 기술 스택 활용 가이드

우리 프로젝트에서 사용하는 핵심 기술의 기본 사용법입니다.  

### 가. React와 TypeScript
TypeScript는 JavaScript에 타입을 지정하여 에러를 사전에 방지하도록 돕습니다.  

1) **컴포넌트 작성 방식**  
   모든 React 컴포넌트는 `.tsx` 확장자를 사용하며 함수형 컴포넌트로 작성합니다.  

2) **타입 정의 예시**  
   컴포넌트의 Props나 데이터 모델은 `interface`를 사용하여 타입을 정의합니다.  
   ```tsx
   interface BookProps {
     title: string;
     author: string;
     isAvailable: boolean;
   }

   export function BookItem({ title, author, isAvailable }: BookProps) {
     return (
       <div>
         <h3>{title}</h3>
         <p>저자: {author}</p>
         <span>{isAvailable ? "대여 가능" : "대여 중"}</span>
       </div>
     );
   }
   ```

### 나. Tailwind CSS
Tailwind CSS는 HTML 클래스 내에서 스타일을 빠르게 적용할 수 있는 유틸리티 CSS 프레임워크입니다.  

1) **자주 사용하는 클래스 예시**  
   * **레이아웃**: `flex`, `grid`, `justify-between`, `items-center`  
   * **여백**: `p-4` (padding 1rem), `m-2` (margin 0.5rem)  
   * **색상**: `bg-blue-500` (배경색), `text-gray-700` (글자색)  
   * **반응형**: `md:flex-row` (중간 크기 화면 이상에서 가로 정렬)  

2) **적용 예시**  
   ```tsx
   <div className="flex flex-col p-4 bg-white rounded-lg shadow-md hover:shadow-lg transition-shadow">
     <h1 className="text-xl font-bold text-gray-900">도서 정보</h1>
     <p className="text-sm text-gray-500 mt-1">대여 현황을 확인하세요.</p>
   </div>
   ```

### 다. Shadcn/ui 컴포넌트 추가 및 사용
Shadcn/ui는 설치형 라이브러리가 아니라 컴포넌트 코드를 직접 다운로드하여 프로젝트 내에서 커스텀할 수 있게 지원합니다.  

1) **컴포넌트 추가 방법**  
   새로운 UI 컴포넌트(예: 버튼, 다이얼로그)가 필요할 때는 아래 명령어를 통해 소스코드를 직접 가져옵니다.  
   ```bash
   npx shadcn@latest add [컴포넌트명]
   # 예: Button 컴포넌트 추가
   npx shadcn@latest add button
   ```
   가져온 컴포넌트 파일은 `src/components/ui/` 디렉토리에 자동으로 생성됩니다.  

2) **컴포넌트 사용 방법**  
   추가된 컴포넌트는 `@/components/ui/` 경로를 통해 불러와 사용합니다.  
   ```tsx
   import { Button } from "@/components/ui/button"

   export default function App() {
     return (
       <div>
         <Button variant="default">대여하기</Button>
       </div>
     )
   }
   ```

---

## 4. 자주 발생하는 오류 해결 (Troubleshooting)

### 가. `@/` 경로 인식을 못 하는 경우
가져온 컴포넌트 임포트 경로의 `@` 기호에서 빨간 밑줄 에러가 발생하는 현상입니다.  
* **원인**: IDE가 TypeScript 경로 설정을 새로고침하지 못해 발생합니다.  
* **해결법**: `tsconfig.app.json`과 `tsconfig.json` 파일에 `paths` 설정이 올바르게 되어 있는지 확인하고, VS Code의 경우 `Ctrl + Shift + P`를 누른 뒤 `TypeScript: Restart TS Server`를 실행합니다.  

### 나. 스타일 클래스를 넣었는데 화면에 반영이 안 되는 경우
Tailwind CSS의 특정 클래스가 렌더링에 적용되지 않는 현상입니다.  
* **원인**: 빌드 시 스타일을 파싱할 대상 파일의 경로가 누락되었을 수 있습니다.  
* **해결법**: `tailwind.config.js` 파일의 `content` 배열 안에 아래와 같이 경로가 등록되어 있는지 확인합니다.  
  ```javascript
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  ```

### 다. Shadcn UI 초기화 및 컴포넌트 추가 시 에러가 발생하는 경우
`Could not load the workspace config` 오류 메시지가 출력되며 다운로드가 정지되는 현상입니다.  
* **원인**: CLI 도구가 프로젝트 루트의 `tsconfig.json` 파일에서 경로 설정을 읽지 못했기 때문입니다.  
* **해결법**: 프로젝트 루트의 `tsconfig.json` 파일의 `references` 아래에 `compilerOptions`가 정상적으로 포함되어 있는지 확인합니다.  
  ```json
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
  ```
