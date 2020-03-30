---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77202096"
---
Můžete vytvořit vazbu na následující typy pro zápis objektů BLOB:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> Vyžaduje vazbu `direction` "in" v `FileAccess.Read` *function.json* nebo v knihovně tříd c#. Můžete však použít objekt kontejneru, který poskytuje runtime dělat operace zápisu, jako je například nahrávání objektů BLOB do kontejneru.

<sup>2</sup> Vyžaduje vazbu `direction` "inout" v `FileAccess.ReadWrite` *function.json* nebo v knihovně tříd c#.

Pokud se pokusíte vytvořit vazbu na jeden z typů sady Storage SDK a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

V asynchronních funkcích použijte `IAsyncCollector` vrácenou `out` hodnotu nebo místo parametru.

Vazba `string` `Byte[]` nebo je doporučena jenom v případě, že velikost objektu blob je malá, protože celý obsah objektu blob jsou načteny do paměti. Obecně je vhodnější použít `Stream` typ `CloudBlockBlob` nebo. Další informace naleznete v [tématu Souběžnost a využití paměti](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) dříve v tomto článku.
