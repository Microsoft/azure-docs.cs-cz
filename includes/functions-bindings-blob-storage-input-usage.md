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
ms.openlocfilehash: 512d05b245f1279a977ba61d5b4c4904fc0f6bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77202122"
---
Pro vazbu vstupu objektu blob můžete použít následující typy parametrů:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Vyžaduje vazbu `direction` "inout" v `FileAccess.ReadWrite` *function.json* nebo v knihovně tříd c#.

Pokud se pokusíte vytvořit vazbu na jeden z typů sady Storage SDK a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Vazba `string` `Byte[]` nebo je doporučena jenom v případě, že velikost objektu blob je malá, protože celý obsah objektu blob jsou načteny do paměti. Obecně je vhodnější použít `Stream` typ `CloudBlockBlob` nebo. Další informace naleznete v [tématu Souběžnost a využití paměti](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) dříve v tomto článku.
