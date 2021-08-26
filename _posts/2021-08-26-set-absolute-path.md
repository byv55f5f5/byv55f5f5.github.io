---
layout: post
title:  "設定專案使用絕對路徑"
date:   2021-08-26 23:10:00 +0800
categories: javascript
author: "愛喝茶的熊"
tags: javascript es2015 webpack eslint
---
往往在專案中，都會使用相對路徑(relative path)來import module，例如:
```jsx
import Header from "../features/Header";

const App = () => (
  <div className="app">
    <Header />
  </div>
);
```
偶爾會寫到component放在很深的資料夾底下，此時就需要寫很多的`../../../`。
然而最近學到可以透過設定來使用絕對路徑(absolute path)來import module，就會變成:
```jsx
import Header from "features/Header";

const App = () => (
  <div className="app">
    <Header />
  </div>
);
```

首先，編輯webpack.config.js加入`modules`設定，假如你的程式碼都放在`src/`底下:
```js
// webpack.config.js
module.exports = {
  // ...
  resolve: {
    // ...
    modules: [path.resolve(__dirname, 'src'), 'node_modules'],
    // ...
  },
  // ...
};
```
其實主要的設定就只有這樣。但因為開發上會使用到eslint，此時編輯器就會被劃惱人的紅線。

這時候我們就要去改`eslintrc.js`，首先需要確保已經安裝了`eslint-plugin-import`
```sh
yarn add --dev eslint-plugin-import
```
然後在`eslintrc.js`新增以下設定
```js
// eslintrc.js
// ...
settings: {
  'import/resolver': {
    node: {
      extensions: ['.js', '.jsx'],
      moduleDirectory: ['node_modules', 'src'],
    }
  }
}
// ...
```

最後，若你是使用vscode，可以去編輯`jsconfig.json`來增加vscode的intellisense，如下
```json
{
  "compilerOptions": {
    "baseUrl": "src"
  }
}
```

接下來就可以安心的使用絕對路徑import module啦