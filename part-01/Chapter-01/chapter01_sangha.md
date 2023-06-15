# Chapter 01

> 자바스크립트에서 타입스크립트로
> 

# 1. Typescript 시작

---

### 1) Node 및 npm 설치 : [https://nodejs.org/ko](https://nodejs.org/ko)

### 2) 설치 확인

```jsx
// Node 버전 확인
node -v

// npm 버전확인
npm -v
```

### 3) Node 프로젝트 시작

```jsx
npm init -y 
```

- 해당 디렉토리가 Node 프로젝트로 전환된다.
- package.json 생성 : Node 프로젝트의 설정 정보가 담겨있다.

### 4) Typescript 프로젝트 시작

```jsx
npm i typescript
```

- node_modules 생성 : Typescript의 라이브러리가 있는 디렉토리
- package-lock.json 생성

### 5) tsc 시작

```jsx
npm tsc --init
```

- tsconfig.json 생성 : Typescript가 코드를 분석할 때 사용하는 설정 파일

### 6) 타입스크립트 코드 분석

```jsx
// 디렉토리 전체 ts 파일 분석
npx tsc

// 특정 파일 코드 분석
npx tsc 파일명
```