---
title: 理解浏览器的缓存机制
author: zeo
categories: [知识点, 浏览器&网络]
tags: [强缓, 协商缓存, 缓存机制, cache-control]
render_with_liquid: false
---

浏览器缓存机制是指浏览器在本地存储一部分网页资源（如图片、CSS、JavaScript 文件等），以减少对服务器的重复请求，从而提升页面加载速度、减少带宽消耗并提高用户体验。理解和合理利用缓存机制是前端开发中提高性能的关键。

  

## **一、缓存的基本概念**

  

当用户第一次访问网页时，浏览器会请求服务器获取页面资源，并将这些资源临时保存到本地存储，即缓存中。下一次访问时，浏览器可以直接从缓存中加载这些资源，从而避免重新请求服务器，提升页面的加载效率。

  

## **二、浏览器缓存的类型**

  

浏览器缓存主要分为**强缓存**和**协商缓存**两种机制。

  

### **1. 强缓存**

  

**概念**：强缓存（也称为本地缓存）允许浏览器在一定时间内直接从缓存中获取资源，而无需向服务器发送请求。这种机制通过资源的 Cache-Control 或 Expires 响应头实现。

  

- **Expires**：设置资源过期时间的绝对时间，格式如 Expires: Wed, 21 Oct 2025 07:28:00 GMT。当超过这个时间点后，资源失效，浏览器需要重新请求服务器。

- **Cache-Control**：现代浏览器更推荐使用的缓存控制方式，包含多种指令，如 max-age、no-cache 等。max-age 表示资源在多少秒内有效，如 Cache-Control: max-age=3600 表示资源在 1 小时内有效。

  

当强缓存有效时，浏览器不会向服务器发送请求，而是直接使用本地缓存。

  

### **2. 协商缓存**

  

**概念**：协商缓存是指在资源的缓存时间过期后，浏览器仍然会携带缓存标识（如 Last-Modified 或 ETag）向服务器发送请求，询问资源是否更新。如果资源未发生变化，服务器会返回状态码 304 Not Modified，浏览器会继续使用缓存；若资源已更新，服务器会返回新的资源内容。

  

- **Last-Modified 和 If-Modified-Since**：Last-Modified 表示资源的最后修改时间，浏览器在请求时使用 If-Modified-Since 报头传递上次缓存的 Last-Modified 值给服务器。服务器比较资源的修改时间，如果没有更改，则返回 304 状态码。

- **ETag 和 If-None-Match**：ETag 是资源的唯一标识符，类似资源的版本号。浏览器在请求时使用 If-None-Match 传递 ETag 值，服务器通过对比标识符决定是否返回 304 状态码或新的资源。

  

## **三、缓存机制的优先级**

  

- 浏览器会首先检查是否可以使用强缓存。如果缓存未过期且 Cache-Control 允许，则直接使用缓存。

- 如果强缓存过期或未命中，则进入协商缓存流程，向服务器发送请求确认资源是否更新。

- 如果协商缓存命中（资源未更新），则使用缓存内容；如果资源已更新或未命中，则服务器会返回新的资源。

  

## **四、缓存机制的常用策略**

  

不同的缓存策略适用于不同的场景，合理使用这些策略可以显著提升网站性能。

  

1. **静态资源长时间缓存**：对于不常更改的静态资源（如图片、CSS 文件），可以设置较长的 max-age，同时使用**文件指纹**（如哈希值）来区分版本，以便资源更新后可以重新加载。

2. **频繁更新资源的协商缓存**：对于经常更新的资源，使用协商缓存，可以保证在资源未更改时使用缓存，避免带宽浪费。

3. **缓存清除**：如果某些资源不需要缓存，可以使用 Cache-Control: no-cache, no-store, must-revalidate 或 Cache-Control: max-age=0 立即使缓存失效，确保每次请求都获取最新内容。

  

## **五、缓存的优缺点**

  

**优点**

  

- **减少服务器请求次数**，减轻服务器负担。

- **提升用户体验**，通过减少资源加载时间，加快页面响应速度。

- **节省带宽**，尤其对图片、视频等大文件资源，缓存可以显著降低网络传输量。

  

**缺点**

  

- **缓存更新问题**：当资源内容更新时，旧的缓存可能导致页面显示过时内容。

- **增加存储空间**：缓存占用用户的本地存储空间，缓存过多可能导致存储不足。

  

## **六、总结**

  

浏览器缓存机制通过强缓存和协商缓存的方式，加速页面加载，提升用户体验。理解并灵活应用这些机制，是优化前端性能的重要一环。合理的缓存策略不仅能减少服务器压力，还能为用户提供更顺畅的网页加载体验。在实际项目中，开发者需要根据资源的特性和更新频率，选择合适的缓存策略，平衡缓存的有效性与时效性。