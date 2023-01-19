---
layout: post
title:  "Vue typescript 筆記"
date:   2023-01-19 18:00:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: vue typescript
---

## Error

1. computed沒加上type
會導致typescript認不出其他的props or data的type

```plain
Property 'selections' does not exist on type 'CombinedVueInstance<Vue, unknown, unknown, unknown, Readonly<{ devices: device[]; actionDevices: actionDevice[]; }>>'
```
