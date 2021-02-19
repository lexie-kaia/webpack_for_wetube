# Webpack For Wetube

webpack config description for youtube clone coding course in nomad coder

<br>

😭유튜브 클론 코딩 강의의 **4.WEBPACK 파트**를 넘어가기 위해 만든 자료입니다.

새롭게 배우는 내용과 수많은 에러로 고통받는 강의지만...  
그만큼 많이 배우고 있는 강의라서  
중간에 큰 산 처럼 있는 WEBPACK때문에 포기하시는 분이  
한 분이라도 적어지기를 바라며...정리한 내용입니다.😭

강의 뿐만 아니라 다른 공식 자료들을 참고하여 만들었기 때문에  
강의의 코드와 다른 부분이 있을 수도 있습니다.

설명에 미숙한 부분이 있을 수도 있으니 참고한 자료를 덧붙입니다.  
혹시 오탈자나 잘못된 내용이 있으면 알려주세요~
그럼 모두 화이팅!!!

<br>

### 직접적인 참고자료

- [webpack document](https://webpack.js.org/concepts/)

### 삽질에 도움이 된 자료

- [웹팩 핸드북](https://joshua1988.github.io/webpack-guide/guide.html)
- 김정환블로그 [프론트엔드 개발환경의 이해](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html)
- Youtube 생활코딩 [Webpack](https://www.youtube.com/watch?v=cp_MeXO2fLg&list=PLuHgQVnccGMChcT9IKopFDoAIoTA-03DA)

<br>
<br>

## 목차

1. 웹팩 설치하기
2. 웹팩을 설정하는 `webpack.config.js` 파일 만들기
3. `package.json`에 webpack을 실행하는 script 생성하기
4. `entry`와 `output` 설정하기
5. loader(`module`) 설정
6. `babel-loader` 추가하기
7. `sass-loader` 추가하기
8. 웹팩 `watch` 실행하기

<br>

## 1. 웹팩 설치하기

- `npm i -D webpack webpack-cli`

- 설치할 파일 목록
  - [ ] `webpack`
  - [ ] `webpack-cli` 콘솔에서 명령어로 webpack을 실행할 수 있게 하는 파일

<br>

## 2. 웹팩을 설정하는 `webpack.config.js` 파일 만들기

- `webpack.config.js` 파일은 <u>웹팩이 실행되는 환경</u>을 설정하는 파일입니다.

- 설정해야하는 웹팩 속성 5가지

  1. **entry**  
     웹팩이 자원을 번들링하기 위해 접근하는 <u>최초의 자바스크립트 파일 경로</u> 설정
  2. **output**  
     <u>아웃풋 파일의 경로와 파일 이름</u> 설정
  3. **loader(module)**  
     웹팩이 최신버전의 자바스크립트나 자바스크립트 파일이 아닌 웹 자원(HTML, CSS, 이미지, 폰트 등)들을 해석하고 변환할 수 있도록 도와주는 속성
  4. **plugin**  
     웹팩의 기본적인 동작 외에 추가적인 기능을 제공하는 속성
  5. **mode**  
     실행 모드, `none`, `developement`(테스트 및 개발용), `production`(프로덕션 배포용)이 설정값으로 있음

<br>

## 3. `package.json`에 webpack을 실행하는 script 생성하기

```json
//package.json

  "scripts": {
    "dev:assets": "webpack --mode=development",
    "build:assets": "webpack --mode=production"
  },
```

- `--mode=<mode>`를 이용하여 웹팩의 mode를 설정하여 실행할 수 있습니다.
- `npm run dev:assets`, `npm run build:assets` 커맨드로 script를 실행할 수 있습니다.

<br>

## 4. entry와 output 설정하기

1. **웹팩 설정을 하기 이전에 <u>웹팩을 적용할 테스트 파일</u>을 만듭니다.**

- assets 디렉토리 안에
- `js` 폴더와 `main.js` 파일 생성 `assets/js/main.js`
- `scss` 폴더와 `styles.scss` 파일 생성 `assets/scss/styles.scss`

2. **`main.js` 파일 안에 `styles.scss` 파일을 `import`합니다.**

```js
// main.js

import '../scss/styles.css';
```

3. **웹팩 설정 파일 `webpack.config.js`에 entry와 output을 설정합니다.**

- 엔트리 파일과 아웃풋 파일의 경로를 지정하기 위하여 `path` 모듈(파일의 경로와 관련된 node.js 모듈)을 `import`합니다.
  - `path.resolve()`  
    인자로 받은 경로들을 묶어 새로운 절대 경로를 반환합니다.
  - `__dirname`  
    현재 파일이 위치한 디렉토리를 나타냅니다. (webpack.config.js파일의 현재 경로)

```js
// webpack.config.js

const path = require('path');

const ENTRY_FILE = path.resolve(__dirname, 'assets', 'js', 'main.js');
const OUTPUT_DIR = path.resolve(__dirname, 'static');

const config = {
  //웹팩이 자원을 번들링하기 위해 접근하는 최초의 자바스크립트 파일(엔트리 파일) 경로 설정
  entry: ENTRY_FILE,
  // 아웃풋 파일의 경로와 파일 이름 설정
  output: {
    filename: '[name].js',
    path: OUTPUT_DIR,
  },
};

// 웹팩 실행 시, 웹팩이 설정에 접근할 수 있도록 exports함
module.exports = config;
```

<br>

## 5. loader(module) 설정하기

- 로더는 `module`이라는 속성 안에 `rules` 배열에 추가합니다.

```js
const config = {
  entry: ENTRY_FILE,
  output: {
    filename: '[name].js',
    path: OUTPUT_DIR,
  },
  // module: 웹팩이 최신버전의 자바스크립트나 자바스크립트 파일이 아닌 웹 자원(HTML, CSS, 이미지, 폰트 등)들을 해석하고 변환할 수 있도록 도와주는 속성
  module: {
    rules: [
      // loader의 구성
      // {
      //   test: ..., (로더를 적용할 파일 유형의 정규식 표현)
      //   use: ..., (적용할 로더의 이름과 옵션)
      // }
      // { babel-loader 추가하기 }
      // { sass- loaser 추가하기 }
    ],
  },
};
```

<br>

## 6. babel-loader 추가하기

- 웹팩 공식 문서 [webpack babel-loader](https://webpack.js.org/loaders/babel-loader/)

1. **babel-loader 설치하기**  
   `npm i -D babel-loader`

   - babel-loader가 실행되기 위해 필요한 파일
   - [ ] `@babel/core`
   - [ ] `@babel/preset-env`

2. **babel-loader 설정하기**

```js
const config = {
  entry: ENTRY_FILE,
  output: {
    filename: '[name].js',
    path: OUTPUT_DIR,
  },
  // module: 웹팩이 최신버전의 자바스크립트나 자바스크립트 파일이 아닌 웹 자원(HTML, CSS, 이미지, 폰트 등)들을 해석하고 변환할 수 있도록 도와주는 속성
  module: {
    rules: [
      {
        // test: 로더를 적용할 파일 유형의 정규식 표현
        test: /\.m?js$/,
        // use: 적용할 로더의 이름과 옵션
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
          },
        },
      },
      // { sass- loaser 추가하기 }
    ],
  },
};
```

<br>

## 7. sass-loader 추가하기

- 웹팩 공식 문서
  - [webpack sass-loader](https://webpack.js.org/loaders/sass-loader/)
  - [webpack postcss-loader](https://webpack.js.org/loaders/postcss-loader/)
  - [webpack MiniCssExtractPlugin](https://webpack.js.org/plugins/mini-css-extract-plugin/)
  - [autoprefixer github](https://github.com/postcss/autoprefixer)

1. **package 설치하기**

   - [ ] sass-loader  
          `npm i -D sass-loader sass`
         (`sass`도 같이 설치해야 합니다.)
   - [ ] postcss-loader  
          `npm i -D postcss-loader postcss`
         (`postcss`도 같이 설치해야 합니다.)
   - [ ] autoprefixer  
          `npm i -D autoprefixer`
   - [ ] css-loader  
          `npm i -D css-loader`
   - [ ] mini-css-extract-plugin  
          `npm i -D mini-css-extract-plugin`
   - 위의 모든 파일을 설치해야 합니다.

2. **loader 설정하기**

```js
const config = {
  entry: ENTRY_FILE,
  output: {
    filename: '[name].js',
    path: OUTPUT_DIR,
  },
  // plugins: 웹팩의 기본적인 동작 외에 추가적인 기능을 제공하는 속성
  // 웹팩은 기본적으로 관련된 모든 파일을 하나의 자바스크립트 파일로 번들링합니다.
  // MiniCssExtractPlugin은 자바스크립트 파일에서 별도의 css 파일을 추출하는 플러그인입니다.
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'styles.css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.m?js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
          },
        },
      },
      {
        test: /\.s[ac]ss$/i,
        // loader는 아래->위 순서로 실행됩니다. (sass->postcss->css->MiniCssExtract)
        use: [
          // JS에서 별도의 css 파일 추출
          // 플러그인 설정에서 지정된 'styles.css'로 추출됩니다.
          MiniCssExtractPlugin.loader,
          // CSS를 CommonJS로 변환합니다.
          'css-loader',
          // postcss의 autoprefixer 기능을 이용하여
          // '벤더 프리픽스(vendor prefix)'를 자동으로 생성합니다.
          // postcss의 옵션은 'postcss.config.js'에 별도로 지정
          'postcss-loader',
          // Sass를 CSS로 변환합니다.
          'sass-loader',
        ],
      },
    ],
  },
};
```

3. **postcss-loader 설정하기**

   - `postcss.config.js` 파일 만들기  
     webpack의 `postcss-loader`는 자동으로 postcss의 설정 파일인 `postcss.config.js`을 참고합니다.

   ```js
   // postcss.config.js

   module.exports = {
     plugins: [require('autoprefixer')],
   };
   ```

   - `package.json` 파일의 `browserslist` 속성 추가하기  
     autoprefixer는 자동으로 `package.json`의 `browserslilst` 속성을 참고합니다.
     babel, eslint와 타겟 브라우저 설정을 공유하기 위해 이 방법이 권장됩니다.

   ```json
   // package.json

   "browserslist": [
     "cover 99.5%"
   ]
   ```

<br>

## 8. 웹팩 watch 실행하기

- `package.json` scripts에 `--watch`를 추가합니다.

```json
  "scripts": {
    "dev:assets": "webpack --mode=development --watch",
  },
```
