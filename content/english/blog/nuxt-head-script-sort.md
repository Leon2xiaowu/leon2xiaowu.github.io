---
title: "Nuxt 3 useHead 如何自定义 script 标签渲染顺序"
meta_title: ""
description: "Nuxt 3 使用 useHead 的 tagPriority 属性自定义 script 标签渲染顺序。"
date: 2024-06-30T01:42:09.525Z
image: "/images/blog-cover/nuxt-head-script-sort.jpg"
categories: ["Technology","Front end"]
author: "hbb"
tags: ["technology"]
draft: false
---

Nuxt 中我们使用 [useHead](https://nuxt.com/docs/api/composables/use-head) 等一系列方法在 html 中渲染 script 内容，一些时候我们需要确保某些script以正确的顺序渲染并执行，那如何确保渲染顺序呢？

Nuxt 的 useHead 能力是由 [Unhead](https://unhead.unjs.io/) 提供的，其提供了[Tag Sorting](https://unhead.unjs.io/usage/guides/sorting)的能力。

Unhead 默认会按照一定的标签权重进行排序，想要进行自定义排序，可以通过这两种方式。

## 通过数字设置优先级

您可以使用 tagPriority 属性为标签设置自定义优先级。

提供一个数字将把优先级设置为该数字。数字越小，优先级越高。

您还可以使用字符串别名按相对量调整优先级：

`critical` - **-80**

`high` - **-10**

`low` - **20**

```javascript
// 这个脚本在前面加载了
useHead({
  script: [
    {
      src: '/not-important-script.js',
    },
  ],
})

// 但是我们希望这个脚本优先加载
useHead({
  script: [
    {
      src: '/very-important-script.js',
      tagPriority: 0,
    },
  ],
})
// 渲染结果
// <script src=\"/very-important-script.js\"></script>
// <script src=\"/not-important-script.js\"></script>

```

## 通过before或者after设置优先级

可以使用 before: 或 after: 根据另一个标签的位置设置优先级。当您需要确保一个标签在另一个标签之前或之后呈现时，这很有用。

为了避免重复数据删除冲突，需要对被相对引用的标签设置key。

```javascript
useHead({
  script: [
    {
      key: 'not-important', // 这个相对script的key是必不可少的
      src: '/not-important-script.js',
    },
  ],
})
useHead({
  script: [
    {
      // 在key为not-important的script标签前面加载
      tagPriority: 'before:script:not-important',
      src: '/must-be-first-script.js',
    },
  ],
})
// <script src=\"/must-be-first-script.js\"></script>
// <script src=\"/not-important-script.js\"></script>

```



