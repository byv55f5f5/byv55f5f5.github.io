---
layout: post
title:  "React Query 筆記"
date:   2022-10-25 18:34:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: redux react-query rtk-query
---

一直以來都是使用Redux做state management，自然而然的API calls也是使用redux的thunk邏輯來做。
才發現最近興起的使用react-query或rtk-query去做API calls。

> "data fetching and caching" is really a different set of concerns than "state management".

# Redux
是state management的library，專門focus在client side的state management。

## 使用Redux Thunk並將API資料再轉存到store會遇到問題
- 不會幫你確認是否store已經有資料，而避免API calls
- 在useEffect內call API，時機會被拉到functional component mount完後才call
- API資料需要自行放入store中，若同一個資料在不同頁面的slice都會用到，就需要每個slice都去寫extraReducers去更新state

### 我使用Redux開發所遇到的其他問題
- dispatch後，經過多道手續才會更新到component上導致效能會變差(在較大的app會比較明顯)
- 限制了react的設計自由度
  - ex: 必須follow store裡面的型別限制等等
  - ex: 使用react-hook-form，就比較難把store裡面的值拿來當預設值

# React Query / RTK Query
相較於Redux store關注UI的state management，Query Library關注於server side的state，幫助前端去同步server side的state資料。RTK Query是Redux Toolkit所提供的Query功能。
- server side的資料有更新，透過mutation去更新server side資料後，告訴某個tags (or keys)的資料已經無效。需要重新再query一次。
- 若資料未過期，重新再使用useQuery會直接拿到cache資料不需要重新發request
- 在functional component不用等到mount完才call API
