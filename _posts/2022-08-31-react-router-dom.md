---
layout: post
title:  "react router dom備忘錄"
date:   2022-08-31 15:08:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: react-router-dom react nginx
---

使用react-router-dom時，因為它是直接使用history API幫我們替換url。所以browser不會去重新load資源。
但假設你的網頁domain為: `https://my-domain.com/` 被react-router-dom切換成 `https://my-domain.com/my-page` 時，此時去嘗試reload頁面，會因為browser嘗試去存取 `my-page.html` 而失敗。
此時可以在nginx加入以下設定:

```
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    try_files $uri $uri/ /index.html; # 需加入這行

    add_header Access-Control-Allow-Origin *;
}
```

加入try_files，如果嘗試讀取 `$uri` 跟 `$uri/` 找不到東西，則會導過去 `/index.html`。

[官方文件](http://nginx.org/en/docs/http/ngx_http_core_module.html#try_files)是說可以加入更多嘗試path，如 `try_files file1 file2 file3 ... uri;`。

但加入try_files後，也只會讓有第一層path的url如: `https://my-domain.com/my-page` 可以reload而已。若多了一層path，像是: `https://my-domain.com/my-page/layer-2` ，browser確實會得到index.html，但html裡面的script的src假如為以下:

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <title>Qrux</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" href="favicon.ico">
  <script defer src="index.bundle.js"></script>
</head>

<body>
  <div id="root"></div>
</body>

</html>
```

會發現browser會去嘗試去存取 `https://my-domain.com/my-page/index.bundle.js` 然後，又得到404 not found。
> 如果index.html的script src是絕對路徑該有多好。

其實webpack的output.publicPath就可以用來達成這個目的。如下:

```js
{
  output: {
    // path,
    filename: 'index.bundle.js',
    publicPath: '/', // 加入這個設定
  },
  devServer: {
    // ...
    historyApiFallback: true, // 使用webpack-dev-server加入這個設定，就如同try_files
  },
}
```

從此以後不管再多層，都可以reload成功。

> 為什麼不能直接手動去修改index.html?

因為我的index.html是使用[HtmlWebpackPlugin](https://webpack.js.org/plugins/html-webpack-plugin/)。
而且在某些用法如: code splitting，在js檔案裏面也會去load其他的js，就也會導致類似的問題。