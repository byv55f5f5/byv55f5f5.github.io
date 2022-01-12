---
layout: post
title:  "使用FormData及fetch API上傳檔案"
date:   2021-08-14 17:25:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: javascript formdata php html
---
這幾天幫公司部門寫了一個excel轉JS檔案的簡易UI系統。用途為將各專案管理字串的excel檔案，轉換成特定格式的JS檔案。

同事希望可以順便備份上傳的excel檔案至NAS上，所以就用到了FormData及fetch API。其實之前已經實做過類似的功能，但這次使用到又必須到處翻過去的專案來找code很麻煩，就記錄在這裡好了。

先在HTML宣告一個 input:file元素

```html
<input type="file" name="file" id="xlsx-file" accept=".xlsx" />
```

上傳檔案的事件處理

```js
const formData = new FormData();
const fileInput = document.getElementById('xlsx-file');

formData.set('file', fileInput.files[0]);

// const headers = new Headers({
//   Content-Type: 'multipart/form-data'
// });

fetch('path/to/api', {
  method: 'POST',
  body: formData,
  // headers, we don't need to add header ourself.
});
```

formData.set()也可以置換成formData.append()。兩者的區別只是formData.set()會覆蓋該key原有的值，而append如果遇到一樣的key則會新增一組一樣key的值在formData最尾端。

值得注意的是，我們**不需要**特別設定Content-Type: multipart/form-data。需要由Browser幫你做設定，因為還需要加上boundary資訊。

最後是php file。但php不熟，幾乎都是從網路上抓下來的。主要就是使用$_FILES及move_uploaded_file來將檔案移到自己想要的folder。另外想特別提到ini_set可以直接把cache disable真的非常方便，一直到這次使用到才知道這個方便的功能。

```php
<?php
// disabled php cache
ini_set("opcache.enable", "0");
$target_dir = "path/to/save/file";
$target_file = $target_dir.basename($_FILES["file"]["name"]);
if(isset($_FILES["file"])) {
  if(file_exists($target_file)) {
    // change the file permissions if allowed
    chmod($target_file, 0755);
    // remove the file
    if (unlink($target_file)) {
      echo "Replace old file\n";
    }
   }
  if (move_uploaded_file($_FILES["file"]["tmp_name"], $target_file)) {
    echo "The file ".htmlspecialchars(basename($_FILES["file"]
"name"]))." has been uploaded.";
  } else {
    echo "Sorry, there was an error uploading your file.";
  }
}
?>
```