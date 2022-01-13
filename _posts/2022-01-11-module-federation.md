---
layout: post
title:  "Webpack Module Federation"
date:   2022-01-11 20:00:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: webpack module federation
---
Webpack 5內建plugin [ModuleFederationPlugin](https://webpack.js.org/plugins/module-federation-plugin/)，其功能為匯出exposed出專案部分javascript code，供其他專案使用，並指定有哪些package需要共用。如此便可降低程式及library重複。

# Module Federation用途
專案可匯出其部分javascript code包括components，並import到其他專案:
  - Host專案使用專案A的App Component
  - Host專案使用專案B的Monitor Component
  - Host專案使用專案C的Map Component
  - Host專案使用專案D的某一個JS util file

## Options
### name
將專案expose出去後，root路徑的名稱

### filename
將專案expose出去後，build出來的檔案名稱

### remotes
指定該專案需要用到哪些remote專案。

等同於直接在該專案的index.html加入 `<script src="${remote_path_and_file_name}"></script>`

example: `<script src="http://localhost:8081/remoteEntry.js"></script>`

### shared
指定哪一些用到的packages需要與其他的專案共享。

例如React在同一個頁面下不能匯入兩次以上，若有兩個federation專案都使用React就必須在shared加入，並加入[Shared hints](https://webpack.js.org/plugins/module-federation-plugin/#sharing-hints) - singleton: true。

## Example
Host Setting:
```js
// webpack.config.js
new ModuleFederationPlugin({
  name: 'host',
  // 指定import那些remote
  remotes: {
    qss: 'qss@http://localhost:9001/remoteEntry.js',
  },
  // 指定那些package與其它專案shared
  shared: { react: { singleton: true }, 'react-dom': { singleton: true } },
})
```

Remote Setting:
```js
new ModuleFederationPlugin({
	name: 'qss',
	filename: 'remoteEntry.js',
  // 指定要expose那些js
	exposes: {
		'./qss': './src/app/App',
	},
	shared: { react: { singleton: true }, 'react-dom': { singleton: true } },
})
```

Host Import Remote:
```jsx
// App.js
import React, { Suspense } from "react";
// 使用 ${remote_name}/${expose_js_name} import
const RemoteApp = React.lazy(() => import("qss/qss"));

const App = () => {
  return (
    <div>
      <div
        style={{
          margin: "10px",
          padding: "10px",
          textAlign: "center",
          backgroundColor: "greenyellow",
        }}
      >
        <h1>App1</h1>
      </div>
      <Suspense fallback={"loading..."}>
        <RemoteApp />
      </Suspense>
    </div>
  );
};

export default App;
```

# 實作
## Redux 設定
當host與remote皆存在redux時，兩個redux會使用不同的react context，所以會互相隔離。
### 合併store實驗
以下實作如何將redux merge合併。但其實實務上應該很少會使用到這個方法。
[redux reducer injection example](https://github.com/module-federation/module-federation-examples/tree/master/redux-reducer-injection)

Host的export store需要重新處理，寫一個function，開放merge store。
```js
const createReducer = (asyncReducer) => {
  return combineReducers({
    ...staticReducer,
    ...asyncReducer,
  });
};

export default function handleStore(initialState) {
  const store = configureStore({ reducer: createReducer() });

  store.asyncReducer = {};

  store.injectReducer = (key, asyncReducer) => {
    store.asyncReducer[key] = asyncReducer;
    store.replaceReducer(createReducer(store.asyncReducer));
  };

  return store;
}

export const store = handleStore();
```

並在import remote時傳進remote components中。
```jsx
// App.jsx
import React, { Suspense } from 'react';
import { Provider } from 'react-redux';

import 'regenerator-runtime';

import Header from './components/Header';
import Panel from './components/Panel';

import { store } from './store';

const Remote1 = React.lazy(() => import('remote1/App'));

const App = () => {
  return (
    <Provider store={store}>
      <div>
        <Header />
        <Panel />
        <div>
          <h3>From Remote 1</h3>
          <div
            style={{
              margin: '10px',
              padding: '10px',
              width: '60%',
              border: '2px solid black',
            }}
          >
            <Suspense fallback="Loading">
              <Remote1 store={store} />
            </Suspense>
          </div>
        </div>
    </Provider>
  );
};

export default App;
```

在remote app收到store後，需要將自己的reducer inject進host的store

```jsx
const AppWrapper = ({ store }) => {
  useEffect(() => {
    store.injectReducer('remote', reducer);
  }, []);

  return <Provider store={store || {}}><App /></Provider>;
};
```

#### selector
remote app 需要重新撰寫如:

`(state) => state.main.appName;`

需改成

`(state) => state.remote.main.appName;`

## Vue 設定
### Vue in Vue (vue-cli + Vue 3)
webpack 設定
- 新增Module Federation Plugin在vue.config.js
- @vue/cli-service等vue cli tool需使用版本: 5.0.0-beta.3
- 須注意vue.config.js中publicPath的設定值

必須將vue: singleton加入shared設定中，不然會跳出錯誤 **[Vue warn]: Invalid VNode type: Symbol(Text) (symbol)**

解法出處: [\[Vue warn\]: Invalid VNode type: Symbol\(Text\) \(symbol\) · Issue #2913 · vuejs/vue-next (github.com)](https://github.com/vuejs/vue-next/issues/2913)

Remote setting:
```js
// vue.config.js
const ModuleFederationPlugin =
  require('webpack').container.ModuleFederationPlugin;

module.exports = {
  publicPath: 'http://localhost:8082/',
  configureWebpack: {
    plugins: [
      new ModuleFederationPlugin({
        name: 'remote2',
        filename: 'remoteEntry.js',
        exposes: {
          './HelloWorld': './src/components/HelloWorld',
          './Remote': './src/components/Remote',
        },
        shared: {
          vue: { singleton: true },
        },
      }),
    ],
    devServer: {
      port: 8082,
    },
  },
};
```

Host setting:
```js
// vue.config.js
const ModuleFederationPlugin =
  require('webpack').container.ModuleFederationPlugin;

module.exports = {
  publicPath: 'http://localhost:8083/',
  configureWebpack: {
    plugins: [
      new ModuleFederationPlugin({
        name: 'host',
        remotes: {
          remote2: 'remote2@http://localhost:8082/remoteEntry.js',
        },
        shared: {
          vue: { singleton: true },
        },
      }),
    ],
    devServer: {
      port: 8083,
    },
  },
};
```

import至app

在Vue 3，必須使用 `defineAsyncComponent` 否則會出現警告: **[Vue warn]: Invalid VNode type: undefined (undefined)**

```js
export default {
  name: 'App',
  components: {
    HelloWorld,
    remote: defineAsyncComponent(() => import('remote2/Remote')),
  }
}
```

### Vue in React (Vue cli + Vue 3)
expose的Vue component，需要export vueApp.mount() method出來，並在react中調用該method。

```js
// bootstrap.js
import { createApp } from 'vue';
import App from './App.vue';

const mount = (el) => {
  const app = createApp(App);
  app.mount(el);
};

export { mount };
```

```jsx
// VueApp.jsx
import { mount } from 'remote2/App';
import React, { useRef, useEffect } from 'react';

export default () => {
  const ref = useRef(null);

  useEffect(() => {
    mount(ref.current);
  }, []);

  return <div ref={ref} />;
};
```

# Reference
- [Module Federation \| webpack](https://webpack.js.org/concepts/module-federation/)
- [ModuleFederationPlugin \| webpack](https://webpack.js.org/plugins/module-federation-plugin/)
- [Webpack5 federation plugin. 大家好，我是奶綠茶 今天來分享 Webpack5 超強的新功能... \| by Milk Midi \| Medium](https://milkmidi.medium.com/webpack5-federation-plugin-841c680672ea)
- [Webpack.js Module Federation Example - StackBlitz](https://stackblitz.com/github/webpack/webpack.js.org/tree/master/examples/module-federation?terminal=start&terminal=)
- [Module Federation (github.com)](https://github.com/module-federation)
- [esplito/mfe-react-vue-module-federation-example: Example of how Webpack 5 Module Federation can be used to have microfrontends with different frameworks. (React + Vue) Based on Stephen Grider's course on this topic on Udemy. (github.com)](https://github.com/esplito/mfe-react-vue-module-federation-example)

