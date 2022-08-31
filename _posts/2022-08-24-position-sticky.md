---
layout: post
title:  "Header Fixed Table"
date:   2022-08-25 15:46:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: html css
---

一直以來如何讓Table的Header固定在最上方，這個問題困擾了我好久。

從前，我都是將Header以及Body分成兩個Table。並將`table-layout`設定成`fixed`。並個別設定每一列(column)的寬度。

所幸這次[TailwindCSS](https://tailwindcss.com/)的v3.1的更新文件，意外地讓我知道`position: sticky;`這個屬性。

只要在th上設定`position: sticky;`，還要記得top, bottom, left, right等位置屬性。指定header要黏在哪裡。

需要注意的是，有sticky屬性的話，祖先元素必須有一個是具有scrollbar機制的。

這樣的話有sticky的元素就會在使用top等屬性指定的位置被"黏住"

<div style="height:100px;overflow-y:scroll;">
  <table>
    <thead>
      <tr>
        <th style="position:sticky;top:0px;z-index:10;background:#000;">Header 1</th>
        <th style="position:sticky;top:0px;z-index:10;background:#000;">Header 2</th>
        <th style="position:sticky;top:0px;z-index:10;background:#000;">Header 3</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Data 1</td>
        <td>Data 2</td>
        <td>Data 3</td>
      </tr>
      <tr>
        <td>Data 1</td>
        <td>Data 2</td>
        <td>Data 3</td>
      </tr>
      <tr>
        <td>Data 1</td>
        <td>Data 2</td>
        <td>Data 3</td>
      </tr>
    </tbody>
  </table>
</div>

```html
<div>
  <table>
    <thead>
      <tr>
        <th>Header 1</th>
        <th>Header 2</th>
        <th>Header 3</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Data 1</td>
        <td>Data 2</td>
        <td>Data 3</td>
      </tr>
      <tr>
        <td>Data 1</td>
        <td>Data 2</td>
        <td>Data 3</td>
      </tr>
      <tr>
        <td>Data 1</td>
        <td>Data 2</td>
        <td>Data 3</td>
      </tr>
    </tbody>
  </table>
</div>
```

```css
table thead tr th {
  position: sticky;
  top: 0px;
  z-index: 10;
  background: #000;
}

div {
  height: 100px;
  overflow-y: scroll;
}
```