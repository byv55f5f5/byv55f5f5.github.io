---
layout: post
title:  "Redux與React Query 比較筆記"
date:   2022-10-25 18:34:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: redux react-query rtk-query
---

原本專案一直都只有使用Redux做state management，自然而然的API calls也是使用redux的thunk邏輯來做。
才發現最近興起的使用react-query或rtk-query去做API calls。

記錄一下運作方法及優缺點。

# Redux
是state management的library，專門focus在client side的state management。

## Pros
- 易於跨component傳遞資料
- state變為可預測性 (reducer都是pure function的關係)
- 因為可預測性，而易於測試
- 有devtool可以隨時觀察state狀態

## Cons
- 資料未封裝
- dispatch後，經過多道手續才會更新到component上導致效能會變差(在較大的app會比較明顯)
- 限制了react的設計自由度
  - ex: 必須follow store裡面的型別限制等等
  - ex: 使用react-hook-form，就比較難把store裡面的值拿來當預設值
- 不會幫你確認是否store已經有資料，而避免API calls(跟React Query & RTK Query比較)

# React Query or RTK Query
關注於server side的state，幫助前端去同步server side的state資料。server side的資料有更新，透過mutation去更新server side資料後，
告訴某個tags (or keys)的資料已經無效。需要重新再query一次。
- 若資料未過期，重新再使用useQuery會直接拿到cache資料不需要重新發request

(working...)

