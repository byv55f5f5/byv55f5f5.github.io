---
layout: page
title: About
permalink: /about/
---

# 許驛涵 (Charlie)
<img src="https://upload.cc/i1/2022/08/27/GVQhNE.png" width="300px">

A web developer.
- 📍 Taipei, Taiwan.
- 👦 男 1991-02-09
- 📫 byv55f5f5@gmail.com
- 🛠 ReactJS & Redux

## 簡歷 Summary
我畢業於台北科技大學自動化科技所，畢業後利用研發替代役制度進入了威聯通科技(QNAP)。研替結束後繼續任職於該公司，主要負責網頁前端程式開發。
大學畢業專題是用Java Swift開發UI，用來操作PLC，實現一個腳踏車停車系統。
研究所時開發出使用人工智慧管理家電狀態的IoT架構，並可使用網頁下達指令。
在威聯通時期則專攻Web前端開發。
網頁開發時主要使用的框架是React.js & Redux。
熟悉Webpack打包程式，了解使用Webpack Federation實現micro-frontend設定。
熟悉Git操作，熟悉GitLab CI流程。
熟悉Docker操作，熟悉基本的K8S操作部屬及撰寫其resource。
偶爾也會接觸到Web後端的小案子，所以對後端程式也能稍微理解。所用到的後端程式為python、PHP、NodeJS。

* * *

## 工作經驗 Professional Experience
### Senior Software Engineer in 威聯通科技 (QNAP) 2015-10 - 在職中
在該QNAP擔任研發替代役，研替結束後繼續留任該公司。主要擔任Web UI前端程式工程師。

#### 前期
到QNAP後，第一個負責的專案是網頁直播應用。後被轉調部門。開始維護及開發NAS網路設定的主要設定介面。所以能熟悉網通相關的名詞及運作。

##### Network & Virtual Switch
此應用是用來設定NAS的網路的一頁式網站，包刮IPv4、IPv6、Gateway、VLAN、Virtual Switch (Bridge)等。
主要開發框架是使用jQuery去動態render頁面，本來為部門資深同事起頭，後由我接手，並使用[AngularJSv1](https://angularjs.org/)去改善UI效率的問題。
使用[gulp](https://gulpjs.com/)將部分程式碼minify。
與公司ART團隊一同設計產品機器圖SVG，並可對之進行互動。
此頁面為NAS網路介面的拓樸圖，是用jQuery去計算每個元件的位置，並用SVG去畫線連結各個元件。
![Network & Virtual Switch](https://upload.cc/i1/2022/08/27/lcVC4X.png)

##### QIoT
此應用是以NAS來當主要中樞，並與其他家電實現IoT架構。是當時QNAP主要的主力產品之一。與另一名前端工程師協力開發。
此應用開始使用ReactJS框架開發網頁，當時使用的資料流框架是[Flux](https://facebook.github.io/flux/)
![QIoT](https://upload.cc/i1/2022/08/30/1URWSC.jpg)

#### 中期
在公司熟悉環境及人脈之後。開始偏向自己與PM、後端工程師、美術溝通，開發應用的前端網頁UI。

##### McAfee
QNAP產品所使用的防毒軟體。此專案是我第一個自己從零開始開發的案子，也開始獨立開發，自己跟PM、後端RD、ART討論溝通進度。
此專案開始嘗試使用[Redux](https://redux.js.org/)資料流架構，並指導同部門前端RD使用。
開始嘗試自行撰寫webpack config，區分dev / production mode。
![McAfee](https://upload.cc/i1/2022/08/30/HPpqB0.png)

##### QNE Network Manager
此專案原先也包含圖形化設定介面，當時使用[react-draggable](https://github.com/react-grid-layout/react-draggable)以及操作svg畫圖實作。
![QNE Network Manager](https://upload.cc/i1/2022/08/30/lV7rX8.png)

##### Service Composer
Service Composer是使用GoJS開發的圖形化設定介面。
此時開始嘗試使用[redux toolkit](https://redux-toolkit.js.org/)，並介紹給同事使用。
![Service Composer](https://upload.cc/i1/2022/08/30/L8yzoq.png)

##### QSS (QNAP Switch System)
此案子原為其他部門使用Vue.js撰寫的。我們的工作是透過閱讀Vue.js的source code並把它改成ReactJS的架構。
開始使用[echarts](https://echarts.apache.org/zh/index.html)繪製各種統計圖。
![QSS](https://upload.cc/i1/2022/08/30/SzM0D6.png)

#### 近期
此時轉調部門，開始開發雲端服務，並回歸與其他前端工程師同時開發。

##### Qrux (未上市)
Qrux是QNAP計畫開發專門用來集中管理QNAP產品的雲端服務平台。
為了User方便定位產品位置，初次使用[MapboxGL](https://docs.mapbox.com/mapbox-gl-js/guides/)去操作地圖相關功能。初期使用公司預設的style時，產生效能問題，因此去挑整將地圖上不需要顯示的東西給取消，才得以解決效能問題。
使用CSS position:sticky; 屬性將table的header黏貼置頂，謝謝IE終於退休。
本專案我除了開發前端Web以外，還跟主管共同討論了雲端服務程式的架構。因此，我學會了使用docker、撰寫k8s resource、撰寫GitLab CI流程達成自動部署

* * *
## 專業技能
### Web UI developing - Expert
Front-end Development
- React.js
- Redux
- Sass / SCSS / TailwindCSS
- AngularJSv1
- Basic SVG painting
- MapboxGL / echarts

Front-end bundle tools
- Webpack
- gulp

Test Case Writing
- JEST with testing library
- Selenium with Python

### Others
- Docker操作
- Git / GitLab CICD
- k8s resources

### Backend developing - Beginner
- PHP
- Python
- NodeJS
- MySQL
- MongoDB

* * *

## 學歷
### 國立台北科技大學 2013-06 ~ 2015-08 自動化科技所
- 碩士論文 - 以專家系統及時間暫態邏輯 實現智慧綠建築之環境控制
  - Java、C++、PHP、MySQL、Javascript

### 國立台北科技大學 2009-09 ~ 2013-06 電機工程系
- 畢業專題 - 校園自動化自行車停車場之網路遠端監控系統
  - Java
