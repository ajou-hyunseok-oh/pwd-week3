# 실습 3주차: TypeScript로 기본 계산기 만들기

## 실습 개요

HTML·CSS·TypeScript로 계산기를 구현하며 **화면 구조 → 입력 처리 → 계산 → 화면 갱신**의 연결 과정 익히기. 각 단계에서 파일의 역할을 짚고, 코드를 작성한 뒤 실제 동작과 연결해 확인하기.

### 학습 목표

- HTML과 CSS로 계산기 화면 만들기
- TypeScript 코드를 계산·상태·화면 역할에 따라 세 파일로 작성하기
- TypeScript를 JavaScript로 컴파일하고 브라우저에서 실행하기
- GitHub에 저장하고 GitHub Pages로 배포하기

## 📋 목차

1. [개발 환경 확인과 폴더 만들기](#step-1-개발-환경-확인과-폴더-만들기)
2. [TypeScript 설치와 설정](#step-2-typescript-설치와-설정)
3. [HTML 파일 작성](#step-3-html-파일-작성)
4. [CSS 파일 작성](#step-4-css-파일-작성)
5. [TypeScript 파일 작성](#step-5-typescript-파일-작성)
6. [컴파일하고 브라우저에서 실행하기](#step-6-컴파일하고-브라우저에서-실행하기)
7. [계산기 동작 확인하기](#step-7-계산기-동작-확인하기)
8. [GitHub에 업로드하고 배포하기](#step-8-github에-업로드하고-배포하기)
9. [제출 확인사항](#제출-확인사항)
10. [문제 해결](#문제-해결)

---

## Step 1: 개발 환경 확인과 폴더 만들기

### 1.1 프로그램 확인

Node.js는 TypeScript 컴파일러 실행, npm은 도구 설치와 명령 실행, Git은 작업 기록과 업로드에 사용. 브라우저에서 계산기를 실행하는 환경과 컴파일 환경을 구분하기.

1. 1·2주차에서 사용한 VS Code를 실행하기.
2. 상단 메뉴에서 **Terminal → New Terminal**(터미널 → 새 터미널)을 선택하기.
3. 아래 명령을 한 줄씩 입력하고 Enter를 누르기.

```bash
node -v
npm -v
git --version
```

**확인:** 세 명령에서 각각 버전이 출력되면 준비 완료. 2주차에서 설치한 Node.js와 npm을 그대로 사용하기. 명령을 찾을 수 없다면 해당 프로그램을 설치하고 VS Code를 다시 실행하기. Node.js 설치 파일은 [공식 사이트](https://nodejs.org/)에서 다운로드 가능.

### 1.2 실습 폴더 열기

프로젝트 폴더는 파일 경로와 명령 실행의 기준 위치. HTML이 CSS·JavaScript를 찾는 상대 경로도 이 파일 배치를 기준으로 결정.

1. **File → Open Folder**를 선택하기.
2. Documents 등 원하는 위치에 `pwd-week3` 폴더를 새로 만들기.
3. 해당 폴더를 선택해 열기. 같은 이름의 폴더가 이미 있다면 `pwd-week3-practice`처럼 다른 이름을 사용하기.
4. **Terminal → New Terminal**을 눌러 새 터미널을 열기.
5. 터미널의 현재 경로가 방금 연 폴더로 끝나는지 확인하기.

이후 모든 명령은 이 폴더에서 실행하기. 파일을 만들 때는 왼쪽 **Explorer → New File**을 누르기. 코드를 입력한 뒤 `Ctrl+S`(Windows) 또는 `Cmd+S`(Mac)로 저장하기.

**확인:** VS Code 왼쪽에 실습 폴더가 보이고, 터미널도 같은 폴더를 가리키는지 확인.

> 이 저장소의 파일은 완성본. 처음부터 실습하려면 별도의 빈 폴더에 작성하기. 이미 내려받은 완성본만 실행하려면 해당 폴더에서 `npm ci`, `npm run build`를 실행한 뒤 `index.html`을 열기.

---

## Step 2: TypeScript 설치와 설정

### 2.1 package.json 작성과 설치

`package.json`은 사용할 도구와 실행 명령을 기록하는 파일. `devDependencies`의 TypeScript 버전과 `scripts`의 `check`, `build`, `watch`를 먼저 살펴보기.

1. 프로젝트 폴더에 `package.json` 파일을 만들기.
2. 위에서 짚은 항목을 코드에서 찾아보며 작성·저장하기.

```json
{
  "name": "pwd-week3-calculator",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "build": "tsc",
    "watch": "tsc --watch",
    "check": "tsc --noEmit"
  },
  "devDependencies": {
    "typescript": "5.9.3"
  }
}
```

3. 터미널에서 다음 명령을 실행하기. 최초 설치에는 인터넷 연결이 필요.

```bash
npm install
```

**확인:** `node_modules`에는 실제 설치된 도구, `package-lock.json`에는 설치 버전 정보가 생성됨. 다른 환경에서도 같은 버전으로 설치할 수 있도록 잠금 파일은 Git에 포함.

### 2.2 tsconfig.json 작성

`tsconfig.json`은 컴파일 대상과 결과 위치를 지정하는 파일. `files`는 입력할 세 파일, `outDir`은 출력 폴더, `strict`는 엄격한 타입 검사, `noEmitOnError`는 오류 발생 시 출력 중단 설정.

1. 같은 폴더에 `tsconfig.json` 파일을 만들기.
2. 위에서 짚은 항목을 코드에서 찾아보며 작성·저장하기.

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "none",
    "lib": [
      "ES2020",
      "DOM"
    ],
    "strict": true,
    "noEmitOnError": true,
    "outDir": "js"
  },
  "files": [
    "operations.ts",
    "calculator.ts",
    "app.ts"
  ]
}
```

이 설정은 앞으로 작성할 TypeScript 세 파일을 읽어 `js` 폴더에 JavaScript로 생성. 아직 소스 파일을 만들지 않았으므로 빌드는 Step 6에서 실행하기.

### 2.3 .gitignore 작성

다시 설치할 수 있는 도구와 개인 환경 파일을 저장소에서 제외하기 위한 설정. `node_modules/`는 제외하지만, 배포에 필요한 `js/`는 Git에 포함.

1. 맨 앞의 점을 포함해 `.gitignore` 파일을 만들기.
2. 아래 제외 목록을 작성·저장하기.

```text
node_modules/
.DS_Store
*.tsbuildinfo
```

**확인:** 프로젝트에 `package.json`, `package-lock.json`, `tsconfig.json`, `.gitignore`가 있는지 확인. `node_modules`는 자동 생성된 폴더이므로 직접 수정하지 않기.

---

## Step 3: HTML 파일 작성

### 3.1 index.html 작성

HTML은 표시할 요소와 버튼의 구조 담당. `#display`는 숫자, `#expression`은 계산식, `#message`는 오류를 표시할 자리. 버튼의 `data-key`는 나중에 JavaScript가 읽을 입력값이며 `class`는 CSS에서 모양을 지정할 이름.

1. 프로젝트 폴더에 `index.html` 파일을 만들기.
2. 위에서 짚은 항목을 코드에서 찾아보며 작성·저장하기.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>3주차 · 나의 계산기</title>
  <link rel="stylesheet" href="styles.css">
  <script src="js/operations.js" defer></script>
  <script src="js/calculator.js" defer></script>
  <script src="js/app.js" defer></script>
</head>
<body>
  <main class="calculator" aria-label="계산기">
    <div class="screen">
      <p id="expression" aria-label="계산식"></p>
      <output id="display" aria-label="계산 결과" aria-live="polite">0</output>
      <p id="message" role="status"></p>
    </div>
    <div class="keys">
      <button type="button" data-key="delete" class="utility" aria-label="마지막 숫자 지우기">⌫</button>
      <button type="button" data-key="clear" class="utility" aria-label="전체 초기화">AC</button>
      <button type="button" data-key="percent" class="utility" aria-label="100으로 나누기">%</button>
      <button type="button" data-key="/" class="operator" aria-label="나누기">÷</button>

      <button type="button" data-key="7">7</button>
      <button type="button" data-key="8">8</button>
      <button type="button" data-key="9">9</button>
      <button type="button" data-key="*" class="operator" aria-label="곱하기">×</button>

      <button type="button" data-key="4">4</button>
      <button type="button" data-key="5">5</button>
      <button type="button" data-key="6">6</button>
      <button type="button" data-key="-" class="operator" aria-label="빼기">−</button>

      <button type="button" data-key="1">1</button>
      <button type="button" data-key="2">2</button>
      <button type="button" data-key="3">3</button>
      <button type="button" data-key="+" class="operator" aria-label="더하기">+</button>

      <button type="button" data-key="sign" aria-label="부호 바꾸기">+/−</button>
      <button type="button" data-key="0">0</button>
      <button type="button" data-key="." aria-label="소수점">.</button>
      <button type="button" data-key="=" class="operator" aria-label="계산하기">=</button>
    </div>
  </main>
</body>
</html>
```

### 3.2 화면 구조 확인

1. 파일 탐색기(Windows) 또는 Finder(Mac)에서 실습 폴더를 열기.
2. `index.html`을 더블클릭하여 브라우저로 열기.
3. 숫자 `0`과 버튼 20개가 보이는지 확인하기.

**관찰:** 현재는 HTML만 있으므로 기본 버튼이 보이고 계산은 실행되지 않음. `data-key="+"`는 버튼의 의미를 저장할 뿐, 이 속성만으로 덧셈이 실행되지는 않는다는 점 확인.

---

## Step 4: CSS 파일 작성

### 4.1 styles.css 작성

CSS는 계산 기능과 분리하여 색상·크기·배치를 담당. `.keys`의 Grid 설정으로 네 열을 만들고, 버튼의 `aspect-ratio: 1`과 `border-radius: 50%`를 조합해 원형 버튼 표현.

1. `index.html`과 같은 폴더에 `styles.css` 파일을 만들기.
2. 위에서 짚은 항목을 코드에서 찾아보며 작성·저장하기.

```css
* {
  box-sizing: border-box;
}
body {
  margin: 0;
  min-height: 100vh;
  display: grid;
  place-items: center;
  background: #eceeef;
  font-family: Arial, 'Malgun Gothic', sans-serif;
}
.calculator {
  width: min(100% - 24px, 450px);
  margin: 24px 0;
  padding: 18px;
  border: 1px solid #60676a;
  border-radius: 36px;
  background: #24292c;
  color: #f5f5f5;
}
.screen {
  padding: 20px 4px 8px;
  text-align: right;
}
#expression {
  min-height: 20px;
  margin: 0 0 8px;
  color: #bfc5c8;
  font-size: 16px;
  overflow-wrap: anywhere;
}
#display {
  display: block;
  font-size: clamp(30px, 9vw, 56px);
  overflow-wrap: anywhere;
}
#message {
  min-height: 18px;
  margin: 8px 0;
  font-size: 12px;
  color: #ffb5a4;
}
.keys {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 12px;
}
button {
  width: 100%;
  aspect-ratio: 1;
  border: 1px solid #727c80;
  border-radius: 50%;
  background: #484e50;
  color: #f5f5f5;
  font: normal clamp(24px, 7vw, 42px) Arial, sans-serif;
  cursor: pointer;
  touch-action: manipulation;
}
.utility {
  background: #747a7c;
  border-color: #a0a7aa;
}
.operator {
  background: #ff9500;
  border-color: #ffb52e;
}
button:hover {
  filter: brightness(1.15);
}
button:active {
  filter: brightness(0.85);
}
button:focus-visible {
  outline: 3px solid white;
  outline-offset: 2px;
}
```

### 4.2 계산기 모양 확인

1. 브라우저에서 새로고침하기.
2. 어두운 본체와 원형 버튼이 보이는지 확인하기.
3. 연산 버튼이 주황색이고, 전체 버튼이 4열·5행으로 배치되는지 확인하기.
4. 브라우저 창을 좁혀 버튼이 화면 밖으로 나가지 않는지 확인하기.

**관찰:** HTML 구조가 같아도 CSS 연결로 배치와 색상이 달라짐. `.operator`의 배경색을 잠시 바꿔 해당 버튼만 달라지는지 확인한 뒤 원래 값으로 복구하기. 계산 동작은 Step 5와 Step 6을 마친 뒤 확인.

---

## Step 5: TypeScript 파일 작성

계산 방법, 계산 상태, 화면 처리를 세 파일로 분리하기. 현재 구성은 일반 스크립트가 전역 범위를 공유하므로 파일 분리는 역할 구분이며, HTML의 로딩 순서도 함께 중요. 아래 전체 코드를 작성하면서 **어떤 값을 받고, 무엇을 바꾸며, 어느 함수를 호출하는지** 함께 확인하기.

| 파일 | 역할 |
| --- | --- |
| `operations.ts` | 사칙연산 함수 |
| `calculator.ts` | 숫자 입력과 계산 상태 처리 |
| `app.ts` | 버튼 클릭과 화면 갱신 |

### 5.1 operations.ts 작성

화면과 상태에 의존하지 않고 **숫자 두 개 → 계산 결과**만 담당하는 파일. `Operator`는 허용할 연산 기호, `BinaryOperation`은 연산 함수의 입력·출력 타입. `operations`에서 함수를 선택해 `calculate()`에 전달하는 흐름 찾기.

1. `operations.ts` 파일을 만들기.
2. 위에서 짚은 항목을 코드에서 찾아보며 작성·저장하기.

```typescript
// 사칙연산과 함수형 Strategy: 상태나 DOM을 변경하지 않습니다.
type Operator = '+' | '-' | '*' | '/';
type BinaryOperation = (left: number, right: number) => number;

const add: BinaryOperation = (left, right) => left + right;
const subtract: BinaryOperation = (left, right) => left - right;
const multiply: BinaryOperation = (left, right) => left * right;
const divide: BinaryOperation = (left, right) => {
  if (right === 0) throw new Error('0으로 나눌 수 없습니다.');
  return left / right;
};

// 함수도 값이므로 객체에 보관하고 다른 함수의 인수로 전달할 수 있습니다.
const operations = { '+': add, '-': subtract, '*': multiply, '/': divide };

function calculate(left: number, right: number, operation: BinaryOperation): number {
  return operation(left, right);
}
```

**코드 확인:** `calculate(12, 3, add)`와 `calculate(12, 3, divide)`가 각각 15와 4를 반환하는 과정을 함수 호출 순서로 따라가기. `add`는 함수 전달, `add(12, 3)`는 함수 실행이라는 차이 확인.

### 5.2 calculator.ts 작성

버튼을 누르는 사이에도 첫 번째 숫자와 연산자를 기억해야 하므로 `state`에 계산 상태 보관. `inputDigit()`은 입력, `selectOperator()`는 연산 대기, `equals()`는 계산 완료, `handleKey()`는 키별 동작 선택을 담당.

1. 새 파일 `calculator.ts`를 만들기. 앞의 `operations.ts`와 별도 파일로 저장하기.
2. 위에서 짚은 항목을 코드에서 찾아보며 작성·저장하기.

```typescript
// 계산 상태와 입력 처리: DOM을 직접 다루지 않습니다.
interface CalculatorState {
  input: string;
  stored: number | null;
  operator: Operator | null;
  waiting: boolean;
  hasOperand: boolean;
  error: string;
  expression: string;
}

const state: CalculatorState = {
  input: '0',
  stored: null,
  operator: null,
  waiting: false,
  hasOperand: false,
  error: '',
  expression: '',
};

function formatNumber(value: number): string {
  return Number(value.toPrecision(12)).toString();
}

// 순수 함수: 전달받은 상태를 읽어 문자열을 반환하고 원본은 바꾸지 않습니다.
function pendingExpression(current: CalculatorState): string {
  if (current.stored === null || current.operator === null) return current.expression;
  const symbols = { '+': '+', '-': '−', '*': '×', '/': '÷' };
  const right = current.hasOperand ? ` ${current.input}` : '';
  return `${formatNumber(current.stored)} ${symbols[current.operator]}${right}`;
}

function clear(): void {
  state.input = '0';
  state.stored = null;
  state.operator = null;
  state.waiting = false;
  state.hasOperand = false;
  state.error = '';
  state.expression = '';
}

// 숫자 입력: 문자열 연결과 숫자 덧셈의 차이를 관찰하세요.
function inputDigit(key: string): void {
  if (state.error) clear();
  if (state.operator === null) state.expression = '';
  if (state.waiting) {
    state.input = '0';
    state.waiting = false;
  }
  state.hasOperand = true;
  if (key === '.') {
    if (!state.input.includes('.')) state.input += '.';
    return;
  }
  // 부호와 소수점을 제외하고 직접 입력은 최대 12자리입니다.
  if (state.input.replace(/[-.]/g, '').length >= 12) return;
  if (state.input === '0') state.input = key;
  else if (state.input === '-0') state.input = '-' + key;
  else state.input += key;
}

function setResult(value: number): void {
  if (!Number.isFinite(value)) {
    throw new Error('계산 가능한 숫자 범위를 벗어났습니다.');
  }
  state.input = String(value);
  state.waiting = true;
  state.hasOperand = true;
}

function selectOperator(operator: Operator): void {
  const value = Number(state.input);
  if (state.operator !== null && state.stored !== null && state.hasOperand) {
    setResult(calculate(state.stored, value, operations[state.operator]));
  }
  state.stored = Number(state.input);
  state.operator = operator;
  state.hasOperand = false;
  state.waiting = true;
}

function equals(): void {
  // 두 번째 숫자 없이 =를 누르면 기다립니다. 반복 =는 계산을 반복하지 않습니다.
  if (state.operator === null || state.stored === null || !state.hasOperand) return;
  const right = Number(state.input);
  const formula = pendingExpression(state);
  setResult(calculate(state.stored, right, operations[state.operator]));
  state.stored = null;
  state.operator = null;
  state.expression = `${formula} =`;
}

// 전달받은 키에 따라 상태를 변경하고 계산 오류를 상태에 저장합니다.
function handleKey(key: string): void {
  try {
    if (/^[0-9.]$/.test(key)) inputDigit(key);
    else if (key === 'clear') clear();
    else if (state.error) return;
    else if (key === '+' || key === '-' || key === '*' || key === '/') {
      selectOperator(key);
    } else if (key === '=') equals();
    else if (key === 'delete') {
      if (!state.waiting) {
        state.input = state.input.slice(0, -1);
        if (state.input === '' || state.input === '-') state.input = '0';
      }
    } else if (key === 'sign') {
      if (state.operator === null) state.expression = '';
      if (state.waiting && !state.hasOperand) {
        state.input = '0';
        state.waiting = false;
      }
      state.hasOperand = true;
      state.input = state.input.startsWith('-') ? state.input.slice(1) : '-' + state.input;
    } else if (key === 'percent') {
      if (state.operator === null) state.expression = '';
      setResult(Number(state.input) / 100);
    }
  } catch (error) {
    state.error = error instanceof Error ? error.message : '계산 오류입니다.';
  }
}
```

**코드 확인:** 아래 상태값을 사용하는 조건문을 찾아 입력 흐름과 연결하기.

| 상태값 | 필요한 이유 |
| --- | --- |
| `input: string` | `0.`처럼 아직 입력 중인 소수점과 숫자 연결을 보존 |
| `stored`, `operator` | 첫 번째 숫자와 선택한 연산을 다음 클릭까지 기억 |
| `waiting` | 다음 숫자를 이어 붙일지 새로 입력할지 결정 |
| `hasOperand` | 계산할 두 번째 숫자가 준비되었는지 확인 |
| `error`, `expression` | 화면에 표시할 오류와 완료된 계산식을 보관 |

`pendingExpression(current)`는 상태를 읽어 문자열만 반환하고, `handleKey()`는 상태를 변경한다는 차이 확인. `const state`도 속성 변경은 가능. `Number(state.input)`에서 문자열을 숫자로 바꾸는 위치와 `right === 0`에서 나누기를 막는 위치를 각각 찾아보기.

### 5.3 app.ts 작성

HTML 요소와 계산 로직을 연결하는 파일. 클릭 콜백은 `data-key`를 읽어 `handleKey()`에 전달하고, `render()`는 변경된 상태를 DOM에 반영. 상태 변경과 화면 갱신이 각각 어디에서 일어나는지 구분하기.

1. 새 파일 `app.ts`를 만들기.
2. 위에서 짚은 항목을 코드에서 찾아보며 작성·저장하기.

```typescript
// 화면과 이벤트: 입력을 계산기에 전달하고 바뀐 상태를 화면에 표시합니다.
const display = document.querySelector<HTMLOutputElement>('#display')!;
const expression = document.querySelector<HTMLParagraphElement>('#expression')!;
const message = document.querySelector<HTMLParagraphElement>('#message')!;
const buttons = document.querySelectorAll<HTMLButtonElement>('[data-key]');

function formatDisplay(value: string): string {
  // 정수 부분에만 쉼표를 넣어 입력 중인 소수점과 끝자리 0을 유지합니다.
  const parts = value.split('.');
  if (!value.includes('e')) parts[0] = parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ',');
  return parts.join('.');
}

// 부수 효과: 계산한 문자열을 실제 DOM에 반영하는 부분입니다.
function render(): void {
  const value = state.waiting ? formatNumber(Number(state.input)) : state.input;
  display.textContent = state.error ? 'Error' : formatDisplay(value);
  message.textContent = state.error;
  expression.textContent = pendingExpression(state);
}

buttons.forEach((button) => {
  // 클릭 콜백은 나중에 실행되어도 바깥 범위의 button을 참조합니다(클로저).
  button.addEventListener('click', () => {
    const key = button.dataset.key;
    if (key) {
      handleKey(key);
      render();
    }
  });
});

render();
```

**코드 확인:** 마지막 `render()`는 초기 화면 표시, 클릭 콜백 안의 `render()`는 입력 후 갱신 역할. 콜백이 나중에 실행되어도 바깥의 `button`을 참조하는 부분에서 클로저 확인. DOM 조회의 `!`는 요소가 존재한다고 가정하는 표기이므로 HTML의 id와 일치하는지 확인.

### 5.4 파일 구성 확인

1. **File → Save All**로 모든 파일을 저장하기.
2. 왼쪽 Explorer에서 다음 파일이 같은 폴더에 있는지 확인하기.

```text
pwd-week3/
├── index.html
├── styles.css
├── operations.ts
├── calculator.ts
├── app.ts
├── package.json
├── package-lock.json
├── tsconfig.json
├── .gitignore
└── node_modules/       # npm install로 생성됨
```

**확인:** 파일명이 설정·HTML의 경로와 일치하는지 확인. `operations.ts.txt`처럼 다른 확장자가 붙지 않았는지 확인하기. `js` 폴더는 다음 단계에서 자동으로 생성.

---

## Step 6: 컴파일하고 브라우저에서 실행하기

### 6.1 타입 검사

브라우저에서 실행하기 전에 잘못된 인수 타입이나 속성명을 찾는 단계. 검사를 통과해도 0 나누기 같은 계산 조건은 실행 중 별도 확인 필요.

1. 모든 파일을 저장하기.
2. 프로젝트 터미널에서 실행하기.

```bash
npm run check
```

`npm run check`는 `package.json`의 `tsc --noEmit`을 호출하는 명령. 저장된 TypeScript 파일의 타입을 검사하며, JavaScript 파일은 아직 생성하지 않음.

**확인:** 오류 메시지 없이 명령이 끝나면 다음 단계로 이동하기. 오류가 나오면 표시된 파일명·줄 번호에서 인수 타입, 속성명, 누락된 코드를 확인하고 수정·저장 후 다시 실행하기.

### 6.2 JavaScript 파일 생성

이 프로젝트의 브라우저 실행 파일은 JavaScript. TypeScript 소스를 컴파일해 HTML이 읽을 `.js` 파일 준비하기.

터미널에서 다음 명령을 실행하기.

```bash
npm run build
```

npm이 프로젝트에 설치된 TypeScript 컴파일러 `tsc`를 실행. 컴파일러는 `tsconfig.json`에 지정한 세 파일을 읽고 검사한 뒤, `js` 폴더에 JavaScript를 생성.

```text
직접 작성한 파일       컴파일 명령          생성된 파일
operations.ts   ── npm run build ──→  js/operations.js
calculator.ts   ── npm run build ──→  js/calculator.js
app.ts          ── npm run build ──→  js/app.js
```

**확인:** 왼쪽 Explorer의 `js` 폴더에 위 세 파일이 생성됨. 원본 `.ts` 파일은 그대로 유지됨.

> 빌드에 오류가 있으면 새 JavaScript를 생성하지 않음. 이전에 생성한 파일은 남아 있을 수 있으므로, 파일이 있다는 것뿐 아니라 **이번 빌드가 오류 없이 끝났는지** 확인하기.

### 6.3 변환된 코드 확인

컴파일 과정에서 **검사용 타입 표기는 제거되고 실제 동작은 남는지** 원본과 출력 파일을 나란히 비교하기.

1. VS Code에서 `operations.ts`를 열기.
2. `js/operations.js`도 열어 두 파일의 `add`와 `calculate()`를 비교하기.
3. 아래 차이를 확인하기. 이 단계에서는 코드를 수정하지 않기.

| TypeScript에서 찾을 부분 | 생성된 JavaScript에서 확인할 결과 |
| --- | --- |
| `type BinaryOperation = ...` | 타입 선언이 사라짐 |
| `const add: BinaryOperation = ...` | `const add = ...`로 바뀜 |
| `left: number`, `right: number` | `left`, `right`만 남음 |
| `add` 함수의 `left + right` | 계산 동작이 그대로 남음 |
| `if (right === 0)`과 오류 발생 코드 | 실행 중 검사할 코드가 그대로 남음 |

4. `calculator.ts`와 `js/calculator.js`도 열기. `interface CalculatorState`는 사라지고 `const state` 객체는 남아 있는지 확인하기.

**확인:** 타입 표기는 제거되고, 계산과 상태 처리를 수행하는 코드가 남은 JavaScript 생성. 브라우저의 실제 실행 대상은 이 `.js` 파일.

### 6.4 브라우저에서 실행

1. 파일 탐색기/Finder에서 `index.html`을 더블클릭하기. 이미 열려 있다면 새로고침하기.
2. `1 → 2 → + → 3 → =` 버튼을 차례로 누르기.
3. 큰 숫자는 **15**, 위쪽 계산식은 **12 + 3 =**인지 확인하기.

HTML에 작성한 세 `script` 태그는 `defer`가 있어 HTML을 모두 읽은 후 아래 순서로 실행됨.

```text
index.html 열기
  → js/operations.js: 사칙연산 함수 준비
  → js/calculator.js: 초기 상태와 입력 처리 함수 준비
  → js/app.js: 버튼 이벤트 연결 → render()로 0 표시
  → 버튼 클릭: 입력 처리 → 계산 → 상태 변경 → 화면 갱신
```

이 구성에서 브라우저의 역할은 **빌드로 생성한 `.js` 파일을 읽어 실행**하는 것. `.ts` 컴파일은 앞서 터미널에서 수행하며 버튼 클릭 때는 이미 로드된 JavaScript 함수를 호출. HTML의 script 순서는 유지하기.

### 6.5 브라우저가 읽은 파일 확인

편집한 원본과 브라우저가 실제 실행하는 파일을 구분하는 단계. Sources에서 생성된 JavaScript를 확인하면 빌드 결과가 연결되었는지 직접 확인 가능.

1. 계산기가 열린 Chrome에서 개발자 도구를 열기.
   - Windows: `F12` 또는 `Ctrl+Shift+J`
   - Mac: `Cmd+Option+J`
2. **Sources**(소스) 탭을 선택하기. 탭이 보이지 않으면 `»` 메뉴에서 찾기.
3. 왼쪽 파일 목록에서 `js` 폴더를 펼치기. 찾기 어렵다면 `Ctrl+P`(Windows) 또는 `Cmd+P`(Mac)를 눌러 `operations.js`를 검색하기.
4. `operations.js`, `calculator.js`, `app.js`를 열어 생성된 코드를 확인하기.

**확인:** VS Code에서 생성한 JavaScript 세 파일이 브라우저에서도 열리는지 확인.

### 6.6 수정 후 다시 실행하는 방법

이후 TypeScript 코드를 수정할 때는 다음 순서로 진행하기.

```text
.ts 수정 → 저장 → npm run build → 오류 없이 완료 → 브라우저 새로고침
```

저장할 때마다 자동으로 컴파일하려면 터미널에서 실행하기.

```bash
npm run watch
```

터미널을 열어 둔 채 `.ts` 파일을 저장하고, 오류 0개라는 메시지가 나오면 브라우저를 직접 새로고침하기. 종료할 때는 터미널에서 `Ctrl+C`를 누르기.

`js/` 파일은 직접 수정하지 않기. 다음 빌드에서 덮어써짐. HTML과 CSS만 수정했다면 저장 후 새로고침하기.

---

## Step 7: 계산기 동작 확인하기

### 7.1 기본 계산 확인

각 행을 시작하기 전에 `AC`를 누르기. `12`는 `1`, `2`를 차례로 누르라는 뜻.

| 누를 버튼 | 예상 결과 |
| --- | --- |
| `12 → + → 3 → =` | `15` |
| `12 → − → 3 → =` | `9` |
| `12 → × → 3 → =` | `36` |
| `12 → ÷ → 3 → =` | `4` |
| `0.1 → + → 0.2 → =` | `0.3` |
| `123 → ⌫` | `12` |
| `5 → +/−` | `-5` |
| `50 → %` | `0.5` |
| `6110000` | `6,110,000` |

**확인:** 예상 결과와 비교하면서 숫자 연산은 `operations.ts`, 삭제·부호 전환·%는 `calculator.ts`, 쉼표 표시는 `app.ts`에서 담당하는 부분 찾아보기.

### 7.2 계산식과 초기화 확인

`12 + 3 =`를 누르며 화면과 `calculator.ts`의 상태 변경을 함께 추적하기. 버튼을 누를 때마다 아래 상태가 어떻게 다음 함수에 사용되는지 확인.

| 입력 직후 | `input` | `stored` | `operator` | `waiting` | `hasOperand` |
| --- | --- | --- | --- | --- | --- |
| `12` | `"12"` | `null` | `null` | `false` | `true` |
| `+` | `"12"` | `12` | `"+"` | `true` | `false` |
| `3` | `"3"` | `12` | `"+"` | `false` | `true` |
| `=` | `"15"` | `null` | `null` | `true` | `true` |

1. `AC → 1 → 2 → +`를 누르기. 위쪽에 `12 +`가 표시되는지 확인하기.
2. `3`을 누르기. `12 + 3`으로 바뀌는지 확인하기.
3. `=`를 누르기. 위쪽은 `12 + 3 =`, 결과는 `15`인지 확인하기.
4. 숫자 `7`을 누르기. 계산식이 지워지고 `7`로 새 입력을 시작하는지 확인하기.
5. `AC`를 누르기. 계산식이 지워지고 `0`으로 돌아가는지 확인하기.

### 7.3 오류와 복구 확인

`divide()`의 오류 발생 → `handleKey()`의 `catch` → `state.error` 저장 → `render()`의 오류 표시 순서를 실제 버튼 조작과 연결하기.

1. `AC → 1 → 2 → ÷ → 0 → =`를 누르기.
2. `Error`와 “0으로 나눌 수 없습니다.”가 표시되는지 확인하기.
3. 숫자 `7`을 누르기.
4. 오류 설명이 사라지고 `7`이 표시되는지 확인하기.

### 7.4 연속 입력과 작은 화면 확인

| 누를 버튼 (각 행 전에 AC) | 예상 결과 |
| --- | --- |
| `2 → + → 3 → × → 4 → =` | 입력 순서대로 계산하여 `20` |
| `2 → + → × → 3 → =` | 마지막 연산자가 적용되어 `6` |
| `200 → + → 10 → % → =` | %는 현재 숫자를 100으로 나누므로 `200.1` |
| `2 → + → =` | 두 번째 숫자를 기다리며 `2` 유지 |
| `2 → + → 3 → = → =` | 반복 계산 없이 `5` 유지 |

브라우저 창을 좁혀도 버튼과 표시창이 잘 보이는지 확인하기. Tab으로 버튼에 초점을 옮긴 뒤 Enter 또는 Space로 버튼을 누를 수 있는지도 확인하기.

> 직접 입력은 최대 12자리이며 결과는 유효 숫자 12자리까지 표시. `⌫`는 직접 입력 중인 숫자만 삭제. 결과가 나온 뒤 새 숫자를 누르면 새 입력을 시작.

---

## Step 8: GitHub에 업로드하고 배포하기

### 8.1 README.md 작성

다른 사람이 코드를 실행하고 구조를 이해할 수 있도록 프로젝트 사용법과 실습 관찰 결과 기록하기.

1. 프로젝트 폴더에 `README.md`를 만들기. 이미 이 안내 문서가 있다면 그대로 두고 자신의 실습 결과를 아래에 추가하기.
2. 계산기 소개, 실행 명령(`npm install`, `npm run build`, `index.html` 열기), Step 7에서 확인한 결과와 해당 동작을 담당한 파일·함수를 기록하기.
3. 저장하기.

### 8.2 GitHub 저장소 만들기

로컬 작업을 원격에 보관하고 공유할 공간 준비. 소스 저장소 주소와 계산기가 실행되는 Pages 주소는 서로 다름.

1. [GitHub](https://github.com)에 로그인하기.
2. 우측 상단 **+ → New repository**를 선택하기.
3. Repository name에 `pwd-week3`를 입력하기. 같은 이름의 저장소가 있다면 다른 이름을 사용하기.
4. **Public**을 선택하기.
5. 로컬에 파일이 있으므로 README, .gitignore, license 자동 추가 항목은 선택하지 않기.
6. **Create repository**를 누르기.
7. 생성된 저장소의 HTTPS 주소를 복사하기.

주소 예: `https://github.com/yourname123/pwd-week3.git`

### 8.3 빌드하고 커밋하기

컴파일 결과까지 최신 상태로 맞춘 뒤 작업 기록 남기기. `git add`는 기록할 변경 선택, `git commit`은 선택한 변경을 하나의 버전으로 저장.

프로젝트 폴더의 터미널에서 다음 명령을 순서대로 실행하기. 검사나 빌드에서 오류가 나면 수정한 뒤 다시 실행하기.

```bash
npm run check
npm run build
git init
git add .
git status
```

`git status`에 TypeScript 세 파일과 **js 폴더의 JavaScript 세 파일**이 포함되는지 확인하기. `node_modules`가 보이면 `.gitignore`를 확인하고 업로드하기 전에 해결하기.

```bash
git commit -m "feat: 기본 계산기 완성"
git branch -M main
```

### 8.4 GitHub에 업로드하기

`origin`은 원격 저장소의 이름, `push`는 로컬 커밋을 해당 저장소로 전송하는 명령.

1. 아래 주소를 **8.2에서 복사한 본인 저장소 주소**로 바꾸기.
2. 명령을 실행하기.

```bash
git remote add origin https://github.com/yourname123/pwd-week3.git
git push -u origin main
```

> 완성본을 clone한 경우에는 origin이 이미 있음. `git remote -v`로 확인하고, 위의 `git remote add` 대신 `git remote set-url origin 본인저장소주소`를 실행한 후 push하기.

3. GitHub 저장소 페이지를 새로고침하기.
4. `index.html`, `styles.css`, TypeScript 세 파일, `js` 폴더가 올라왔는지 확인하기.

**확인:** `js` 폴더 안에 `operations.js`, `calculator.js`, `app.js`가 모두 있고, `node_modules`는 없음.

### 8.5 GitHub Pages 설정

저장소의 HTML·CSS·JavaScript를 웹 주소로 제공하기 위한 설정. 이번 구성은 브라우저가 실행할 파일을 그대로 게시하므로 빌드한 `js/`도 업로드 필요.

1. 자신의 저장소에서 **Settings → Pages**로 이동하기.
2. **Build and deployment → Source**에서 **Deploy from a branch**를 선택하기.
3. Branch는 **main**, 폴더는 **/ (root)**를 선택하기.
4. **Save**를 누르기.
5. 배포가 완료될 때까지 기다리기. 진행 상태는 **Actions**에서 확인 가능.
6. Pages 화면에 표시된 사이트 링크를 열기.

주소 예: `https://yourname123.github.io/pwd-week3/`

본인의 사용자 이름과 저장소 이름에 따라 주소가 달라짐. [GitHub Pages 게시 설정 안내](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)

### 8.6 배포 결과 확인

1. 공개된 사이트에서 `12 + 3 =`를 눌러 `15`가 나오는지 확인하기.
2. 휴대폰에서도 같은 주소를 열어 계산해 보기.
3. 저장소 URL과 Pages URL을 복사해 두기.

**확인:** 로컬 파일을 열었을 때와 같은 계산기가 공개 주소에서도 작동하는지 확인. 이번 배포 설정은 TypeScript를 컴파일하지 않으므로, 로컬에서 생성한 `js/` 파일을 반드시 함께 업로드하기.

이후 수정한 내용을 올릴 때는 다음 순서를 사용하기. 커밋 메시지는 실제 변경 내용에 맞게 바꾸기.

```bash
npm run check
npm run build
git add .
git commit -m "style: 버튼 색상 수정"
git push
```

---

## 제출 확인사항

### ✅ 체크리스트

- [ ] HTML·CSS와 TypeScript 세 파일 작성하기.
- [ ] 타입 검사와 빌드가 오류 없이 끝나는지 확인하기.
- [ ] js 폴더의 JavaScript 세 파일과 원본의 차이 확인하기.
- [ ] Step 7의 계산·오류·초기화 동작과 담당 코드 연결하기.
- [ ] GitHub에 최신 소스와 js 폴더 업로드하기.
- [ ] GitHub Pages에서 계산기 작동 확인하기.
- [ ] README에 소개·실행 방법·관찰한 동작과 담당 코드를 기록하기.

### 📝 제출 정보

LMS에 다음 두 주소를 제출하기. `yourname123`은 본인의 GitHub 사용자 이름으로 바꾸기.

1. GitHub 저장소 URL: `https://github.com/yourname123/pwd-week3`
2. GitHub Pages URL: `https://yourname123.github.io/pwd-week3/`

저장소 이름을 다르게 사용했다면 주소도 맞춰 바꾸기. 제출 기한은 수업 공지를 확인하기.

---

## 문제 해결

| 증상 | 해결 방법 |
| --- | --- |
| npm을 찾을 수 없음 | Node.js 설치 후 VS Code와 터미널을 다시 열기. |
| PowerShell에서 npm.ps1 오류 | `npm.cmd install`, `npm.cmd run build`처럼 실행하기. |
| package.json을 찾을 수 없음 | VS Code에서 실습 폴더를 열고 터미널을 새로 열기. |
| JSON 오류 | 큰따옴표, 쉼표, 닫는 괄호를 예제와 비교하기. |
| TypeScript 파일을 찾을 수 없음 | Step 5의 세 파일을 모두 저장했는지, tsconfig.json의 이름과 일치하는지 확인하기. |
| 타입 검사·빌드 오류 | 오류에 표시된 파일과 줄을 Step 5의 코드와 비교하여 수정하기. |
| 버튼이 반응하지 않음 | 빌드 성공 여부와 js 폴더의 파일 세 개를 확인하기. |
| 정의되지 않은 함수·변수 오류 | HTML의 script 순서가 operations.js → calculator.js → app.js인지 확인하기. |
| 수정한 내용이 반영되지 않음 | .ts 저장 → 빌드 성공 → 브라우저 새로고침 순서로 진행하기. |
| 화면 스타일이 적용되지 않음 | styles.css 파일명과 HTML의 link 경로를 확인하기. |
| commit에서 사용자 정보를 요구함 | 1주차처럼 `git config --global user.name "본인 이름"`, `git config --global user.email "본인 이메일"`을 설정하기. |
| node_modules가 Git 추가 목록에 있음 | .gitignore를 저장하고 `git rm -r --cached node_modules`로 Git 목록에서만 제외하기. 이후 `git add .`를 다시 실행하기. |
| origin already exists | `git remote -v`로 확인하고 `git remote set-url origin 본인저장소주소`로 수정하기. |
| push 인증 실패 | 1주차에 사용한 `gh auth login`으로 다시 로그인하기. |
| Pages가 열리지 않음 | 배포 완료 여부, main/root 설정, index.html 위치를 확인하기. |
| 배포 후 버튼이 반응하지 않음 | js 폴더의 최신 파일 세 개가 모두 업로드되었는지 확인하기. |
