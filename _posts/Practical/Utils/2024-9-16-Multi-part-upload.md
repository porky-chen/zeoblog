---
title: 大文件上传 与 批量文件上传的优化方案
author: zeo
categories: [实战篇, Utils]
tags: [JavaScript, Promise.allSettled]
render_with_liquid: false
---

# 大文件上传与批量文件上传的优化方案：使用 Promise.allSettled

在前端开发中，文件上传是一个常见的需求。尤其在处理大文件（如10GB）和多个文件的批量上传时，我们需要考虑如何进行分片上传、并发控制、错误处理等，以确保上传过程的高效与稳定。本文将介绍如何通过 Promise.allSettled 来优化上传过程，以确保部分文件上传失败时不会影响其他文件的正常上传。

## 一、背景

前端上传文件至服务器时，通常面临以下问题：
	1.文件大小限制：前端和后端对单次上传的大小有限制，因此需要进行分片处理。
	2.网络波动：网络波动可能导致上传失败，因此需要失败重试机制。
	3.传输效率：对于大文件或多个文件，单次上传会消耗较多带宽，影响上传速度和用户体验。
	4.错误处理：多个文件上传过程中，单个文件失败不应影响整体上传过程，因此需要避免上传失败时中断其他文件的上传。

## 二、需求分析
	1.大文件分片上传：将10GB文件切分为100MB的分片上传，每个分片独立上传，完成后通知服务器合并。
	2.批量文件上传：针对多个文件，设置单次上传大小不超过200MB，并确保部分上传失败不会影响其他文件。
	3.失败处理：当某个文件上传失败时，使用 Promise.allSettled 确保其他文件的上传不受影响，并单独收集失败的文件用于重试或后续处理。

## 三、实现方案与代码示例

在实现过程中，我们将通过 Promise.allSettled 来并发上传文件，确保在文件上传失败的情况下，不影响其他文件的上传。

### 1. 大文件分片上传

大文件分片上传的核心是将文件分割为固定大小的分片（如100MB），并逐个上传。Promise.allSettled 可以让我们收集每个分片的上传结果，确保即使某个分片上传失败，也不会影响其他分片的上传。上传完所有分片后再进行合并操作。

```javascript
function sliceFile(file, chunkSize = 100 * 1024 * 1024) {
    const chunks = [];
    let start = 0;
    while (start < file.size) {
        const end = Math.min(start + chunkSize, file.size);
        chunks.push(file.slice(start, end));
        start = end;
    }
    return chunks;
}

async function uploadChunkedFile(file) {
    const chunks = sliceFile(file);
    const uploadPromises = chunks.map((chunk, index) => uploadChunk(chunk, index));

    const results = await Promise.allSettled(uploadPromises);
    const failedChunks = results
        .map((result, index) => (result.status === "rejected" ? index : null))
        .filter(index => index !== null);

    if (failedChunks.length > 0) {
        console.error("Some chunks failed to upload:", failedChunks);
        // 可在此处进行失败重试或记录失败分片
    } else {
        await completeUpload(file.name); // 通知服务器合并分片
        console.log("File upload complete");
    }
}

async function uploadChunk(chunk, index) {
    const formData = new FormData();
    formData.append("chunk", chunk);
    formData.append("index", index);
    await fetch("/upload-chunk", {
        method: "POST",
        body: formData,
    });
}
```
说明：
	•sliceFile 函数用于将大文件分片。
	•uploadChunkedFile 函数调用 Promise.allSettled 并发上传所有分片，并在上传完成后检查失败的分片进行重试。
	•uploadChunk 函数处理单个分片的上传，前端发送分片数据及索引至服务器。

### 2. 批量文件上传控制大小

在批量上传场景中，我们将文件分批上传，且每次上传的文件总大小不超过200MB，并通过 Promise.allSettled 避免单个文件的失败影响其他文件。
```javascript
function batchFiles(files, maxSize = 200 * 1024 * 1024) {
    let batch = [], currentSize = 0;
    const batches = [];

    files.forEach(file => {
        if (currentSize + file.size > maxSize) {
            batches.push(batch);
            batch = [];
            currentSize = 0;
        }
        batch.push(file);
        currentSize += file.size;
    });
    if (batch.length) batches.push(batch);
    return batches;
}

async function uploadFilesBatch(files) {
    const batches = batchFiles(files);
    
    for (const batch of batches) {
        const uploadPromises = batch.map(file => uploadFileWithRetry(file));
        const results = await Promise.allSettled(uploadPromises);

        results.forEach((result, index) => {
            if (result.status === "rejected") {
                console.error(`File ${batch[index].name} failed:`, result.reason);
                // 可以进一步收集失败文件并重试
            }
        });
    }
}

async function uploadFileWithRetry(file, retries = 3) {
    while (retries > 0) {
        try {
            await uploadFile(file);
            console.log(`File ${file.name} uploaded successfully`);
            break;
        } catch (error) {
            retries--;
            console.warn(`Retry for file ${file.name}, retries left: ${retries}`);
            if (retries === 0) throw error;
        }
    }
}

async function uploadFile(file) {
    const formData = new FormData();
    formData.append("file", file);

    await fetch("/upload", {
        method: "POST",
        body: formData,
    });
}
```
说明：
	•batchFiles 函数按总大小限制将文件分成多个批次。
	•uploadFilesBatch 使用 Promise.allSettled 来保证即使批次中的某个文件上传失败，也不会影响其他文件的上传。
	•uploadFileWithRetry 提供文件上传的重试机制，确保最大限度提升成功率。

## 四、优缺点分析

### 优点：
	•分片上传：有效控制每次请求的大小，避免一次性上传大文件带来的带宽和性能瓶颈。
	•批量控制：通过限制单批上传文件总大小，减少并发请求数，降低带宽消耗。
	•错误隔离：使用 Promise.allSettled 隔离失败文件，确保单个文件失败不影响其他文件的上传。

### 缺点：
	•请求数量增加：分片上传和批次上传增加了请求数量，增加了服务器负担。
	•复杂性提高：实现过程中需要协调分片和批次上传的逻辑，且实现重试机制会增加代码复杂度。

## 五、性能开销
	•内存和带宽：分片上传对客户端和服务器带宽、内存有较大消耗，需要合理控制分片大小和批次上传数。
	•服务器负载：并发上传文件过多会导致服务器负载增加，可以通过合理的分片和批次控制来缓解服务器压力。
	•响应时间：由于 Promise.allSettled 等待所有请求完成，可能延长响应时间，但对文件上传的可靠性提升明显。

## 总结

通过使用 Promise.allSettled，我们可以在文件上传场景中，隔离失败文件并单独处理，保证即使在部分文件上传失败的情况下，其他文件也能正常完成上传。结合分片上传和批量控制等技术手段，我们能够有效提升文件上传的效率与稳定性。这种方案适用于大文件上传、批量文件上传等复杂的上传场景。